# Other workflow systems

This guide tells you how to fill the workflow profile (`workflow-profile.md`)
when there is no guide for the workflow system. WorkflowHub accepts many
workflow types. We did not test the notes below on WorkflowHub. Tell the
researcher how completely you could read the workflow.

## General approach

1. Read the README and the description of the workflow first.
2. Find the entry point: the main entity of the crate, or the main file.
   The file list of the crate can be incomplete (`workflow-profile.md`). If
   you must know all files, list the ZIP contents.
3. Fill the profile as completely as the format lets you:
   - `provides`: declared inputs or parameters; files that the code reads.
   - `outputs`: declared outputs; files that the code writes.
   - `steps`: tasks, calls, cells, scripts or targets, in order.
   - `requirements`: environment files (`requirements.txt`,
     `environment.yml`, `Dockerfile`), containers, reference data.
   - `docs.tests`: test folders or example data.
4. Keep files in the scratch folder, and read them in parts. First, list the
   structure of a file (headings, names of functions or tasks, JSON keys).
   Then read the parts that you need. Read small files completely.
5. Skip `ro-crate-preview.html` and the stored outputs of notebooks. Keep
   each diagram of the workflow. For a format that you cannot read, a
   diagram can be the clearest view of the steps. You can also refer the
   researcher to it.
6. Tell the researcher how sure you are. Set `completeness` in the profile
   to partial for the parts that you could not read. A useful sentence is:
   "Explained from the README; the workflow file itself is in a format I
   couldn't read in detail".

## Notes on common types

**Jupyter notebooks (`.ipynb`).** JSON with cells. Read the markdown and
code cells in order. Skip the stored outputs, because they can be very
large. The imports show the software. The first cells usually load the
inputs.

**WDL.** The files start with a `version` line. They hold a `workflow` that
calls (`call`) tasks (`task`). The `input` section of the workflow gives
`provides`. Each task has `input`, `command`, `output` and `runtime`
sections. `runtime` frequently names a container image.

**KNIME (`.knwf`).** A ZIP archive of XML node settings. It is difficult to
read in detail. Use the description, the README and each workflow image. If
the folder names in the archive show the node names, list them.

**Scripts and Makefiles.** Each script or target is a step. Tell the
researcher what each step reads and writes, and the order in which the steps
run.

**Unrecognised formats.** Fill the profile from the metadata and README
only. Set it to partial, and tell the researcher.
