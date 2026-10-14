# Reading other workflow systems

WorkflowHub accepts workflows of many types. When there is no dedicated
guide, use the general approach below and be open about how deeply you could
read the workflow.

## General approach

1. Read the README and the workflow's description first.
2. Find the entry point: the RO-Crate's main entity, or the obvious main
   file.
3. Identify inputs, steps, outputs and dependencies, at whatever level of
   detail the format allows.
4. Look for environment files (`requirements.txt`, `environment.yml`,
   `Dockerfile`) to describe software requirements.
5. State your confidence. "Explained from the README; the workflow file
   itself is in a format I couldn't read in detail" is a useful sentence.

## Notes on common types

**Jupyter notebooks (`.ipynb`).** JSON containing cells. Read markdown and
code cells in order, and skip stored outputs, which can be very large.
Imports reveal dependencies; early cells usually load inputs.

**WDL.** Files start with a `version` line and contain a `workflow` that
`call`s `task`s. Each task has `input`, `command`, `output` and `runtime`
sections; `runtime` often names a container image.

**KNIME (`.knwf`).** A zipped archive of XML node settings, hard to read in
detail. Rely on the description, README and any workflow image, and list
node names if they are visible in the archive's folder names.

**Scripts and Makefiles.** Treat each script or target as a step. Explain
what each one reads and writes, and in what order they are meant to run.

**Unrecognised formats.** Explain from the metadata and README only, and
say so.