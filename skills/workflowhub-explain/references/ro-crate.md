# Reading a workflow RO-Crate

WorkflowHub packages workflows as RO-Crates: a zip archive containing the
workflow files plus `ro-crate-metadata.json`, a JSON-LD file describing them.
Read the metadata file first; it is usually small and tells you where
everything is.

## Structure

The metadata file has an `@graph` list of entities, each with an `@id`.
Useful entities:

- **The root dataset** (`"@id": "./"`): the crate's name, description and
  license. Its `mainEntity` points to the main workflow file.
- **The main workflow** (the entity `mainEntity` points to): typically has
  types including `File`, `SoftwareSourceCode` and `ComputationalWorkflow`.
  Look for:
  - `programmingLanguage`: points to an entity naming the workflow system,
    such as Galaxy, CWL, Nextflow or Snakemake.
  - `input` and `output`: point to `FormalParameter` entities with a name,
    description, format and whether a value is required.
  - `creator`, `version`, `license`, `url`.
  - `image`: a diagram of the workflow, if one was provided.
  - `subjectOf`: sometimes an abstract CWL description of the workflow.
- **Other files** listed in the root's `hasPart`, such as a README, test
  definitions, configuration files or test data.

A minimal example:

```json
{
  "@graph": [
    { "@id": "./", "@type": "Dataset",
      "mainEntity": { "@id": "workflow.ga" } },
    { "@id": "workflow.ga",
      "@type": ["File", "SoftwareSourceCode", "ComputationalWorkflow"],
      "programmingLanguage": { "@id": "#galaxy" },
      "input": [{ "@id": "#input-reads" }] }
  ]
}
```

## Tips

- File paths in `@id` may be URL-encoded (for example `%20` for a space).
  Decode them before looking for the file in the archive.
- An `@id` that is a full URL refers to a file that isn't in the archive.
  Mention it, but don't assume you can read it.
- If the crate includes a README, read it early. Authors often explain the
  inputs and intended use there more clearly than anywhere else.
- Test definitions and test data are a good sign for reuse. Note them in the
  explanation, because they give the researcher a safe first run.
- If there is no metadata file, or it lacks `mainEntity`, look for the file
  whose extension matches the workflow type (such as `.ga`, `.cwl`,
  `main.nf` or `Snakefile`).