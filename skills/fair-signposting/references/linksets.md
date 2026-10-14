# Linksets (Level 2)

A linkset is a separate document that lists the typed links for several
resources at once. It follows RFC 9264 and has two serializations:

- `application/linkset+json`: JSON, easiest to read
- `application/linkset`: plain text in `Link` header syntax

It is found through a link with `rel="linkset"` and a `type` attribute on the
landing page (and recommended on each content and metadata resource).

## JSON structure

```json
{
  "linkset": [
    {
      "anchor": "https://example.org/page/7507",
      "cite-as": [{"href": "https://doi.org/10.1234/56789"}],
      "describedby": [{"href": "https://example.org/meta/7507", "type": "application/ld+json"}],
      "item": [{"href": "https://example.org/file/7507/1", "type": "application/pdf"}]
    },
    {
      "anchor": "https://example.org/file/7507/1",
      "collection": [{"href": "https://example.org/page/7507", "type": "text/html"}]
    }
  ]
}
```

## Reading rules

- Each object in `linkset` has an `anchor`, the resource that is the origin of
  its links. Read links only under the anchor you care about.
- Link relation names are keys; each holds an array of targets with `href` and
  usually `type`.
- Targets and anchors are absolute URIs.
- For the landing page, the linkset repeats the Level 1 relations and lists
  every `item`. For files, `collection` points back to the landing page; for
  metadata documents, `describes` does.
- Other relations may appear. Report them if they matter; don't invent
  meanings.
