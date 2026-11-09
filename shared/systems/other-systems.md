# Other workflow systems

How to fill the workflow profile (`workflow-profile.md`) when there is no
guide for the workflow's system. WorkflowHub accepts many types. None of the
notes below has been tested against WorkflowHub yet; say how deeply you
could read the workflow.

## General approach

1. Read the README and the workflow's description first.
2. Find the entry point: the crate's main entity, or the obvious main file.
   The crate's file list may be incomplete (`workflow-profile.md`), so list
   the ZIP if you need to know what is there.
3. Fill the profile at whatever depth the format allows:
   - `provides`: declared inputs or parameters; files the code reads.
   - `outputs`: declared outputs; files the code writes.
   - `steps`: tasks, calls, cells, scripts or targets, in order.
   - `requirements`: environment files (`requirements.txt`,
     `environment.yml`, `Dockerfile`), containers, reference data.
   - `docs.tests`: test folders or example data.
4. Keep files in the scratch folder and read them in pieces: list a file's
   structure (headings, function or task names, JSON keys) before reading
   any part in full. Read small files whole.
5. Skip `ro-crate-preview.html` and stored notebook outputs. Keep any
   diagram of the workflow: for a format you can't read, it may be the
   clearest view of the steps, and the researcher can be pointed to it.
6. State your confidence, and set the profile's `completeness` to partial
   where you couldn't read the format. "Explained from the README; the
   workflow file itself is in a format I couldn't read in detail" is a
   useful sentence.

## Notes on common types

**Jupyter notebooks (`.ipynb`).** JSON containing cells. Read markdown and
code cells in order, and skip stored outputs, which can be very large.
Imports reveal software; early cells usually load inputs.

**WDL.** Files start with a `version` line and contain a `workflow` that
`call`s `task`s. The workflow's `input` section is `provides`. Each task has
`input`, `command`, `output` and `runtime` sections; `runtime` often names a
container image.

**KNIME (`.knwf`).** A zipped archive of XML node settings, hard to read in
detail. Rely on the description, README and any workflow image, and list
node names if they are visible in the archive's folder names.

**Scripts and Makefiles.** Treat each script or target as a step. Say what
each one reads and writes, and in what order they are meant to run.

**Unrecognised formats.** Fill the profile from the metadata and README
only, mark it partial, and say so.
