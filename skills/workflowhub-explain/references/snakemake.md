# Reading Snakemake workflows

Snakemake workflows are built from rules, each describing how to make output
files from input files.

## Where things are

Many workflows follow the standard layout:

- `workflow/Snakefile`: the entry point (sometimes just `Snakefile` at the
  top level), which `include:`s rule files.
- `workflow/rules/*.smk`: the rules.
- `workflow/envs/*.yaml`: conda environments, one per tool group.
- `workflow/scripts/`: scripts called by rules.
- `config/config.yaml` and `config/README.md`: settings, with documentation.
- `config/samples.tsv` (or similar): the sample sheet.
- `.test/`: a small test setup, good for a first run.

## Reading rules

Each `rule` has some of:

- `input:` and `output:`: file patterns, often with wildcards such as
  `{sample}`, meaning the rule runs once per sample.
- `params:`, `threads:`, `resources:`: settings and compute needs.
- `conda:` or `container:`: the software environment.
- `shell:`, `script:` or `wrapper:`: what actually runs. A wrapper path such
  as `v3.0.0/bio/bwa/mem` names the tool directly.

`rule all` usually lists the final targets, so it tells you what the
workflow ultimately produces. Work backwards from those targets to explain
the order of steps.

## Inputs and configuration

Inputs are normally described by the config file and the sample sheet. When
mapping the researcher's data, explain which columns and settings they would
need to fill in.

## Requirements

Note the conda environments or containers, any reference data the config
points to, and the minimum Snakemake version if stated (often in the
Snakefile through `min_version`).