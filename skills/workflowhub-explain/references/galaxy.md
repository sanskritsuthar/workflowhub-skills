# Galaxy workflows

How to fill the workflow profile (`workflow-profile.md`) from a Galaxy
workflow. Tested 2026-11-09 on workflows 223, 248, 1594 and 1612 unless
noted.

## What's in a Galaxy crate

- **The workflow file.** Usually native format, `.ga`: JSON with a top-level
  `"a_galaxy_workflow"` key. Format 2 (`.gxwf.yml`, YAML with
  `class: GalaxyWorkflow`) also exists; none of the tested crates used it, so
  the commands below are for `.ga` only.
- **Always there, not needed:** `ro-crate-preview.html`, a page generated
  from the crate metadata for people to read. It says what the metadata
  already says.
- **A diagram,** as `.png` (105 to 360 KB) or `.svg`, in all six crates
  tested; the crate may link it as the main workflow's `image` (1612). Don't
  read a generated one for facts: it draws the same graph as the `.ga`. Do
  use it in three ways: point the researcher to it rather than drawing your
  own; take stage names from one the authors drew (248's 12 KB SVG labels
  its stages, and an SVG's labels are text in `<text>` elements); and look
  at it when the `.ga` can't be read.
- **Sometimes there, worth reading:** `README.md`, `CHANGELOG.md`, a test
  file `<name>-tests.yml` and `test-data/` (248, a community-maintained
  workflow).
- **Not there:** the tools' own definitions (Galaxy tools are XML files kept
  in the ToolShed, not in the workflow). The workflow names each tool by ID.

The TRS request (`tools.md`) returns the `.ga` file alone, without the ZIP.

## What takes the space, and what to skip

A `.ga` file is 9 to 88 KB. Of that, 22 to 46% is indentation, and 3 to 10%
is the visual editor's layout (`position`, `uuid`, `errors`, `comments`).
Most of the rest is each step's settings (`tool_state`), embedded
subworkflows, rules that rename or hide outputs (`post_job_actions`) and
ToolShed coordinates (`tool_shed_repository`, repeated in `tool_id`). The
commands below skip all of it and read settings only where a question needs
them.

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

Run these on the saved `.ga` file, in order. Tool names come from
`tool_id`; built-in tools (such as `Cut1` in 1594) have no ToolShed path.

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
  | [$t | paths(scalars) as $p | {k: ($p | map(tostring) | join(".")), v: ($t | getpath($p))}]
  | .[] | select((.k | test("__[a-z_]+__$")) | not)
  | select(.v == "cached" or .v == "indexed"
           or (.k | test("ref_file|dbkey|genome|database|builtin|\\.index$"; "i")))
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

Level 4, when the question is about one step: its settings with
`jq '.steps["<id>"].tool_state | fromjson'`, or a subworkflow through
`.steps["<id>"].subworkflow`, to which every command above also applies.
The reference and run-time commands don't look inside subworkflows; run
them on each subworkflow too when requirements matter.

What the tested workflows showed: 1612 needs `hg38` (minimap2) and
`hg38full` (bowtie2) on the server, and leaves two unicycler options open;
1594 needs the BLAST database `refseq_mitochondrion`; 248 needs no server
reference and leaves two optional inputs of its simulation steps open; 223
needs neither.

## Requirements to look out for

- **Reference data held on the server** is the commonest reason a Galaxy
  workflow fails on a different server, and it is visible only in step
  settings. Run the reference command whenever requirements matter,
  including during an evaluation.
- **Tools:** every tool must be installed on the researcher's server, at a
  compatible version. The tool list lets them check.

## Explaining it

- A tool ID such as `.../repos/iuc/fastp/fastp/0.23.4+galaxy0` names the tool
  in its second-to-last part. Prefer the author's step `label` and
  `annotation` when describing a step.
- Explain collections in plain terms: `list:paired` is "a set of paired-end
  samples, uploaded together".
- Explain a subworkflow as one stage, and expand it only if asked.
- A tests file shows example inputs and expected outputs, and allows a safe
  first run. Mention it.
