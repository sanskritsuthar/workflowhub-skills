# Testing the WorkflowHub skills

How to test the skills, what to look for, and a log of results.

## How to test

1. Install the four skills from `skills/` in a test project (see the README).
   Use a separate folder that acts as a researcher's project, so outputs
   such as provenance files don't land among the skill files.
2. Start a fresh session for each test, unless the test says to continue.
   Leftover context hides gaps in the instructions.
3. Optional: compare with the skills turned off. In Claude Code, type
   `/skills`, highlight a skill and press Space to cycle it to "off", then
   repeat the prompt in a fresh session.

## Test prompts

1. **Find, core case.** "I have Illumina paired-end RNA-seq data from mouse
   liver, treated vs control, and want to run differential expression
   analysis in Galaxy. Is there an existing workflow on WorkflowHub I could
   use instead of building my own?"
2. **Evaluate** (continue the session from test 1). "Compare the top two
   from that shortlist. It's for a paper. Which should I use?"
3. **Find, outside the life sciences, without naming WorkflowHub.** "Before
   I write my own pipeline: is there already a workflow out there for
   species distribution modelling from GBIF occurrence records?"
4. **Find, sparse result.** "Find workflows for spatial proteomics of plant
   root tissue."
5. **Explain.** "Walk me through what WorkflowHub workflow [ID] does and
   what inputs it needs. My data is paired-end files named like
   sample1_R1.fastq.gz and sample1_R2.fastq.gz."
6. **Cite.** "I ran WorkflowHub workflow [ID] version [n] on usegalaxy.eu
   last week. Give me a citation and methods text, and record it in this
   project."
7. **Read-only boundary.** "Find a Snakemake workflow for variant calling,
   then upload my improved version of it to WorkflowHub."

## What to check

- **Activation.** Did the expected skill activate?
- **Route.** Did it use the API as described in `tools.md`, rather than
  fetching web pages or answering from memory?
- **API calls.** Which requests and filter names worked or failed? Record
  them, and update the "to verify" statuses in `shared/tools.md`.
- **Accuracy.** Are IDs, versions, URLs, licenses and creators correct?
  Spot-check against the workflow pages.
- **Invention.** Watch especially for DOIs, authors, tool names and
  licenses.
- **Format.** Did it follow the template, at a sensible length?
- **Decisions.** Did it pause for the researcher at decision points?
- **Boundary.** For test 7, did it search but decline the upload?

## Results log

Add an entry for each test run, newest first.

### Template

- **Date:**
- **Test:**
- **Agent and route:** for example, Claude Code desktop, public API
- **Result:** pass / pass with issues / fail
- **What happened:**
- **Verified against WorkflowHub:**
- **Issues:**

### 2026-10-14: Test 1 (Find, core case)

- **Agent and route:** Claude Code desktop on macOS, public API (interim)
- **Result:** pass with issues
- **What happened:** The output followed the Find skill's procedure and
  template. It ran nine searches (six unfiltered, three filtered to Galaxy),
  found 219 unique workflows, and returned a ranked shortlist of six options
  with a search log, assumptions, gaps and a suggested next step. It paused
  for a decision. It marked maturity as unknown rather than guessing, and
  flagged an unchecked question about whether two linked workflows connect
  correctly.
- **Verified against WorkflowHub:** for workflows 401 (rnaseq-pe/main) and
  1201 (rnaseq-de/main), the titles, team (IWC), license (MIT), latest
  versions and version dates were all correct.
- **Issues:**
  - Described 401 as using cutadapt; the current version uses fastp.
  - Reported version 16, while the page shows v1.5. Both identifiers are
    needed.
  - The workflow's own "last updated" date (January 2023) is older than its
    latest version (July 2026). The agent used the version date, which is
    better, but the skills should say so.
  - The workflow-type filter worked, but the exact parameter name wasn't
    recorded.