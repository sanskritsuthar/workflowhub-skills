---
name: workflowhub-survey
description: "Systematically survey WorkflowHub for all workflows on a topic and summarise the landscape: what exists, in which workflow systems, from which teams, how mature and how recently maintained, and where the gaps are. Use this for review articles, grant proposals, community curation or gap analysis, when a researcher wants an overview of everything on a topic rather than a shortlist for their own analysis. Read-only."
license: BSD-3-Clause
metadata:
  version: "0.0.1"
  status: outline
  maintainer: eScience Lab, University of Manchester
---

# Survey the workflow landscape (outline)

## Purpose
A reproducible overview of a topic, rather than workflowhub-find's
shortlist for a single analysis.

## Procedure (outline)
1. Define scope and inclusion criteria with the researcher.
2. Run systematic, logged searches, paging through all results.
3. Collect light metadata for every included workflow, in batches.
4. Aggregate by workflow system, team, maturity, license and recency.
5. Identify gaps, duplicates and clusters.
6. Write a method statement so the survey can be repeated.

## Outputs
Landscape report, a table of included workflows, search log, method
statement.

## Open design questions
- Acceptable API load for large enumerations, and rate limits.
- Is a bulk listing route (such as TRS) better than repeated searches?
- Context budget: split the work across several agents by query or batch?