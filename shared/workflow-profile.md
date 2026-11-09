# The workflow profile

The facts the WorkflowHub skills need about one version of one workflow, in a
single shape that fits every workflow system. Build it once, save it, and let
each skill read the part it needs. How to fetch and save responses is in
`tools.md`; this file says what to collect, where to look, and what to write
when something isn't there.

This file is identical in every skill that uses it. Update all copies
together.

## First rule: a starting point, never a limit

The profile is the least a skill needs, not the most it may read. When the
researcher's question needs something the profile doesn't hold, read more:
from the files you already saved first, then with a targeted request. Say what
you read and why. Nothing in this file is a reason to answer less than the
question asks.

The only hard limits are the ones that protect the researcher and WorkflowHub,
and they are set elsewhere: read only, never run workflow code, and the size
and pacing limits on requests and downloads (`tools.md`, `ro-crate-consume`).
Above a size limit, ask the researcher; don't silently answer with less.

## Levels

| Level | What it holds | Usually enough for |
|---|---|---|
| 1. Registry facts | identity, description, annotations, license, maturity, versions and dates, people, team, DOI, links | evaluate (most criteria), cite |
| 2. Structure summary | what the user provides (names, data or setting, format, required), outputs (names, formats), what running it needs (names), which docs and tests exist, number of steps | evaluate (input, output and requirement criteria) |
| 3. Full structure | level 2 with descriptions and defaults, plus the steps in order with the tool each runs and what feeds it | explain |
| 4. Detail | one step's full settings or command, the inside of a subworkflow, a section of a README, a test definition | any skill, when the question needs it |

"Usually enough" is a default, not a boundary. Go to the next level when the
question turns on it. Two common cases:

- **Reference data** is often visible only at level 4. Galaxy workflow 1612
  aligns reads against a cached `hg38` genome that must be installed on the
  Galaxy server, and the only place that says so is one step's settings.
  Nextflow's `--genome` parameter and its config are similar. When the
  researcher's data or platform depends on it, look, even while evaluating;
  each system's guide says where.
- **A format question** ("will it take my BAM files?") needs the declared
  format of one input. Read that input at level 3, not the whole workflow.

## Every field: a value, or why not

Each field holds exactly one of these:

- **A value, with its source:** `api`, `bioschemas`, `crate`, `file:<path>`,
  `readme`, `internals` or `inferred`.
- **`not declared`:** you checked the places that would hold it, and the
  workflow doesn't say. A missing license is a finding, and evaluate rates
  it.
- **`unknown: <reason>`:** you couldn't check. For example the download was
  declined, the files are only in an external repository, the format is one
  you can't read, or the environment can't save files. Evaluate rates these
  as unknown, never as weak.
- **Several values, each with its source,** when sources disagree. Don't pick
  one silently.

Lists follow the same rule. An empty list means "checked, and there are
none", with the source you checked. If you didn't check, write `unknown`.
WorkflowHub's `internals` block is often empty because nothing was extracted,
so an empty `internals` never makes an empty list.

## Level 1: registry facts and where to find them

Try the sources in order, and stop when you have the fact. "API" is the JSON
API response for the workflow and version; "crate" is the crate metadata;
"Bioschemas" is the JSON-LD record (all in `tools.md`). Tested 2026-11-09 on
workflows 248, 1266, 1612 and 1815 unless noted.

| Field | Where to look, in order | Notes |
|---|---|---|
| ID, version, URL | the request; API `version`, `latest_version` | If no version was asked for, use `latest_version` and say so. |
| Title, description | API `title`, `description`; crate root `name`, `description`; README | |
| Workflow type | API `workflow_class.key`; crate main entity `programmingLanguage`; the main file's extension | |
| Topics, operations, keywords | API `topic_annotations`, `operation_annotations` (EDAM terms), `tags`; Bioschemas `keywords` | Often empty: `not declared`. |
| License | API `license`; crate root `license`; a `LICENSE` file in the crate's file list | |
| Maturity | crate `creativeWorkStatus`; Bioschemas `creativeWorkStatus` | Not in the API. They can differ: 1266 version 7 gave Stable in the crate and nothing in Bioschemas. |
| Version date | API `created_at` of the versioned request | 248: version 1 2021-12-07, version 7 2026-03-10. `updated_at` can change without a new version (248 version 1 showed 2026-11-09), so don't use it as the version date. |
| Versions | API `versions[]`: `version`, `doi`, and for workflows kept in git, `remote` and `ref` | A date for each version takes one request per version; fetch them only if maintenance needs more than the first and latest. |
| Source repository, upstream release | API `versions[].remote` and `ref`; crate root `isBasedOn`; crate main entity `url` | 1266: `github.com/nf-core/drugresponseeval`, `refs/tags/1.4.0`. Present for all four types tested (248, 360, 1266, 1815). |
| Creators | API `creators` (names and ORCIDs) and `other_creators` (free text); crate; Bioschemas `creator` | Not Bioschemas `contributor`, which can be the submitter. Empty for 1266 and 1612 in the API; then try the crate and README before `not declared`. |
| Team | Bioschemas `producer[].name` | The API gives only a project ID. |
| DOI | API `doi` for the version; a `cite-as` link | A DOI in DataCite XML alone isn't proof (`tools.md`). |
| Issue tracker | API `discussion_links` | |
| Tools named by the authors | API `tools` (bio.tools names and links) | 1815 lists 21, 1612 lists 9; many Nextflow workflows list none. A list from the authors, not from the files. |

