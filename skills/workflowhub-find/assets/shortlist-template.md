# WorkflowHub shortlist: <research need in a few words>

Searched <YYYY-MM-DD> on WorkflowHub (https://workflowhub.eu)

Assumptions: <any assumptions made about the need, or "none">

| Rank | Workflow | Type | Maturity | License | Latest version | Why it fits | Concerns |
|---|---|---|---|---|---|---|---|
| 1 | [<title>](https://workflowhub.eu/workflows/<id>) (ID <id>) | <type> | <stable / work in progress / unknown> | <license or unknown> | <n or unknown> | <one line> | <concerns or none> |

## Search log

| Query | Filters | Results |
|---|---|---|
| <terms> | <filters or none> | <count> |

## Gaps

<What was not found, or "none".>

## Suggested next step

<For example: evaluate candidates 1 and 3 against your data.>

---

## Handoff record

Include this when another skill or agent will use the results, or when the
researcher wants to save them. The `workflow` block is shared by all
WorkflowHub skills; here, `version` is the latest version at search time.

```yaml
shortlist:
  need: "<one-sentence summary of the research need>"
  searched_on: "<YYYY-MM-DD>"
  source: "https://workflowhub.eu"
  assumptions: "<or none>"
  candidates:
    - workflow:
        id: <number>
        title: "<title>"
        url: "https://workflowhub.eu/workflows/<id>"
        version: <number or "unknown">
        workflow_type: "<for example Galaxy>"
        maturity: "<stable | work in progress | unknown>"
        license: "<SPDX identifier or unknown>"
        last_updated: "<YYYY-MM-DD or unknown>"
        team: "<team name or unknown>"
      rank: <number>
      reason: "<one line>"
      concerns: []
  search_log:
    - query: "<terms>"
      filters: {}
      results: <count>
  gaps: "<what was not found, or none>"
```