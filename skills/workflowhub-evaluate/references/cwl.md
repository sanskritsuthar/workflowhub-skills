# CWL workflows

How to fill the workflow profile (`workflow-profile.md`) from a Common
Workflow Language workflow. Tested 2026-11-09 on workflows 1765 (with its
tool files) and 2367 (main file only) unless noted.

## What's in a CWL crate

- **The main workflow file:** YAML (sometimes JSON) with `cwlVersion` and
  `class: Workflow`. The TRS request (`tools.md`) returns it alone.
- **Tool and subworkflow files:** each step's `run` points to another
  `.cwl` file (`class: CommandLineTool` or `Workflow`). 1765 has 43. They
  are only in the ZIP.
- **Often:** test job files giving input values (1765:
  `tiny_test/test_main.chip.yml`), test data, and sometimes reference files
  such as index files (1765 ships `.bt2` files).
- **Size varies widely:** 78 KB (2347), 686 KB (1765), 61 MB (2367), 204 MB
  (360 and 361). Check the size first; above the limit, the main file alone
  still gives levels 1 and 2 except software.
- **Packed files** hold everything in one `$graph` list; the entry point is
  the entry with `id: main` (or `#main`). Not tested.
- **Abstract CWL** describes a workflow written in another system, such as
  Galaxy: structure only, no runnable tools. Say so if that's all there is.

## Filling the profile

| Profile field | Where it is | Level |
|---|---|---|
| `provides` | the main file's `inputs`: name, `type` (`?` optional, `[]` a list), `default`, `format` (often an EDAM term), `secondaryFiles`, `doc`. `File` and `Directory` are data; other types are settings | 2 (names, types), 3 (docs, defaults) |
| `outputs` | `outputs`: name, `type`, `format`, `outputSource` | 2 |
| `steps.count`, `steps.list` | `steps`: names, `run` targets, `in` sources | 2, 3 |
| `requirements.software` | `DockerRequirement` (`dockerPull`) and `SoftwareRequirement` in the tool files, under `requirements` or `hints` | 2 (needs the ZIP) |
| `requirements.reference_data` | `File` inputs with `secondaryFiles` (an index that sits beside the file), and inputs named for genomes, indexes or databases | 2 |
| `requirements.platform` | `cwlVersion`, a CWL runner, and a container engine if Docker images are named | 2 |
| `requirements.compute` | `ResourceRequirement` (`coresMin`, `ramMin`) in tool files | 3 |
| `docs.tests` | job files (`.yml` with input values) and test data | 2 |

## Commands

For block-style YAML with two-space indentation, the usual layout. For JSON
CWL or other layouts, find the sections and read them by line range.

```bash
# Where the top-level sections start
grep -nE '^(cwlVersion|class|requirements|hints|inputs|outputs|steps|\$graph):' workflow.cwl

# Level 2: inputs, with their type, default, format and secondary files
# (1765: 14 inputs, a 4.7 KB section, in 730 bytes)
awk '/^inputs:/{f=1;next} /^[a-z$]/{f=0} f' workflow.cwl \
  | grep -E '^  [A-Za-z0-9_-]+:|^    (type|default|format|secondaryFiles):'

# Level 2: outputs, the same way
awk '/^outputs:/{f=1;next} /^[a-z$]/{f=0} f' workflow.cwl \
  | grep -E '^  [A-Za-z0-9_-]+:|^    (type|format|outputSource):'

# Level 2: steps and the files they run
grep -nE '^\s+run:' workflow.cwl

# Level 2, with the ZIP extracted: container images across all tool files
grep -rhoE 'dockerPull: *[^ ]+' --include=*.cwl . | sort | uniq -c | sort -rn

# Level 2: test job files
find . -name '*.yml' -o -name '*.yaml'
```

An input whose `type:` line is empty has a nested type (an array or a
record); read that input's lines. Level 3 adds each input's `doc`, read by
line range, and each step's `in` sources. Level 4 is one tool file's
`baseCommand` and `arguments`.

What 1765 showed: 7 steps running tool files under `workflow_modules/`; a
`genome` input with `secondaryFiles` (a Bowtie2 index) and a
`genome_spike_in` input, both reference data; Docker images for samtools,
bedtools, deeptools, Trim Galore and MultiQC; a test job in `tiny_test/`.

## Requirements to look out for

- **Reference data:** look for `secondaryFiles` and for input names such as
  `genome`, `index`, `reference` or `database`. The workflow won't say where
  to get them; the README or test job may.
- **Containers:** most CWL tools name a Docker image. The researcher needs
  Docker, Singularity or another engine their CWL runner supports.

## Explaining it

- Follow the `in` sources to work out the order of steps.
- `scatter` runs a step once per item in a list; `when` (CWL 1.2) runs it
  only if a condition holds.
- Feature requirements such as `ScatterFeatureRequirement` matter to the
  runner, not usually to the researcher.
