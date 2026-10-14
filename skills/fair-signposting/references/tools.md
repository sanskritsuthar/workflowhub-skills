# Tools for FAIR Signposting

## curl (always works)

```bash
# Level 1: the Link headers (HEAD), following redirects
curl -sIL "https://example.org/page/7507"

# If HEAD is rejected
curl -sL -D - -o /dev/null "https://example.org/page/7507"

# Level 2: a linkset
curl -sL -H "Accept: application/linkset+json" "<linkset URL>"

# A metadata document by its type
curl -sL -H "Accept: application/ld+json" "<describedby URL>"
```

Use only HEAD and GET.

## Python library `signposting` (optional)

PyPI: <https://pypi.org/project/signposting/>. Alpha status, Apache-2.0.
Python 3.7 or later. It finds and sorts links but does not fetch metadata or
content.

```bash
pip install signposting          # use a virtual environment
signposting <url>                # HTTP and HTML links combined
signposting --http <url>         # Link headers only
signposting --html <url>         # <link> elements only
signposting --linkset <url>      # linksets only
signposting -D <url>             # report each method separately
signposting -c <url>             # include links from other anchors
signposting -e <url>             # include extension relation types
```

Sample output (a WorkflowHub record resolved through its DOI):

```
Signposting for https://workflowhub.eu/workflows/624?version=1
CiteAs: <https://doi.org/10.48546/workflowhub.workflow.624.1>
DescribedBy: <https://workflowhub.eu/workflows/624?version=1> application/vnd.datacite.datacite+xml
             <https://workflowhub.eu/workflows/624?version=1> application/ld+json
Item: <https://workflowhub.eu/workflows/624/ro_crate?version=1> application/zip
```

Python API: `find_signposting_http(url)`, `find_signposting_html(url)` and
`find_signposting_linkset(url)` return a `Signposting` object with sets such as
`authors`, `cite_as`, `describedby` and `items`. Check attribute names in the
installed version before relying on them.

Install the library only if the researcher agrees; curl is enough.

## Known behaviour (tested 2026-10-14)

- `workflowhub.eu` serves `describedby` (DataCite XML and Bioschemas JSON-LD)
  and `item` (RO-Crate) in the landing page's Link header, and adds `cite-as`
  when the version has a minted DOI (seen for workflow 624, version 1, where
  the DOI resolves). It offers no linkset.
- A DOI shown in DataCite XML for `workflowhub.eu/workflows/248?version=7`
  (`10.48546/workflowhub.workflow.248.7`) returned 404 at doi.org: it is not
  registered, and the Link header had no `cite-as`. This is why step 5 resolves identifiers.
