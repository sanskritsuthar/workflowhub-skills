# The workflow profile

The profile holds the facts that the WorkflowHub skills need about one
version of one workflow. It has the same shape for every workflow system.
Make it one time and save it. Each skill then reads the part that it needs.

`tools.md` tells you how to get and save responses. This file tells you
which facts to collect, where to find them, and what to write when a fact is
not available.

This file is the same in every skill that uses it. If you change it, change
all copies together.

## First rule: a starting point, never a limit

The profile is the minimum that a skill needs. It is not the maximum that a
skill can read. If the question of the researcher needs a fact that is not in
the profile, read more:

1. Look first in the files that you saved.
2. Then send a request for the specific fact.
3. Tell the researcher what you read and why.

Nothing in this file is a reason to give a less complete answer.

The only fixed limits protect the researcher and WorkflowHub. Other files set
them: read only, never run workflow code, and the limits on size and speed
of requests and downloads (`tools.md`, `ro-crate-consume`). If a download is
larger than a size limit, ask the researcher. Do not give a smaller answer
without a statement.

## Levels

| Level | What it holds | Usually enough for |
|---|---|---|
| 1. Registry facts | identity, description, annotations, license, maturity, versions and dates, people, team, DOI, links | evaluate (most criteria), cite |
| 2. Structure summary | what the user provides (names, data or setting, format, required), outputs (names, formats), what running it needs (names), which docs and tests exist, number of steps | evaluate (input, output and requirement criteria) |
| 3. Full structure | level 2 with descriptions and defaults, plus the steps in order with the tool each runs and what feeds it | explain |
| 4. Detail | one step's full settings or command, the inside of a subworkflow, a section of a README, a test definition | any skill, when the question needs it |

"Usually enough" is a default. It is not a boundary. Go to the next level
when the question depends on it. Two frequent examples:

- **Reference data** is frequently visible only at level 4. Galaxy workflow
  1612 aligns reads to a cached `hg38` genome. That genome must be installed
  on the Galaxy server, and only the settings of one step show this.
  Nextflow's `--genome` parameter and its config are similar. If the data or
  the platform of the researcher depends on reference data, look for it. Do
  this also when you evaluate. Each system guide tells you where to look (see
  "System guides" below).
- **A format question** ("will it take my BAM files?") needs the declared
  format of one input. Read that input at level 3. Do not read the full
  workflow.

## Every field: a value, or why not

Each field holds one of these:

- **A value, with its source:** `api`, `bioschemas`, `crate`, `file:<path>`,
  `readme`, `internals` or `inferred`.
- **`not declared`:** you examined the places that can hold the fact, and the
  workflow does not give it. A missing license is a finding. Evaluate gives
  it a rating.
- **`unknown: <reason>`:** you could not examine the places. Examples: the
  researcher did not agree to a download; the files are only in an external
  repository; you cannot read the format; the environment cannot save files.
  Evaluate rates these as unknown, never as weak.
- **Several values, each with its source,** when the sources are different.
  Do not select one value without a statement.

Lists follow the same rule. An empty list means "examined, and there are
none". Give the source that you examined. If you did not examine a source,
write `unknown`. WorkflowHub's `internals` block is frequently empty because
WorkflowHub extracted nothing. Thus, an empty `internals` never gives an
empty list.

## Level 1: registry facts and where to find them

Use the sources in the order of the table. Stop when you have the fact.

- "API" is the JSON API response for the workflow and version.
- "Crate" is the crate metadata. Read it with `ro-crate-consume`, also when
  you need only one field from it, such as maturity or the file list.
- "Bioschemas" is the JSON-LD record.

`tools.md` tells you how to get each source. The table was tested on
2026-11-09 on workflows 248, 1266, 1612 and 1815, unless the table tells
otherwise.

| Field | Where to look, in order | Notes |
|---|---|---|
| ID, version, URL | the request; API `version`, `latest_version` | If no version was asked for, use `latest_version` and say so. A request without `?version=` describes the latest version (248, 401, 1201), so there is no need to repeat it with the version. |
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
    diagram: "<path or not declared>"   # the crate's `image` on the main workflow, or an image file in the crate
