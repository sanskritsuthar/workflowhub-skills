# Reading Galaxy workflows

Galaxy workflows come in two formats:

- **Native format (`.ga`):** JSON with a top-level `"a_galaxy_workflow"`
  key. This is the most common format.
- **Format 2 (often `.gxwf.yml`):** YAML with `class: GalaxyWorkflow`, and
  sections for `inputs`, `outputs` and `steps`. Easier to read; same ideas.

## Native format essentials

Top level: `name`, `annotation` (the description), `license`, `creator`,
sometimes `release` (a version string), `tags`, and `steps`.

`steps` is a dictionary keyed by number. Each step has:

- `type`: `data_input` or `data_collection_input` (workflow inputs),
  `parameter_input` (a value the user sets at runtime), `tool`, or
  `subworkflow`.
- `label` and `annotation`: names and descriptions given by the author.
  Prefer these when describing a step.
- `tool_id` and `tool_version`: for tool steps. A tool ID is often a long
  ToolShed path ending in the tool name and version, for example
  `.../repos/iuc/fastp/fastp/0.23.4+galaxy0`. The second-to-last part is
  the tool's name.
- `input_connections`: which earlier step's output feeds each input. Follow
  these to work out the order and shape of the workflow.
- `tool_state`: the tool's parameter settings, stored as a JSON-encoded
  string. Values marked `RuntimeValue` are set by the user when running;
  values marked `ConnectedValue` come from another step.
- `workflow_outputs`: outputs the author marked as the workflow's results.
- `subworkflow`: an embedded workflow, for `subworkflow` steps. Explain it
  as a single stage, and expand only if asked.

## Reading it without loading it all

A `.ga` file is mostly `tool_state` strings, post-job actions and embedded
subworkflows; the structure you need is a small part of it (workflow 248
version 7: 88 KB file, about 4 KB of inputs, steps and outputs). Keep the
file in the scratch folder and extract, in this order (tested 2026-11-09 on
workflow 248):

```bash
# Inputs: label, kind, description
jq -c '[.steps[] | select(.type | test("input")) | {label, type, annotation}]' workflow.ga

# Steps: one line each, with the tool and where its inputs come from;
# subworkflows as a name and step count, not their full contents
jq -c '.steps[] | {id, type, label, tool_id, tool_version,
  sub: (if .subworkflow then {name: .subworkflow.name,
        steps: (.subworkflow.steps | length)} else null end),
  from: [.input_connections[]? | (if type == "array" then .[] else . end) | .id] | unique}' workflow.ga

# Outputs the author marked as results
jq -c '[.steps[] | .workflow_outputs[]? | .label]' workflow.ga
```

Then decode one step's settings only when you need its parameters:
`jq -r '.steps["<id>"].tool_state | fromjson' workflow.ga`. Expand a
subworkflow the same way, through `.steps["<id>"].subworkflow`, only if the
researcher asks about that stage.

## Inputs

For `data_input` and `data_collection_input` steps, `tool_state` usually
records allowed formats and, for collections, the collection type, such as
`list` or `list:paired` (a list of read pairs). Explain collections in plain
terms: "a set of paired-end samples, uploaded together".

## Requirements to look out for

- **Reference data held on the Galaxy server:** parameters that select a
  "cached" or "built-in" reference, such as a genome index. The researcher's
  Galaxy server must have that reference installed. Flag this; it is a common
  reason workflows fail on a different server.
- **Tools:** every tool must be installed on the server the researcher uses.
  Listing the tools helps them check.

## Tests

Community-maintained Galaxy workflows often ship a test file (for example
`<name>-tests.yml`) with test data. Mention it: it shows the expected inputs
and outputs, and it allows a safe first run.