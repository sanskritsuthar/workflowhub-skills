# Reaching WorkflowHub

The procedures in the WorkflowHub skills describe capabilities, not tool
names. This file maps each capability to how to perform it. Use the first
option that is available in your environment.

This file is identical in every WorkflowHub skill. Update all copies together.

## Option 1: WorkflowHub MCP server (preferred)

> Placeholder: the WorkflowHub MCP server is in development. Replace each
> TBD with the real tool name when it is final.

| Capability | MCP tool |
|---|---|
| Search and filter workflows | TBD (expected: `search_workflows`) |
| Read a workflow's metadata, including versions | TBD (expected: `get_workflow`) |
| Download a workflow's content (main file and file list) | TBD (expected: `get_workflow_content`) |
| List valid filter values (types, tags, teams) | TBD (expected: `list_filter_values`) |

## Option 2: WorkflowHub public API (interim)

Use this when the MCP tools aren't connected but you can make HTTP requests,
for example with curl or in a code-execution environment. Send only GET
requests. Base URL: `https://workflowhub.eu`. Send the header
`Accept: application/vnd.api+json` to get JSON rather than HTML.

| Capability | Request | Status |
|---|---|---|
| Search and filter workflows | `GET /workflows?filter[query]=<terms>`, plus filters below | Confirmed on the web listing; check JSON |
| Read a workflow's metadata | `GET /workflows/<id>`, or add `?version=<n>` | To verify |
| Download a workflow's content | `GET /workflows/<id>/ro_crate?version=<n>` (a zip file) | To verify |
| List all workflows (GA4GH TRS API) | `GET /ga4gh/trs/v2/tools` | Confirmed |
| Read one workflow file (GA4GH TRS API) | `GET /ga4gh/trs/v2/tools/<id>/versions/<n>/<type>/descriptor` | To verify, including type codes |

Filters for the search request:

| Filter | Parameter | Status |
|---|---|---|
| Free text | `filter[query]=<terms>` | Confirmed on the web listing |
| Team | `filter[project]=<team id>` | Confirmed on the web listing |
| Workflow type | `filter[workflow_type]=<key>` | To verify |
| Tag | `filter[tag]=<tag>` | To verify |

Notes:

- Encode square brackets as `%5B` and `%5D`, or pass `-g` to curl so it
  doesn't treat them as patterns.
- If a response includes `links.next`, follow it to get further pages.
- The content download is a zip archive. Its `ro-crate-metadata.json` file
  identifies the main workflow file.

Example:

```bash
curl -s -g -H "Accept: application/vnd.api+json" \
  "https://workflowhub.eu/workflows?filter[query]=nanopore%20assembly"
```

## Option 3: neither is available

Tell the researcher you can't reach WorkflowHub from this environment, and
give them a link they can open themselves:

- Search: `https://workflowhub.eu/workflows?filter%5Bquery%5D=<terms>`
  (replace spaces with `%20`)
- A specific workflow: `https://workflowhub.eu/workflows/<id>`

Don't scrape web pages and don't answer from memory.