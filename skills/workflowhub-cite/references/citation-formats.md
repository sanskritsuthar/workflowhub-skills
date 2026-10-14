# Citation formats

Use plain text by default, BibTeX on request or for LaTeX projects, and a
named journal style if the researcher gives one. Whatever the format,
include: creators, year, title, version, publisher (WorkflowHub), resource
type, and DOI or versioned URL.

## Plain text

This follows the common pattern used for DOI-registered software and data.

With a DOI:

<Creators> (<Year>). <Title> (Version <n>) [Computational workflow].
WorkflowHub. https://doi.org/<DOI>

Without a DOI:

<Creators> (<Year>). <Title> (Version <n>) [Computational workflow].
WorkflowHub. https://workflowhub.eu/workflows/<id>?version=<n>
(accessed <YYYY-MM-DD>)

Notes:

- **Creators:** as listed in WorkflowHub, in the same order. Use family name
  and initials only if the full names are given; don't guess initials.
- **Organisational author:** if only a team is listed, use the team name.
- **Year:** the year this version was created or published. If it isn't
  available, write "n.d." and flag it.
- **DOI:** only from WorkflowHub's metadata. Never build one from a pattern.

## BibTeX

`@misc` works in every BibTeX setup. If the project uses biblatex, `@software`
is a better fit and accepts the same fields.

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

Without a DOI, omit `doi`, use the versioned WorkflowHub URL for `url`, and
add `urldate = {<YYYY-MM-DD>}`.

For an organisational author, wrap the name in an extra pair of braces so
BibTeX doesn't split it: `author = {{<Team name>}}`.

## Associated publications and tools

- Cite a linked publication in the journal's usual article style, using the
  details WorkflowHub gives. If details are incomplete, give what you have
  and mark the gaps.
- For tools used by the workflow, use references the workflow provides
  (such as a `CITATIONS.md` file). Otherwise list the tool name and version
  and note that a reference is needed.