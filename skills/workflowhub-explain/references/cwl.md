# Reading CWL workflows

Common Workflow Language files are YAML (sometimes JSON) with `cwlVersion`
(such as `v1.2`) and `class: Workflow`. Individual tools are
`class: CommandLineTool`.

## Workflow structure

- `inputs`: each has a type, and often `label`, `doc`, `format` (frequently
  an EDAM identifier), `default` and `secondaryFiles` (companion files such
  as an index that must sit next to the main file).
- `outputs`: each has an `outputSource` naming the step output it comes from.
- `steps`: each has:
  - `run`: the tool or subworkflow it runs, either a path to another `.cwl`
    file or an embedded definition;
  - `in`: where each input comes from (a workflow input or another step's
    output);
  - `out`: the outputs it produces;
  - optionally `scatter` (run once per item in a list) and `when` (run only
    if a condition holds, from CWL v1.2).

Follow `in` sources to work out the order of steps.

## Reading it without loading it all

Keep the file in the scratch folder. First find where the top-level sections
start, then read each section by line range:

```bash
grep -nE '^(cwlVersion|class|requirements|inputs|outputs|steps|\$graph):' workflow.cwl
grep -nE '^\s+run:' workflow.cwl      # the tool or subworkflow each step runs
```

Read `inputs` and `outputs` next, then list the step names under `steps`
with their `run` targets before reading any step in full. Open a `run` file
(another `.cwl`) only for the steps the explanation needs. Tested 2026-11-09
on workflow 360 (264 lines). For a packed file, list the `$graph` entries'
`id` and `class` first and read only `#main` before the tools it uses.

## Types

`File`, `Directory`, `string`, `int`, `boolean`, enums and records. A
trailing `?` means optional (`File?`); a trailing `[]` means a list
(`File[]`).

## Requirements and hints

- `DockerRequirement` (`dockerPull`): the container image a tool uses.
- `SoftwareRequirement`: named software packages and versions.
- `ResourceRequirement`: cores and memory (`coresMin`, `ramMin`).
- Feature requirements such as `ScatterFeatureRequirement` or
  `SubworkflowFeatureRequirement` matter to the engine, not usually to the
  researcher.

## Packed workflows

A packed file has a `$graph` list containing every workflow and tool. The
entry point is usually the entity with `id: main` (or `#main`).

## Abstract CWL

WorkflowHub sometimes holds an abstract CWL description of a workflow
written in another system, such as Galaxy. It shows structure (inputs, steps,
outputs) but doesn't contain runnable tool definitions. If that's all you
have, explain the structure and say that tool details come from the original
workflow.