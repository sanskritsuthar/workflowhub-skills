---
name: workflowhub-reuse
description: "Prepare to run a chosen WorkflowHub workflow version in the researcher's own environment: retrieve the exact version, work out how to obtain and launch it on their platform, prepare inputs, and hand off to the execution environment. Use this when a researcher has chosen a WorkflowHub workflow and asks how to run it, import it into Galaxy, launch it with Nextflow, Snakemake or a CWL runner, or set up a first test run. Read-only on WorkflowHub; doesn't run anything without the researcher's approval."
license: BSD-3-Clause
metadata:
  version: "0.0.1"
  status: outline
  maintainer: eScience Lab, University of Manchester
---

# Prepare a workflow for reuse (outline)

## Purpose
Bridge from "I've chosen this workflow" to "I'm ready to run it", without
running anything on the researcher's behalf.

## Procedure (outline)
1. Confirm the workflow, exact version and target platform.
2. Gather run instructions: README, test profile or test data, required
   parameters.
3. Produce a run plan for the platform: how to obtain the workflow (for
   example, import into Galaxy by TRS ID, or launch a Nextflow pipeline at a
   pinned revision), prepare inputs and sample sheets, and fetch reference
   data.
4. Recommend a first test run on small or test data.
5. Hand off: to the researcher, or as a record for an execution server, such
   as a Galaxy MCP server.

## Outputs
Run plan, input checklist, handoff record.

## Open design questions
- Should this skill call execution servers directly, or only prepare
  handoffs?
- Per-platform guides, like Explain's?
- What approval is needed before any run, given that it executes code
  written by someone else?