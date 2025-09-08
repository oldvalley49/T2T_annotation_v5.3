# T2T-CHM13 Gene Annotation v5.3

## Motivation

Using LiftOff, we projected genes from the latest MANE gene annotation (v1.4) on GRCh38 onto the T2T-CHM13 v2.0 assembly. We identified 256 distinct MANE genes (296 including additional copies) that are missing from the v5.2 gene annotation. See r`esults/mapped_but_missing.csv` for the list of MANE genes that could be mapped but are missing from the CHM13 v5.2 gene annotation. 

**The goal of this v5.3 update was to add as many of the missing MANE genes as possible.**

## Results

We applied a number of procedures to consider adding a gene to the existing v5.2 annotation, at times involving the removal of existing ones (i.e., replacing). As a result, we added 105 new MANE genes (10 manually curated) while removing 62 existing ones. See `results/added_genes.curated.w_itg.csv` and `results/removed_genes.curated.csv` for the list of genes added and removed in v5.3 annotation (gene symbol shown in the first column and gene ID in the second).

See below for a detailed breakdown of the number of genes in each biotype category in the previous v5.2 versus v5.3 gene annotations.

| Gene biotype | v5.2 | v5.3 |
|----------|----------|----------|
| Protein coding   | 20,571    | 20,644 (+73)    |
| LncRNA    | 18,389     | 18,385 (-4)    |
| Pseudogene    | 17,283  | 17,248 (-35)   |
| Other	| 5,068 | 5,064 (-4)	|
| **Total** | 61,312 | 61,342 (+30) |

## Methods

We first identified 4 genes that were mapped to an intergenic region (DDTL, SMN-AS1, SMN-AS1_1, and LY6S) and added them. The remaining 292 genes were overlapping gene(s) already in v5.2.

We found that about half, 155 out of 292, were not truly absent, but rather synonyms of genes already in v5.2. We define gene A as a synonym of gene B when both symbols refer to the same gene object. For example, the gene previously known as ODF3B is now registered as CIMAP1B in the HGNC (HUGO Gene Nomenclature Committee) database, making ODF3B and CIMAP3B synonymous. To address this, we compiled a list of gene synonyms from two sources (`TODO`), linking each gene symbol to its set of synonyms (see `results/synonym_referencemap.json`). We then updated the gene symbols in v5.2 to match those used in the MANE v1.4, which typically reflects the most current HGNC-approved nomenclature. To be more specific, if a newly mapped MANE gene overlaps another gene in v5.2 and they are synonymous, then we updated the existing gene symbol. Note that if there are multiple copies of a gene whose symbol is updated, then all copies were updated.

Nearly all updates involved revising the attributes column of the GFF file. If there are multiple copies of a gene being updated. However, for a few of these instances, an update in gene symbols was accompanied by a gene type update as well, indicating a more significant update to the known information on a gene and its identity. For example, SSU72P2 used to be characterized as a pseudogene but is now approved as SSU72L2, an update also reflected in MANE. We counted 17 gene biotype updates from non-protein-coding to protein-coding (16 from pseudogene, 1 from lncRNA). In these cases, we also transferred transcript and coding sequence (CDS) annotations from MANE, while removing the existing ones in v5.2.

For the remaining 137 genes, we established rules to determine whether to add the newly mapped MANE gene or to remove the existing overlapping one in v5.2 or both (i.e., a replacement). 

We first grouped the overlaps by gene symbols. If there is more than one overlap detected for a group, then it was flagged for manual inspection (25 distinct genes, 52 including copies). More than one overlap may be reported if there are multiple copies of a distinct gene and/or a single copy has multiple overlaps with existing ones in v5.2. Both scenarios require special care to ensure copy numbers are accurately annotated, and if multiple overlaps pose competing conditions, to decide whether to discard the existing.
The remaining are genes mapped as a single copy with an overlap with a non-synonymous gene in v5.2. Note that additional overlaps found for genes previously updated through synonyms matching were ignored, as these overlaps are inherited from v5.2. We applied the following logic (presented as pseudocode, see `notebooks/edit.ipynb` for implementation details) to determine which actions to take:

```
gene_A <- new gene projected from MANE v1.4
gene_B <- old gene in v5.2 overlapping with gene_A

IF gene_A and gene_B are OVERLAPPING in MANE v1.4:
    THEN ADD gene_A

ELSE IF gene_A has a broken ORF (invalid start and/or stop, premature stop codon):
    THEN KEEP gene_B

ELSE:
	  IF gene_B not in MANE v1.4:
		    REPLACE gene_B with gene_A

	  ELSE:
		    IF gene_B has no valid ORFs AND/OR gene_A encodes a longer protein than gene_B:
                REPLACE gene_B with gene_A
```
The code used for the production of the v5.3 gene annotation is available under the directories `scripts/` and `notebooks/`.

## Contact

Should you have any questions/suggestions regarding the update, please feel free to either open a GitHub issue or contact Tomo Furutani (<tfuruta1@jh.edu>), Hayden Ji (<hji20@jh.edu>), or Celine Hoh (<choh1@jhu.edu>) directly.
