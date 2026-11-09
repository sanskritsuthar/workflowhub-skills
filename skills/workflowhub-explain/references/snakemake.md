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

## Reading it without loading it all

Keep the files in the scratch folder and outline them before reading rules:

```bash
grep -nE '^(rule|include|configfile|min_version|module|use rule)\b' Snakefile
grep -nE '^rule\b' rules/*.smk                                     # rule names
grep -nE '^rule\b|^\s+(input|output|conda|container|wrapper):' rules/<file>.smk
```

The first line lists the rule files it pulls in. The second lists the rules:
enough for a summary (level 2). The third gives one rule file's rules with
their inputs, outputs and software, one line each; run it per file, for the
stages you are explaining, because across a large workflow it adds up.
Workflow 1815 (tested 2026-11-09): 8 rule files, 220 KB in all, 96 rules;
the names took 2.4 KB and the full directive outline 21.5 KB. For software,
list the conda environment files the rules name (1815 names one per tool,
such as `bakta.yaml`). Read a rule's full body by line range only when you
need its `shell:` or `params:`. The TRS descriptor returns only the
Snakefile, so the rule files need the crate's ZIP (3.5 MB for 1815).

## Inputs and configuration

Inputs are normally described by the config file and the sample sheet. When
mapping the researcher's data, explain which columns and settings they would
need to fill in.

## Requirements

Note the conda environments or containers, any reference data the config
points to, and the minimum Snakemake version if stated (often in the
Snakefile through `min_version`).