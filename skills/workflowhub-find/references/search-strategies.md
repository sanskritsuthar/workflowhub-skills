# Search strategies for WorkflowHub

## Vary the angle, not just the wording

Workflow titles are written by their authors, so the same analysis can appear
under very different names. Cover several angles:

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

Also try spelling variants, such as hyphenated and unhyphenated forms and
British and American spellings ("modelling" and "modeling").

## Controlled vocabularies

In the life sciences, many WorkflowHub workflows are annotated with EDAM
operations and topics. EDAM-style queries such as "Sequence assembly",
"Variant calling" or "Genome annotation" can surface workflows whose titles
use different wording. In other fields, try the terms used in that field's
methods sections and standard method names.

## Using filters

- Search with free text first; add filters only for stated constraints.
- Filters exclude silently. If a filtered search returns nothing, rerun it
  without filters before concluding that nothing exists.
- Filter values are WorkflowHub's internal keys, not display names. If a
  "list filter values" capability is available, use it rather than guessing.

## Stopping

Three rounds of searching and adjusting is usually enough. Beyond that,
report what was tried and what was found, including partial matches.

## Worked examples

**Nanopore assembly, Galaxy user.** "We have nanopore reads from bacterial
isolates and want to assemble genomes. We use Galaxy."

1. Queries: "nanopore assembly", "bacterial genome assembly", "long read
   assembly", "flye".
2. Filter by workflow type Galaxy, because the researcher stated it.
3. If there are too few results, rerun the two broadest queries without the
   filter, and list strong non-Galaxy matches separately as alternatives.

**Species distributions, no stated platform.** "I want to model species
distributions from occurrence records and climate layers."

1. Queries: "species distribution model", "niche model", "SDM",
   "occurrence data".
2. No filters, because no platform or constraint was stated.
3. In the shortlist, note each candidate's workflow system so the researcher
   can say which they can run.