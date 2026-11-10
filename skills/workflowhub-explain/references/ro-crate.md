# Reading a workflow RO-Crate

WorkflowHub puts workflows in RO-Crates. An RO-Crate is a ZIP archive with
the workflow files and `ro-crate-metadata.json`, a JSON-LD file that
describes them. Read the metadata file first. It is usually small, and it
tells you where all the parts are.

## Reading the crate

Get and read the crate with `workflowhub-inspect` (and the
`ro-crate-consume` skill that it uses). Do not read it yourself. These
skills use the `rocrate` library, find the root dataset correctly, and apply
the size and speed limits. Their reports tell you where things are:

- the **main workflow** (`mainEntity`) and its language
  (`programmingLanguage`). The main entity can be a `.ga`, a `.cwl`, a
  Nextflow `main.nf` or `nextflow.config`, a Snakefile, a notebook or a
  script;
- declared **inputs and outputs** (`FormalParameter` entities), if there are
  any;
- declared **steps** (`HowToStep`), a **diagram** (`image`) and a **CWL
  description** (`subjectOf`), if there are any;
- other files: a README, test definitions, configuration and test data;
- **recorded runs** (`CreateAction`), for Run Crates.

All of these are optional. Many valid crates declare no inputs, outputs or
steps. This is not an error.

## Tips

- File paths in `@id` can be URL-encoded (for example `%20` for a space).
  Decode them before you look for the file in the archive.
- An `@id` that is a full URL refers to a file that is not in the archive.
  Tell the researcher about it, but do not assume that you can read it.
- If the crate has a README, read it early. Authors frequently explain the
  inputs and the intended use there more clearly than in other places.
- Test definitions and test data are a good sign for reuse. Include them in
  the explanation, because they let the researcher do a safe first run.
- The `@id` of the root dataset is usually `./`, but it can be an absolute
  URL. Find it through the `about` of the metadata file.
- If there is no metadata file, or it has no `mainEntity`, look for the file
  whose extension agrees with the workflow type (for example `.ga`, `.cwl`,
  `main.nf` or `Snakefile`).
