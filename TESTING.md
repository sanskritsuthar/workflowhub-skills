# Testing the WorkflowHub skills

How to test the skills, what to look for, and a log of results.

## How to test

1. Install the four skills from `skills/` in a test project (see the README).
   Use a separate folder that acts as a researcher's project, so outputs
   such as provenance files don't land among the skill files.
2. Start a fresh session for each test, unless the test says to continue.
   Leftover context hides gaps in the instructions.
3. Repeat the core tests (1, 2, 5, 9) in at least one non-Claude agent, such
   as OpenAI Codex (install under `.agents/skills`; see the README). Record
   the agent in each log entry, and whether network access for curl needed
   approval or was blocked.
4. Optional: compare with the skills turned off. In Claude Code, type
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
8. **Find, molecular dynamics.** "Is there an existing workflow for
   molecular dynamics simulation of a protein with GROMACS? I can use
   Galaxy or Python." Check that biobb tutorial variants are grouped, and
   that unrelated "MD" hits are dropped.
9. **Inspect, Run Crate (workflow 1326).** "Inspect WorkflowHub workflow 1326:
   what is in its RO-Crate, which profiles does it conform to, and has it
   been run before?" Expected: Workflow RO-Crate 1.0 and the Process,
   Workflow and Provenance Run Crate 0.5 profiles declared on the root;
   about 29 recorded runs; a COMPSs lysozyme-in-water workflow whose main
   entity is a Python file; no download of the ZIP.
10. **Inspect, size and rate limits (workflow 1326 and others).** "Inspect
    workflows 1326, 401, 1201 and 248 and tell me which are Run Crates."
    Check that crates are read one at a time with at least 2 seconds between
    requests, via the metadata route, and that no ZIP is downloaded.
11. **Inspect, declared versus observed.** "Does workflow 248 conform to
    Workflow RO-Crate? Does it have tests?" Expected: no workflow profile
    declared, but the structure matches; a Planemo test suite on GitHub
    Actions; the agent doesn't query the test service.
12. **Inspect, no declared inputs.** "What inputs does workflow 401 declare
    in its crate?" Expected: "not declared", not "none", and a pointer to the
    workflow file.
13. **Explain a Run Crate (workflow 1326).** "Walk me through workflow 1326
    and tell me what parameters I should change." Check that values from the
    recorded run are called an example of one past run, not defaults, and
    that the ZIP is downloaded only if needed, once, within the limit.
14. **Signposting directly.** "Does https://workflowhub.eu/workflows/248?version=7
    support FAIR Signposting? Is there a DOI I can cite?" Expected: Level 1
    headers with `describedby` and `item`; no `cite-as`, no linkset; the
    DOI shown in the DataCite XML returns 404 at doi.org, so the agent says
    there's no citable DOI. Repeat with version 1 of workflow 624, which has
    a `cite-as` link.
15. **Content negotiation trap.** "Get the RO-Crate metadata for workflow 248
    version 7 using content negotiation." Expected: the agent notices the
    response is Bioschemas, not an RO-Crate, and uses the metadata route.
16. **Large crate.** "Download the full crate for workflow [ID of a crate over
    20 MiB]." Expected: the agent checks the size with HEAD and asks before
    downloading. (Pick a large crate when testing; none is recorded here.)

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
- **Limits.** For tests 9 to 13 and 16, did it check size first, fetch one
  crate at a time with at least 2 seconds between requests, and avoid
  repeat downloads?
- **Declared versus observed.** For tests 9 to 12, does the report separate
  what the crate claims from what the agent saw?

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

### 2026-10-14: Molecular dynamics search and FAIR Signposting check

- **Agent and route:** Claude Code, public API plus HTTP `Link` headers
- **Result:** pass with issues
- **What happened:** Searches for "molecular dynamics", "MD simulation" and
  "gromacs" returned 68, 102 and 91 workflows in one page each. Metadata,
  `?version`, workflow-type and tag filters, RO-Crate download and TRS
  worked. Signposting headers on workflow 248 version 7 gave DataCite XML,
  Bioschemas JSON-LD and the RO-Crate link; `Accept` selected each
  representation. No linkset is offered; `cite-as` appears only for versions with a minted DOI (624 v1 yes, 248 v7 no).
- **Issues:** maturity not in the JSON; "MD simulation" pulls in unrelated
  workflows; biobb tutorials appear as many near-duplicate variants; the
  DataCite DOI for 248 v7 was not in the JSON API.

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