# Explanation template

Match the depth to the question: answer a narrow question with the relevant
section only. Include the handoff record when another skill or agent will use
the results, or when the researcher wants to save them.

---

# <title> (ID <id>, version <n>)

<workflow type> · <license> · https://workflowhub.eu/workflows/<id>?version=<n>

## What it does

<Two or three plain-language sentences.>

## Inputs

| Input | What it is | Format | Required | Default |
|---|---|---|---|---|
| <name> | <description> | <format> | <yes / no> | <value or none> |

## Steps

1. **<Stage name>:** <what happens>. Tools: <tool names>.
2. ...

## Key parameters

| Parameter | Default | Why you might change it |
|---|---|---|
| <name> | <value> | <one line> |

## Outputs

| Output | What it is | Format |
|---|---|---|
| <name> | <description> | <format> |

## What you'd need to run it

- **Software:** <containers, environments, workflow system version>
- **Reference data:** <databases, genomes, indexes, or none>
- **Compute:** <if stated, or unknown>
- **Platform:** <for example, a Galaxy server with these tools installed>

## Your data

| Workflow input | Your data | Status | What to do |
|---|---|---|---|
| <name> | <file or description> | <matches / needs conversion / missing / unclear> | <action> |

## Notes

<Limitations, assumptions, items marked as inferred, and anything the
workflow's tests or README add.>

---

## Handoff record

The `workflow` block is shared by all WorkflowHub skills.

```yaml
explanation:
  explained_on: "<YYYY-MM-DD>"
  workflow:
    id: <number>
    title: "<title>"
    url: "https://workflowhub.eu/workflows/<id>"
    version: <number>
    workflow_type: "<for example Snakemake>"
    maturity: "<stable | work in progress | unknown>"
    license: "<SPDX identifier or unknown>"
    last_updated: "<YYYY-MM-DD or unknown>"
    team: "<team name or unknown>"
  main_file: "<path in the package>"
  completeness: "<full | partial: reason>"
  purpose: ""
  inputs:
    - name: ""
      description: ""
      format: ""
      required: true
      default: null
  stages:
    - name: ""
      summary: ""
      tools: []
  key_parameters:
    - name: ""
      default: ""
      why_it_matters: ""
  outputs:
    - name: ""
      description: ""
      format: ""
  requirements:
    software: []
    reference_data: []
    compute: "<or unknown>"
    platform: "<or unknown>"
  data_mapping:
    - workflow_input: ""
      researcher_data: ""
      status: "<matches | needs conversion | missing | unclear>"
      action: ""
  tools_to_cite: []
  inferred: []
  open_questions: []
```