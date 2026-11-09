# Galaxy workflows

This guide tells you how to fill the workflow profile (`workflow-profile.md`)
from a Galaxy workflow. We tested it on 2026-11-09 on workflows 223, 248,
1594 and 1612, unless the text tells otherwise.

## What's in a Galaxy crate

- **The workflow file.** It is usually in the native format, `.ga`: JSON
  with a top-level `"a_galaxy_workflow"` key. Format 2 (`.gxwf.yml`, YAML
  with `class: GalaxyWorkflow`) also exists. None of the tested crates used
  it, so the commands below are for `.ga` only.
- **Always there, not necessary:** `ro-crate-preview.html`. It is a page for
  people, made from the crate metadata. It gives the same facts as the
  metadata.
- **A diagram,** as `.png` (105 to 360 KB) or `.svg`. All six tested crates
  had one. The crate can link it as the `image` of the main workflow (1612).
  A diagram that a tool made shows the same graph as the `.ga`, so do not
  read it for facts. Use a diagram in these three ways:
  - Refer the researcher to it. Do not draw your own diagram.
  - Take stage names from a diagram that the authors drew. The 12 KB SVG of
    248 names its stages. The labels of an SVG are text in `<text>`
    elements.
  - Look at it when you cannot read the `.ga`.
- **Sometimes there, useful to read:** `README.md`, `CHANGELOG.md`, a test
  file `<name>-tests.yml` and `test-data/` (248, a workflow that a community
  maintains).
- **Not there:** the definitions of the tools. Galaxy tools are XML files in
  the ToolShed, not in the workflow. The workflow names each tool by its ID.

The TRS request (`tools.md`) gives the `.ga` file without the ZIP.

## What takes the space, and what to skip

A `.ga` file is 9 to 160 KB (160 KB for 401). Of that, 22 to 46% is
indentation. 3 to 10% is the layout of the visual editor (`position`,
`uuid`, `errors`, `comments`). Most of the remaining part is:

- the settings of each step (`tool_state`);
- embedded subworkflows;
- rules that change the names of outputs or hide them (`post_job_actions`);
- ToolShed coordinates (`tool_shed_repository`, the same as in `tool_id`).

The commands below skip all of this. They read settings only when a
question needs them.

## Filling the profile

| Profile field | Where it is in a `.ga` file | Level |
|---|---|---|
| `provides`, data | steps of type `data_input` or `data_collection_input`: `label`, `annotation`, and in their settings `collection_type` and `optional` | 2 |
| `provides`, settings | steps of type `parameter_input` (settings: `parameter_type`, `optional`) | 2 |
| `provides`, settings left open | tool settings marked `RuntimeValue` and not wired to another step | 3 |
| `outputs` | each step's `workflow_outputs`: `label`, or the step and output name when unlabelled | 2 |
| `steps.count`, `steps.list` | steps of type `tool` or `subworkflow`; `tool_id`, `label`, `input_connections` | 2, 3 |
| `requirements.software` | every `tool_id` (a ToolShed path ending in name and version): each must be installed on the Galaxy server | 2 |
| `requirements.reference_data` | settings that pick a reference held on the server (`cached`, `indexed`, `dbkey`, `database`) | 2 |
| `requirements.platform` | a Galaxy server with those tools and references | 2 |
| `docs.tests` | `<name>-tests.yml` and `test-data/` beside the `.ga` | 2 |
| Level 1 fallbacks | top level: `name`, `annotation` (description), `license`, `creator`, `release` | 1 |

## Commands

Run these commands on the saved `.ga` file, in this order. The tool names
come from `tool_id`. Built-in tools (for example `Cut1` in 1594) have no
ToolShed path.

