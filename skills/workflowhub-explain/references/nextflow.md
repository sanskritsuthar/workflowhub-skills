# Nextflow workflows

This guide tells you how to fill the workflow profile (`workflow-profile.md`)
from a Nextflow pipeline. We tested it on 2026-11-09 on workflow 1266 (an
nf-core pipeline, 147 files, ZIP 375 KB), unless the text tells otherwise.

## What's in a Nextflow crate

A Nextflow crate usually holds the full repository of the pipeline. The TRS
request (`tools.md`) gives only `main.nf`. In modern pipelines, `main.nf`
mostly imports other files. Thus, most of the profile needs the ZIP.

- **Read:** `README.md`, `nextflow_schema.json` (all parameters),
  `nextflow.config` (defaults, profiles, the `manifest`), `main.nf`,
  `workflows/*.nf` (the order of the stages), `modules/` and
  `subworkflows/` (one process each), `conf/` (resources, test profiles),
  `docs/usage.md` and `docs/output.md`, `CITATIONS.md`.
- **nf-core pipelines also have:** `assets/schema_input.json` (the columns
  of the sample sheet), `conf/test.config`, `tests/` (nf-test).
- **Skip:** `.github/`, `.pre-commit-config.yaml`, `modules.json`,
  `CODE_OF_CONDUCT.md`, `assets/multiqc_config.yml`, and the nf-core
  utility subworkflows (`subworkflows/nf-core/utils_*`). These files are for
  the management of the project.

## Filling the profile

| Profile field | Where it is | Level |
|---|---|---|
| `provides` | `nextflow_schema.json`: parameters by group, which are required, which take files or folders (`format` containing `path`: data); the sample sheet's columns in `assets/schema_input.json` | 2 |
| `outputs` | `docs/output.md` (its headings); all results go to `--outdir` | 2 |
| `steps.count`, `steps.list` | processes in `modules/`; the order they're called in `workflows/*.nf` | 2, 3 |
| `requirements.software` | `container` and `conda` lines in each module; profiles such as `docker`, `singularity`, `conda` in `nextflow.config` | 2 |
| `requirements.reference_data` | schema parameters for references (`--genome`, `--fasta`, databases), often in a reference group | 2 |
| `requirements.platform` | `nextflowVersion` in the `manifest` block of `nextflow.config`; profiles for executors and container engines | 2 |
| `requirements.compute` | process labels (`process_high`, `process_gpu`) and `conf/base.config` | 2 |
| `docs.tests` | `conf/test.config` (a `test` profile with small data), `tests/` | 2 |

## Commands

Run these commands on the extracted crate:

```bash
# Level 2: parameter groups and which parameters are required
jq -c '(.["$defs"] // .definitions) | to_entries
  | map({group: .key, n: (.value.properties | length), required: (.value.required // [])})' nextflow_schema.json

# Level 2: parameters that take files or folders, with defaults
jq -r '(.["$defs"] // .definitions) | .[].properties | to_entries[]
  | select(.value.format? | tostring | test("path"))
  | "\(.key)\t\(.value.format)\t\(.value.default // "")"' nextflow_schema.json

# Level 2: the sample sheet's columns, if there is one
jq -c '.items | {required, columns: (.properties | to_entries
  | map({(.key): (.value.format // .value.type)}) | add)}' assets/schema_input.json

# Level 2: documented outputs
grep -nE '^#{2,3} ' docs/output.md

# Level 2: processes, software and compute labels
grep -rnE '^\s*process\s+\w+' modules/ subworkflows/
grep -rnE '^\s*(container|conda)\b' modules/
grep -rhoE "label +'process_[a-z_]+'" modules/ | sort | uniq -c

# Level 2: required Nextflow version and profiles
grep -nE 'nextflowVersion' nextflow.config
awk '/^profiles *\{/{f=1;next} f && /^\}/{f=0} f && /^    [A-Za-z_]+ *\{/' nextflow.config | sed 's/ *{.*//'

# Level 3: the stages in the order they run
grep -nE '^\s*(include|workflow)\b' main.nf
grep -nE '^\s+[A-Z_]{3,}\s*\(' workflows/*.nf
```

Level 4 is the `script:` block of a module, or its `publishDir` settings in
`conf/modules.config`. Read them when the question is about the command or
the output files of that step.

Results for 1266:

- 41 parameters in 9 groups;
- a sample sheet that needs `sample` and `fastq_1`;
- 21 processes, of which 6 have the label `process_gpu`;
- Nextflow `>=26.04.0`;
- profiles for conda, docker, singularity, apptainer and others, and also
  `test` and `test_full`;
- three stages (`PREPROCESS_CUSTOM`, `RUN_CV`, `MODEL_TESTING`);
- nf-test tests.

## Requirements to look out for

- **GPUs and large resources:** the labels `process_gpu` or `process_high`
  tell you that the researcher needs applicable hardware, or must change the
  configuration.
- **Reference data:** `--genome` keys refer to a reference collection
  (iGenomes) that the pipeline configures. `--fasta` and similar parameters
  take files from the researcher.
- **Container engine:** the profiles show which engines the pipeline
  supports.

## Explaining it

- Inputs are usually a sample sheet (`--input samplesheet.csv`) and
  reference parameters. To compare the data of the researcher with the
  inputs, start with the required columns of the sample sheet.
- `CITATIONS.md` lists the tools to cite. workflowhub-cite needs it.
- Do not read each module. List the processes in the order in which the
  workflow calls them. Give the tool that each process runs.
