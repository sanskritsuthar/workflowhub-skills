# Reading a workflow RO-Crate

WorkflowHub packages workflows as RO-Crates: a zip archive containing the
workflow files plus `ro-crate-metadata.json`, a JSON-LD file describing them.
Read the metadata file first; it is usually small and tells you where
everything is.

## Reading the crate

Retrieve and read the crate with `workflowhub-inspect` (and the
`ro-crate-consume` skill behind it), not by hand: they use the `rocrate`
library, find the root dataset properly, and apply the size and rate limits.
Their reports tell you where things are:

- the **main workflow** (`mainEntity`) and its language
  (`programmingLanguage`); the main entity can be a `.ga`, `.cwl`, a Nextflow
  `main.nf` or `nextflow.config`, a Snakefile, a notebook or a script;
- declared **inputs and outputs** (`FormalParameter` entities), if any;
- declared **steps** (`HowToStep`), a **diagram** (`image`) and a **CWL
  description** (`subjectOf`), if any;
- other files: a README, test definitions, configuration and test data;
- **recorded runs** (`CreateAction`), for Run Crates.

Each of these is optional. Many valid crates declare no inputs, outputs or
steps. Don't treat that as an error.

## Tips

- File paths in `@id` may be URL-encoded (for example `%20` for a space).
  Decode them before looking for the file in the archive.
- An `@id` that is a full URL refers to a file that isn't in the archive.
  Mention it, but don't assume you can read it.
- If the crate includes a README, read it early. Authors often explain the
  inputs and intended use there more clearly than anywhere else.
- Test definitions and test data are a good sign for reuse. Note them in the
  explanation, because they give the researcher a safe first run.
- The root dataset's `@id` is usually `./` but can be an absolute URL. Find it
  through the metadata file's `about`.
- If there is no metadata file, or it lacks `mainEntity`, look for the file
  whose extension matches the workflow type (such as `.ga`, `.cwl`,
  `main.nf` or `Snakefile`).