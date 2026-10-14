# Reading a crate with the `rocrate` library

PyPI: <https://pypi.org/project/rocrate/> (tested with version 0.16.0, installed with `pip` into a virtual environment; loaded five WorkflowHub crates from metadata-only folders). It
reads and writes RO-Crates and handles the JSON-LD for you. This skill only
reads.

## Install

```bash
python3 -m venv <scratch>/rocrate-venv
<scratch>/rocrate-venv/bin/pip install rocrate
```

Install it in a virtual environment, in the scratch folder, and only if the
researcher agrees. Run Python with `-I` when reading files from a download, and
pass the crate path as an argument rather than running from inside the crate
folder.

## Loading

```python
from rocrate.rocrate import ROCrate

crate = ROCrate("path/to/crate.zip")      # a ZIP file
crate = ROCrate("path/to/folder")         # a folder, even one holding only ro-crate-metadata.json
```

Loading a ZIP reads the whole archive. For a big ZIP, extract only the
metadata file into a folder first, then load the folder.

## Reading

```python
root = crate.root_dataset
root.get("name"), root.get("description"), root.get("license")
root.get("datePublished"), root.get("version")

crate.mainEntity                    # main entity, or None
crate.mainEntity.get("programmingLanguage")
crate.creator, crate.publisher      # may be None or entities

for e in crate.get_entities():      # every entity
    e.id, type(e).__name__, e.get("name")

crate.data_entities                 # files and folders
crate.contextual_entities           # people, organisations, licenses, ...
crate.get_by_type("CreateAction")   # recorded runs
crate.test_suites                   # test suites, if any
crate.get("some-id")                # one entity by @id
```

Property values may be strings, lists or references to other entities.
Handle all three, and print values as read rather than assuming a type.

Attribute names and return types vary between versions. Check what the
installed version provides (`dir(crate)`) rather than guessing.

## What it does not do

- It doesn't download a crate from a URL. Do that with the retrieval steps in
  `SKILL.md`.
- Loading doesn't validate the crate. Use the RO-Crate validator
  (`roc-validator`) if the researcher asks whether a crate is valid.
- It reports what the metadata says. It doesn't check that listed files exist
  or that checksums match; compare with the folder contents if that matters.
