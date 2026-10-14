# Reading Nextflow workflows

Nextflow pipelines are usually a repository of files rather than a single
file. Read them in this order; the first two often answer most questions
without opening the code.

1. **`README.md`:** the pipeline summary, usage and quick-start command.
2. **`nextflow_schema.json`** (if present): every parameter, with
   descriptions, defaults and groupings. The best single source for inputs
   and key parameters.
3. **`nextflow.config`:** `params { }` defaults, `profiles` (such as
   `docker`, `singularity`, `conda`, `test`), resource settings, and a
   `manifest` with the pipeline's name, version and required Nextflow
   version.
4. **`main.nf`:** the entry point. In modern (DSL2) pipelines it mostly
   imports and calls named workflows, often found in `workflows/`.
5. **`subworkflows/` and `modules/`:** the building blocks. Each `process`
   has `input:`, `output:` and `script:` sections, plus `container` or
   `conda` directives naming its software.

Don't read every module. List the processes in the order the workflow calls
them, and name the tool each one runs.

## nf-core conventions

Pipelines following nf-core conventions add:

- `assets/schema_input.json`: the columns of the input sample sheet.
- `docs/usage.md` and `docs/output.md`: how to run it and what each output
  means.
- `conf/test.config`: a `test` profile with small data, good for a first run.
- `CITATIONS.md`: the tools the pipeline uses that should be cited. Point
  this out; workflowhub-cite will need it.

## Inputs and outputs

Inputs are commonly a sample sheet (`--input samplesheet.csv`) listing files
and sample information, plus reference parameters such as `--fasta` or
`--genome`. Outputs go to the directory given by `--outdir`. When mapping the
researcher's data, the sample sheet's required columns are usually the main
task.

## Requirements

Note the required Nextflow version, the container or environment profiles
available, reference data, and any compute expectations given in
configuration or documentation.