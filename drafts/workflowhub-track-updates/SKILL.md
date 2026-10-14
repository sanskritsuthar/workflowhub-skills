---
name: workflowhub-track-updates
description: "Check whether WorkflowHub workflows recorded in a project's provenance file have new versions or important changes, and summarise what changed. Use this when a researcher asks whether workflows they depend on have been updated, wants a periodic check of pinned workflow versions, or is deciding whether to upgrade. Read-only; never changes the project's pinned versions without the researcher's agreement."
license: BSD-3-Clause
metadata:
  version: "0.0.1"
  status: outline
  maintainer: eScience Lab, University of Manchester
---

# Track workflow updates (outline)

## Purpose
Keep researchers aware of changes to the workflows they rely on, the
repeatable check that suits automation.

## Procedure (outline)
1. Read the project's provenance file (written by workflowhub-cite).
2. For each workflow, read current metadata and compare the latest version
   with the recorded one.
3. Summarise changes: new versions and their notes, and changes in maturity,
   license, inputs or key parameters.
4. Flag changes that could affect results or reuse.
5. Report; never upgrade automatically.

## Outputs
Update report; optionally a note appended to the provenance entry.

## Open design questions
- Does the API expose version notes or change summaries?
- How deep should comparisons go? Metadata only, or also workflow content?
- Scheduling: run on request only, or as a scheduled task in clients that
  support it?