```

Data and settings are in one list because workflow systems divide them
differently. In CWL, settings are inputs. In Nextflow, they are `params`. In
Snakemake, they are in the config file. In Galaxy, they are parameter inputs
and values that the user sets at run time.

### Where structure comes from, for any system

Use the sources in this order. The first three cost little, and they
frequently give enough for level 2.

1. **The file list of the crate** (crate metadata, root `hasPart`). It shows
   quickly which files are there, for example a README, usage documents,
   tests, a parameter schema or a `LICENSE`. You do not download the files.
   The list is **incomplete**. 1815 lists 2 parts, but its ZIP holds 117
   files, with the 8 rule files that the workflow runs. 1266 lists 23 and
   holds 147. Use the list to find files. Never use it to decide that a file
   is not there. Only a list of the ZIP contents shows all files.
2. **Declared inputs and outputs** in the crate or Bioschemas, and a test
   suite that the crate refers to. These are frequently not declared: 1266,
   1612, 1815 and 2367 declare none.
3. **Hints:** the `internals` and `tools` of the API. Use them to plan what
   to read. Do not use them as the answer (`tools.md`).
4. **The workflow file and its companion files.** These are the
   authoritative source. The guide for each system tells you how to read
   them (see "System guides" below). Sources 1 to 3 are frequently empty, so
   level 2 usually needs the main workflow file. The TRS request gives that
   file without the ZIP (`tools.md`). Companion files, for example Nextflow
   modules, Snakemake rule files and CWL tool files, need the ZIP.
5. **The README, documents and diagram,** for what the files do not make
   clear. A diagram from the authors can name the stages in their own words.
   For example, the SVG of 248 has "Complex Setup" and the stages after it.
   The workflow file does not have these names. A diagram that a tool made
   from the workflow shows the same graph as the file. Read it for facts only
   when you cannot read the file.

Tested 2026-11-09: we got level 2 from the files for 1266 (Nextflow), 1612
(Galaxy) and 1815 (Snakemake). For 2367 (CWL), we got level 2 from the main
file only, because its ZIP (61 MB) is larger than the size limit.

### When the files can't be read

Sometimes you cannot get level 2 or 3 from the files. For example:

- the workflow is only a link to an external repository;
- the crate is larger than the size limit, and the researcher does not agree
  to the download;
- you cannot read the format;
- there are more files than the question justifies.

Then do these steps:

1. Fill the profile from sources 1, 2, 3 and 5. If the TRS request can get
   the main workflow file, use that file too.
2. If the ZIP is larger than the size limit, tell the researcher its size
   and what it would add. Ask before you download it. For example, for 2367
   the main CWL file gives inputs, outputs and the step list. The tool
   definitions for each step are in the 61 MB ZIP.
3. If you know the source repository (`versions[].remote` and `ref`), offer
   to read the missing files from it at that `ref`. Ask first: you need the
   agreement of the researcher to read outside WorkflowHub.
4. Set `completeness` to `partial`, and give the reason.
5. Give the source of each list. Then the researcher can see if a list came
   from a README or a hint, and not from the workflow.
6. Still answer the question. Tell the researcher which parts are partial,
   and what can make them complete.

### System guides

Between workflow systems, only two things are different: where each fact is,
and how to extract it. One guide for each system tells you this: `galaxy.md`,
`cwl.md`, `nextflow.md`, `snakemake.md`, and `other-systems.md` for all other
systems. The guides are in the evaluate and explain skills. Each guide:

- connects the fields of the profile to the files of that system;
- gives tested commands for levels 2 and 3;
- tells you what to skip;
- lists the requirements that most frequently stop a run.

## Saving and reusing the profile

Save the profile as `profile.yaml` in the scratch folder of the workflow
(`<scratch>/workflowhub/<id>/v<n>/`, with the saved responses). Record the
date and the level that you reached. A later skill in the same session uses
the profile again. If it needs a higher level, it adds that level. It does
not get the facts again.

The `workflow` block of the profile is the same as the `workflow` block in
the handoff record of each skill. Thus, you can copy it. Its fields are
`id`, `title`, `url`, `version`, `workflow_type`, `maturity`, `license`,
`last_updated` and `team`. `last_updated` is the date of the latest version.

## When you can't save files

Some environments cannot write files or run a JSON tool. An example is an
environment where tools put their results directly into the conversation.
Do not refuse the task. Do not skip the profile. Do these steps:

1. Make the profile in your notes, for one workflow at a time.
2. Take only the fields that you need from each response, and then
   continue.
3. If the researcher wants to compare many workflows, tell them that you can
   examine only a few in detail. Ask which ones.