## Levels 2 and 3: structure, in one shape for every system

```yaml
structure:
  level: 2                      # 2 or 3, the depth actually reached
  read_from: []                 # what you actually read, e.g. [crate, "file:main.nf", readme]
  completeness: "full"          # or "partial: <reason>"
  provides:                     # everything the user supplies: data and settings
    - name: ""
      kind: "data"              # data: names or contains files; setting: a number, text, switch or choice
      description: ""           # level 3
      format: ""                # file format or value type
      required: true            # true, false or unknown
      default: null             # level 3
      source: ""
  outputs:
    - { name: "", description: "", format: "", source: "" }
  steps:
    count: 0                    # level 2: the number, or unknown
    list: []                    # level 3: { name, tool, from: [names of earlier steps or inputs] }
  requirements:
    workflow_system: ""         # with a minimum version, if stated
    software: []                # containers, environments, tools that must be installed
    reference_data: []          # genomes, indexes, databases
    platform: []                # server features, executors, profiles
    compute: "unknown"
  docs:
    readme: "<path or not declared>"
    usage: "<path or not declared>"
    tests: "<path or not declared>"
```

Data and settings sit in one list because workflow systems draw the line
differently: in CWL settings are inputs, in Nextflow they are `params`, in
Snakemake they are in the config file, and in Galaxy they are parameter
inputs and values marked to be set at run time.

### Where structure comes from, for any system

Work down this list. The first three cost little and often settle level 2.

1. **The crate's file list** (crate metadata, root `hasPart`): a quick look
   at which files are there, such as a README, usage docs, tests, a
   parameter schema or a `LICENSE`, without downloading any of them. It is
   **incomplete**: 1815 lists 2 parts, but its ZIP holds 117 files, including
   the 8 rule files the workflow runs; 1266 lists 23 and holds 147. Use it to
   find files, never to conclude one is absent. Only a listing of the ZIP
   shows what is really there.
2. **Declared inputs and outputs** in the crate or Bioschemas, and a test
   suite the crate mentions. Often not declared: none of 1266, 1612, 1815 or
   2367 declares any.
3. **Hints:** the API's `internals` and `tools`. Use them to plan what to
   read, not as the answer (`tools.md`).
4. **The workflow file and its companions.** The authoritative source. How
   to read each system's files is in its guide. Because sources 1 to 3 are
   so often empty, expect level 2 to need the main workflow file, which the
   TRS request returns without the ZIP (`tools.md`). Companion files, such as
   Nextflow modules, Snakemake rule files and CWL tool files, need the ZIP.
5. **The README and docs,** for what the files leave unclear.

Tested 2026-11-09: level 2 was reached for 1266 (Nextflow), 1612 (Galaxy)
and 1815 (Snakemake) from their files, and for 2367 (CWL) from the main file
alone, because its ZIP (61 MB) is over the size limit.

### When the files can't be read

Sometimes level 2 or 3 can't come from the files: the workflow is only a
link to an external repository, the crate is over the size limit and the
researcher declines the download, the format is one you can't read, or there
are more files than the question justifies. Then:

- fill what you can from sources 1, 2, 3 and 5, and from the main workflow
  file if the TRS request can get it;
- if the ZIP is over the size limit, tell the researcher its size and what
  it would add, and ask (2367: the main CWL file gives inputs, outputs and
  the step list; the tool definitions each step runs are in the 61 MB ZIP);
- if the source repository is known (`versions[].remote` and `ref`), offer
  to read the missing files from it at that `ref`. Ask first: reading
  outside WorkflowHub needs the researcher's agreement;
- set `completeness` to `partial`, with the reason;
- give every list its source, so the researcher can see it came from a
  README or a hint rather than the workflow itself;
- still answer the question, and say which parts are partial and what would
  complete them.

## Saving and reusing the profile

Save the profile as `profile.yaml` in the workflow's scratch folder
(`<scratch>/workflowhub/<id>/v<n>/`, beside the saved responses), with the
date and the level reached. A later skill in the same session reuses it and
adds a level when it needs one, rather than fetching again.

Its `workflow` block is the one in every skill's handoff record (`id`,
`title`, `url`, `version`, `workflow_type`, `maturity`, `license`,
`last_updated`, `team`), so it can be copied across. `last_updated` is the
latest version's date.

## When you can't save files

Some environments can't write files or run a JSON tool, for example when
tools return their results straight into the conversation. Don't refuse the
task, and don't skip the profile:

- build it in your notes, from one workflow at a time;
- take only the fields you need from each response and move on;
- for comparisons of many workflows, tell the researcher that you can go
  into depth on only a few, and ask which.
