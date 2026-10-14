# Evaluation templates

Use the single-workflow format for one workflow and the comparison format for
several. Include the handoff record when another skill or agent will use the
results, or when the researcher wants to save them.

---

## Single workflow

# Evaluation: <title> (ID <id>, version <n>)

**Verdict: <good fit / fit with caveats / poor fit / can't tell yet>**
<Two or three sentences on the reasons that drive the verdict.>

Evaluated <YYYY-MM-DD> for: <purpose>, <data>, <platform>

| Criterion | Rating | Evidence |
|---|---|---|
| Task fit | <rating> | <one line> |
| Input and output fit | <rating> | <one line> |
| Platform fit | <rating> | <one line> |
| License | <rating> | <one line> |
| Documentation | <rating> | <one line> |
| Maintenance | <rating> | <one line> |
| Credibility | <rating> | <one line> |
| Requirements | <rating> | <one line> |

**Concerns to resolve:** <list, or "none">

**What WorkflowHub can't tell us:** <for example, whether results are
correct on your data>

**Suggested next step:** <for example, a walkthrough of inputs with
workflowhub-explain>

---

## Comparison

# Comparison: <need in a few words>

**Summary:** <Two or three sentences. If the choice depends on the
researcher's priorities, say so conditionally.>

| Criterion | <title A> (ID, v) | <title B> (ID, v) | <title C> (ID, v) |
|---|---|---|---|
| Task fit | <rating> | <rating> | <rating> |
| Input and output fit | <rating> | <rating> | <rating> |
| Platform fit | <rating> | <rating> | <rating> |
| License | <rating> | <rating> | <rating> |
| Documentation | <rating> | <rating> | <rating> |
| Maintenance | <rating> | <rating> | <rating> |
| Credibility | <rating> | <rating> | <rating> |
| Requirements | <rating> | <rating> | <rating> |
| **Verdict** | <verdict> | <verdict> | <verdict> |

**Differences that matter:** <the few that decide the choice, with evidence>

**Choose:** <A if ...; B if ...>

---

## Handoff record

One record per workflow. A comparison is a list of these records plus a
summary. The `workflow` block is shared by all WorkflowHub skills.

```yaml
assessment:
  assessed_on: "<YYYY-MM-DD>"
  context:
    purpose: "<exploratory | publication | production | other: ...>"
    data: "<as understood, or unknown>"
    platform: "<as understood, or unknown>"
    constraints: "<or none>"
  workflow:
    id: <number>
    title: "<title>"
    url: "https://workflowhub.eu/workflows/<id>"
    version: <number>
    workflow_type: "<for example Nextflow>"
    maturity: "<stable | work in progress | unknown>"
    license: "<SPDX identifier or unknown>"
    last_updated: "<YYYY-MM-DD or unknown>"
    team: "<team name or unknown>"
  criteria:
    task_fit: { rating: "<strong | adequate | weak | unknown>", evidence: "" }
    input_output_fit: { rating: "", evidence: "" }
    platform_fit: { rating: "", evidence: "" }
    license: { rating: "", evidence: "" }
    documentation: { rating: "", evidence: "" }
    maintenance: { rating: "", evidence: "" }
    credibility: { rating: "", evidence: "" }
    requirements: { rating: "", evidence: "" }
  verdict: "<good fit | fit with caveats | poor fit | can't tell yet>"
  concerns: []
  open_questions: []
```