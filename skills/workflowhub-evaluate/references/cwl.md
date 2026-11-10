# CWL workflows

This guide tells you how to fill the workflow profile (`workflow-profile.md`)
from a Common Workflow Language workflow. We tested it on 2026-11-09 on
workflows 1765 (with its tool files) and 2367 (main file only), unless the
text tells otherwise.

## What's in a CWL crate

- **The main workflow file:** YAML (sometimes JSON) with `cwlVersion` and
  `class: Workflow`. The TRS request (`tools.md`) gives only this file.
- **Tool and subworkflow files:** the `run` of each step refers to another
  `.cwl` file (`class: CommandLineTool` or `Workflow`). 1765 has 43 of them.
  They are only in the ZIP.
- **Frequently:** test job files that give input values (1765:
  `tiny_test/test_main.chip.yml`), test data, and sometimes reference files,
  for example index files (1765 has `.bt2` files).
- **The size is very different between crates:** 78 KB (2347), 686 KB
  (1765), 61 MB (2367), 204 MB (360 and 361). Examine the size first. If the
  ZIP is larger than the limit, the main file alone still gives levels 1 and
  2. Only the software is then missing.
- **Packed files** hold all parts in one `$graph` list. The entry point is
  the entry with `id: main` (or `#main`). We did not test this.
- **Abstract CWL** describes a workflow that is written in a different
  system, for example Galaxy. It shows only the structure, with no tools that
  can run. If you have only abstract CWL, tell the researcher.

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

These commands are for YAML in block style with two-space indentation. This
is the usual layout. For JSON CWL or a different layout, find the sections
and read them by line range.

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

If the `type:` line of an input is empty, the input has a nested type (an
array or a record). Read the lines of that input. Level 3 adds the `doc` of
each input, read by line range, and the `in` sources of each step. Level 4
is the `baseCommand` and `arguments` of one tool file.

Results for 1765:

- 7 steps that run tool files in `workflow_modules/`;
- a `genome` input with `secondaryFiles` (a Bowtie2 index) and a
  `genome_spike_in` input, both reference data;
- Docker images for samtools, bedtools, deeptools, Trim Galore and MultiQC;
- a test job in `tiny_test/`.

## Requirements to look out for

- **Reference data:** look for `secondaryFiles` and for input names such as
  `genome`, `index`, `reference` or `database`. The workflow does not tell
  where to get them. The README or the test job can tell this.
- **Containers:** most CWL tools name a Docker image. The researcher needs
  Docker, Singularity or a different engine that their CWL runner supports.

## Explaining it

- To find the order of the steps, follow the `in` sources.
- `scatter` runs a step one time for each item in a list. `when` (CWL 1.2)
  runs a step only if a condition is true.
- Feature requirements, for example `ScatterFeatureRequirement`, are
  important to the runner. Usually, they are not important to the
  researcher.