```bash
# Level 2: inputs, with kind, collection type, value type and whether optional
jq -c '[.steps[] | select(.type | test("input")) | {label, type, annotation,
  settings: (.tool_state | fromjson? | {format, collection_type, parameter_type, optional}
             | with_entries(select(.value != null)))}]' workflow.ga

# Level 2: outputs; unlabelled ones (all 18 in 1612) by step and output name
jq -c '[.steps[] | . as $s | .workflow_outputs[]? | {label, step: $s.id, output: .output_name}]' workflow.ga

# Level 2: number of steps and the tools they need
jq -c '{steps: ([.steps[] | select(.type == "tool" or .type == "subworkflow")] | length),
        subworkflows: ([.steps[] | select(.type == "subworkflow")] | length),
        tools: ([.steps[] | select(.type == "tool") | .tool_id] | unique)}' workflow.ga

# Level 2: reference data held on the Galaxy server. Some settings are JSON
# stored as text inside the JSON, so a plain text search misses them.
jq -r 'def deep: if type == "string" and test("^[\\[{]") then (fromjson? // .) | deep
                 elif type == "object" then map_values(deep)
                 elif type == "array" then map(deep) else . end;
  def toolname: .tool_id // "" | split("/") | if length > 1 then .[-2] else .[0] end;
  .steps[] | select(.type == "tool") | . as $s | (.tool_state | deep) as $t
  | [$t | paths(scalars) as $p
     | {k: ($p | map(tostring) | join(".")), name: ([$p[] | strings] | last // ""), v: ($t | getpath($p))}]
  | .[] | select(.name | test("^__[a-z_]+__$") | not)
  | select(.v == "cached" or .v == "indexed"
           or (.name | test("ref_file|dbkey|genome|database|builtin|^index$"; "i")))
  | "\($s.id)\t\($s | toolname)\t\(.k) = \(.v)"' workflow.ga

# Level 3: steps, one line each, with the tool and what feeds them;
# a subworkflow as its name and step count
jq -c '.steps[] | {id, type, label, tool_id, tool_version,
  sub: (if .subworkflow then {name: .subworkflow.name,
        steps: (.subworkflow.steps | length)} else null end),
  from: [.input_connections[]? | (if type == "array" then .[] else . end) | .id] | unique}' workflow.ga

# Level 3: tool settings left for the user at run time. Galaxy marks wired
# inputs the same way, so those are removed.
jq -r 'def deep: if type == "string" and test("^[\\[{]") then (fromjson? // .) | deep
                 elif type == "object" then map_values(deep)
                 elif type == "array" then map(deep) else . end;
  def toolname: .tool_id // "" | split("/") | if length > 1 then .[-2] else .[0] end;
  .steps[] | select(.type == "tool") | . as $s
  | ([.input_connections // {} | keys[] | gsub("\\|"; ".")]) as $wired
  | (.tool_state | deep) as $t
  | [$t | paths(objects) as $p | select(($t | getpath($p) | .__class__?) == "RuntimeValue")
     | $p | map(tostring) | join(".") | select(. as $k | $wired | index($k) | not)]
  | select(length > 0) | "\($s.id)\t\($s | toolname)\t\(join(", "))"' workflow.ga
```

Level 4 is for a question about one step:

- For the settings of one step, use
  `jq '.steps["<id>"].tool_state | fromjson'`.
- For a subworkflow, use `.steps["<id>"].subworkflow`. All commands above
  also work on a subworkflow.

The reference command and the run-time command do not look inside
subworkflows. When requirements are important, save each subworkflow as a
file. Then run the commands on that file too:

```bash
jq -r '.steps[] | select(.type == "subworkflow") | .id' workflow.ga
jq '.steps["<id>"].subworkflow' workflow.ga > sub-<id>.ga
```

Results on the tested workflows:

- 1612 needs `hg38` (minimap2) and `hg38full` (bowtie2) on the server. It
  leaves two unicycler options open.
- 1594 needs the BLAST database `refseq_mitochondrion`.
- 401 (version 19) needs a STAR index for the selected genome and a cached
  genome for Cufflinks. Its four subworkflows need none.
- 248 needs no reference on the server. It leaves two optional inputs of its
  simulation steps open.
- 223 and 1201 need neither.

The reference command examines the last named part of each setting. Thus, it
does not list settings that are only under a reference heading, for example
`refGenomeSource.sjdbOverhang` of STAR.

## Requirements to look out for

- **Reference data on the server** is the most frequent reason that a Galaxy
  workflow fails on a different server. Only the step settings show it. Run
  the reference command each time that requirements are important. Do this
  also when you evaluate a workflow.
- **Tools:** each tool must be installed on the server of the researcher, at
  a compatible version. With the tool list, the researcher can examine this.

## Explaining it

- In a tool ID such as `.../repos/iuc/fastp/fastp/0.23.4+galaxy0`, the
  second-to-last part is the name of the tool. To describe a step, use the
  `label` and `annotation` of the author first.
- Explain collections in simple words. For example, `list:paired` is "a set
  of paired-end samples, uploaded together".
- Explain a subworkflow as one stage. Give its details only if the
  researcher asks.
- A tests file shows example inputs and the expected outputs. It also lets
  the researcher do a safe first run. Tell the researcher about it.
