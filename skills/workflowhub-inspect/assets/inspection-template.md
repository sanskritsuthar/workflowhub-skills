# Inspection: <workflow title>

**Workflow:** ID <id>, version <n> (<version name or unknown>), <URL>
**WorkflowHub metadata:** license <license>, team <team>, latest version
<n>, version date <date>
**Citable identifier:** <DOI from `cite-as`, or "none minted">
**Maturity:** <`creativeWorkStatus` from the crate or Bioschemas, or "unknown">

## How it was read

- Signposting: <links found: cite-as, describedby types, item>
- Crate retrieved by: <metadata route / ZIP>, <size>, RO-Crate version <v>
- Bioschemas record read: <yes / no>
- Not checked: <for example "workflow file not downloaded", "test service
  not queried", "ZIP contents not compared with metadata">

## Profiles

| Layer | Declared (where) | Observed |
|---|---|---|
| RO-Crate | <identifier> (descriptor) | yes |
| Workflow RO-Crate | <identifier or "not declared"> | <yes / partly: what is missing> |
| Workflow Testing | <declared or not> | <suites, engine, service or none> |
| Run Crate profiles | <identifiers or "not declared"> | <N recorded runs or none> |
| Other | <identifiers> | <what they are, if known> |

## What the crate holds

- **Main workflow:** <file>, language <language and version>, version <v>,
  repository <url>
- **Diagram:** <yes/no>
- **Inputs:** <N declared: first few with types, or "not declared">
- **Outputs:** <same>
- **Steps:** <N declared, or "not declared">
- **Tests:** <suites, definition, engine, service, or none>
- **Recorded runs:** <N, what and where it ran, when, software and version,
  inputs and outputs, or none>
- **Creators / authors:** <names with identifiers, or "none declared">
- **Contributors:** <names with identifiers, or "none declared"; say if one
  is the submitter>

## Beyond the profile

<entity types not covered above, with counts and anything relevant, or
"nothing further">

## Differences between sources

| Item | WorkflowHub API | Crate | Bioschemas |
|---|---|---|---|
| <title, license, creators, contributors, version, dates, maturity, inputs, outputs, steps: only the rows that differ> | <value> | <value> | <value or "not read"> |

<"none found" if nothing differs>

## Suggested next step

<for example: evaluate against the researcher's data; explain the workflow
step by step; cite it. The researcher decides.>
