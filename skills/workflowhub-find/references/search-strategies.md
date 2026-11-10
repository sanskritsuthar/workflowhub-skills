# Search strategies for WorkflowHub

## Change the angle, not only the words

The authors of a workflow write its title. Thus, the same analysis can have
very different names. Search from several angles:

| Angle | Example for "bacterial genomes from nanopore reads" |
|---|---|
| Researcher's own words | nanopore bacterial assembly |
| Broader or synonym term | long read assembly |
| Key tool name | flye |
| Controlled-vocabulary term | genome assembly |

## Synonym groups

Life sciences:
- RNA-seq, RNAseq, transcriptomics, differential expression
- 16S, amplicon, metabarcoding
- variant calling, SNP calling, genotyping
- metagenomics, taxonomic profiling, microbiome
- single-cell, scRNA-seq

Other fields:
- species distribution modelling, ecological niche modelling, SDM
- image segmentation, object detection, cell segmentation
- climate downscaling, bias correction
- molecular dynamics, MD simulation

Also try different spellings: with and without hyphens, and British and
American spellings ("modelling" and "modeling").

## Controlled vocabularies

In the life sciences, many WorkflowHub workflows have EDAM operations and
topics. Queries in EDAM terms, such as "Sequence assembly", "Variant
calling" or "Genome annotation", can find workflows with different words in
their titles. In other fields, try the terms from the methods sections of
that field, and standard method names.

## Using filters

- Search with free text first. Add filters only for constraints that the
  researcher gave.
- A filter removes workflows without a statement. If a search with filters
  gives no results, do the search again without the filters. Do this before
  you decide that nothing exists.
- Filter values are internal keys of WorkflowHub, not the names on its web
  pages. If a "list filter values" capability is available, use it. Do not
  guess.

## Stopping

Three rounds of search and change are usually enough. After that, report
what you tried and what you found, with the partial matches.

## Worked examples

**Nanopore assembly, Galaxy user.** "We have nanopore reads from bacterial
isolates and want to assemble genomes. We use Galaxy."

1. Queries: "nanopore assembly", "bacterial genome assembly", "long read
   assembly", "flye".
2. Filter by the workflow type Galaxy, because the researcher gave it.
3. If there are too few results, do the two broadest queries again without
   the filter. List good matches that are not Galaxy workflows separately,
   as alternatives.

**Species distributions, no stated platform.** "I want to model species
distributions from occurrence records and climate layers."

1. Queries: "species distribution model", "niche model", "SDM",
   "occurrence data".
2. Use no filters, because the researcher gave no platform or constraint.
3. In the shortlist, give the workflow system of each candidate. Then the
   researcher can tell you which systems they can use.
