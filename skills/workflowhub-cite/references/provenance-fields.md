# Provenance record fields

The provenance file is a simple YAML list of entries, one per workflow
version the project used or plans to use. It is meant to be readable by
people and easy for agents to write correctly. Its fields were chosen to map
onto RO-Crate later.

## Entry fields

| Field | Meaning |
|---|---|
| `recorded_on` | Date the entry was written |
| `status` | `planned` (intended use) or `used` (actually run) |
| `workflow` | The shared workflow block (ID, title, URL, version, type, maturity, license, last updated, team), plus `doi` and `creators` |
| `use.purpose` | What the workflow was used for in this project |
| `use.run_on` | The platform or environment, such as a named Galaxy server or an HPC cluster |
| `use.run_date` | When it was run |
| `use.engine_version` | The workflow system version, such as Nextflow 24.04 |
| `use.parameters_changed` | Only parameters changed from their defaults, as name and value |
| `use.reference_data` | Name and version of each reference dataset or database |
| `use.inputs` | Descriptions or project paths of the inputs |
| `use.outputs` | Project paths of the outputs |
| `citation` | The citation as given to the researcher |
| `tools_to_cite` | Tools used by the workflow that should be cited |
| `notes` | Anything else worth keeping |

Use `unknown` for anything not known. Don't leave fields out, so that every
entry has the same shape.

## Indicative mapping to RO-Crate

This mapping is a proposal for discussion, to be confirmed by the team. A
`used` entry corresponds roughly to a run described with the Workflow Run
Crate profile:

| Provenance field | RO-Crate term (indicative) |
|---|---|
| The entry itself (status `used`) | `CreateAction` |
| `workflow.url`, `workflow.id` | `@id` of the `ComputationalWorkflow`, the action's `instrument` |
| `workflow.title` | `name` |
| `workflow.version` | `version` |
| `workflow.doi` | `identifier` |
| `workflow.license` | `license` |
| `workflow.creators` | `creator` (`Person` or `Organization`) |
| `workflow.workflow_type` | `programmingLanguage` (`ComputerLanguage`) |
| `use.run_date` | `endTime` |
| `use.inputs` | `object` |
| `use.outputs` | `result` |
| `use.parameters_changed` | `PropertyValue` entities in `object` |
| `use.run_on`, `use.engine_version`, `workflow.team` | To decide |