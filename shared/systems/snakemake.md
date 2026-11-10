# Snakemake workflows

How to fill the workflow profile (`workflow-profile.md`) from a Snakemake
workflow. Tested 2026-11-09 on workflow 1815 (117 files, ZIP 3.5 MB) unless
noted.

## What's in a Snakemake crate

A Snakemake crate is usually the whole repository. The TRS request
(`tools.md`) returns only the Snakefile, which mostly `include:`s rule files,
so most of the profile needs the ZIP. The crate metadata may list far fewer
files than the ZIP holds (1815: 2 listed, 117 present).

- **Read:** the Snakefile (`workflow/Snakefile`, `Snakefile`, or in a
  subfolder: `magneto/Snakefile` in 1815), its rule files (`rules/*.smk`),
  the config file and its documentation, the sample sheet, `envs/*.yaml`,
  the README.
- **Often:** a `.test/` folder with a small setup; cluster profiles (1815
  has `.slurm/` and `.sge/`); a config schema under `schemas/`.
- **Skip until level 4:** `scripts/` and the `shell:` bodies of rules.

## Filling the profile

| Profile field | Where it is | Level |
|---|---|---|
| `provides` | the config file (named by `configfile:`, or `config/config.yaml`; 1815 uses `.configtemplate.yaml`): its top-level keys; the sample sheet; a config schema, if any | 2 |
| `outputs` | the rules defined in the Snakefile itself: `rule all`, or several entry rules the user picks (1815: `run_QC`, `run_assembly` and four more); their `input:` lists the final files | 2 |
| `steps.count`, `steps.list` | rules in the rule files (names for level 2; `input`, `output` and software per file for level 3) | 2, 3 |
| `requirements.software` | `conda:` environment files, `container:`, `wrapper:` (a wrapper path such as `v3.0.0/bio/bwa/mem` names the tool) | 2 |
| `requirements.reference_data` | config keys naming databases or references, and rules that download them (1815: `rules/databases.smk`) | 2 |
| `requirements.platform` | `min_version` in the Snakefile; cluster profiles | 2 |
| `requirements.compute` | `threads:` and `resources:` in rules; cluster profile settings | 3 |
| `docs.tests` | `.test/` | 2 |

## Commands

On the extracted crate, from the folder holding the Snakefile:

```bash
# Level 2: what the Snakefile pulls in, its config, version and its own rules
grep -nE '^(rule|include|configfile|min_version|module|use rule)\b' Snakefile

# Level 2: the rules in each file, names only
# (1815: 96 rules, 2.4 KB; the full outline below is 21.5 KB)
grep -nE '^rule\b' rules/*.smk

# Level 2: the config file's top-level keys
grep -nE '^[A-Za-z_]+:' config.yaml

# Level 2: software environments
ls envs/

# Level 3: one rule file's rules with inputs, outputs and software
grep -nE '^rule\b|^\s+(input|output|conda|container|wrapper):' rules/<file>.smk
```

Run the level 3 command per rule file, for the stages you need, because
across a large workflow it adds up. Level 4: a rule's full body, read by line
range, when you need its `shell:`, `params:` or script.

What 1815 showed: six entry rules in the Snakefile; 8 rule files, including
one that downloads databases; 66 files in `envs/` (a YAML file and a pinned
lock file per tool); a config template whose top-level keys include
`Assembly`, `co_assembly` and `contaminant`; Slurm and SGE profiles; no
`.test/`.

## Requirements to look out for

- **Databases:** many workflows download large databases on first run, in
  their own rules. Note their size if the README gives it.
- **Conda or containers:** the researcher needs conda (or mamba) or a
  container engine, as the rules require.
- **Cluster use:** a cluster profile shows which schedulers the authors
  support.

## Explaining it

- A rule's `input:` and `output:` are file patterns; a wildcard such as
  `{sample}` means it runs once per sample.
- Work backwards from the target rules to explain the order of steps.
- Inputs are normally the config file and the sample sheet. When mapping the
  researcher's data, say which keys and columns they would need to fill in.
