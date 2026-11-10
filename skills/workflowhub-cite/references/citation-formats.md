# Citation formats

Use plain text as the default. Use BibTeX if the researcher asks for it, or
for LaTeX projects. If the researcher names a journal style, use that style.
Each format must include: creators, year, title, version, publisher
(WorkflowHub), resource type, and DOI or versioned URL.

## Plain text

This format follows the usual pattern for software and data that are
registered with a DOI.

With a DOI:

<Creators> (<Year>). <Title> (Version <n>) [Computational workflow].
WorkflowHub. https://doi.org/<DOI>

Without a DOI:

<Creators> (<Year>). <Title> (Version <n>) [Computational workflow].
WorkflowHub. https://workflowhub.eu/workflows/<id>?version=<n>
(accessed <YYYY-MM-DD>)

Notes:

- **Creators:** as WorkflowHub lists them, in the same order. Use the family
  name and initials only if WorkflowHub gives the full names. Do not guess
  initials.
- **Organisational author:** if WorkflowHub lists only a team, use the team
  name.
- **Year:** the year when WorkflowHub made or published this version. If the
  year is not available, write "n.d." and tell the researcher.
- **DOI:** take it only from the metadata of WorkflowHub. Never make a DOI
  from a pattern.

## BibTeX

`@misc` works in all BibTeX setups. If the project uses biblatex,
`@software` is better. It accepts the same fields.

```bibtex
@misc{workflowhub_<id>_v<n>,
  author    = {<Creator One> and <Creator Two>},
  title     = {<Title>},
  year      = {<Year>},
  version   = {<n>},
  publisher = {WorkflowHub},
  doi       = {<DOI>},
  url       = {https://doi.org/<DOI>},
  note      = {Computational workflow (<workflow type>)}
}
```

Without a DOI:

- Do not include `doi`.
- Use the versioned WorkflowHub URL for `url`.
- Add `urldate = {<YYYY-MM-DD>}`.

For an organisational author, put the name in a second pair of braces. Then
BibTeX does not divide the name: `author = {{<Team name>}}`.

## Associated publications and tools

- Cite a linked publication in the usual article style of the journal. Use
  the details that WorkflowHub gives. If the details are incomplete, give
  the details that you have, and mark the gaps.
- For tools that the workflow uses, use the references that the workflow
  gives (for example a `CITATIONS.md` file). If there are none, list the
  name and version of the tool. Tell the researcher that a reference is
  necessary.
