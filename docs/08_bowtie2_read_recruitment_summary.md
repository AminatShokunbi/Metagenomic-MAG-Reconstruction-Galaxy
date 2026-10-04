# Bowtie2 read-recruitment summary

This document records evidence from the Galaxy Bowtie2 mapping-statistics collection generated after mapping the processed paired-end reads back to their corresponding sample-specific MEGAHIT assemblies. This is the **coverage-estimation/read-recruitment** Bowtie2 step used before `Calculate contig depths for MetaBAT2`; it is distinct from the earlier hg38 host-mapping demonstration.

## Mapping results

| Sample | Paired reads reported by Bowtie2 | Overall alignment rate |
|---|---:|---:|
| SRR18276513 | 626,956 | 89.06% |
| SRR18276515 | 621,064 | 88.84% |
| SRR18276516 | 504,835 | 93.51% |
| SRR18276520 | 581,241 | 89.92% |

The mapping-statistics files for SRR30026879 and SRR30026880 additionally contain warnings that some mates were only one nucleotide long after preprocessing. Bowtie2 skipped those individual extremely short mates. Their complete Galaxy mapping-statistics outputs should therefore be retained with the analysis record rather than reducing those files to a single summary percentage.

## Interpretation

The high read-recruitment rates observed for the four samples summarized above confirm that a large fraction of their processed reads could be mapped back to their corresponding assemblies. This provided BAM alignments from which contig coverage could be estimated for MetaBAT2.

However, a high overall mapping rate does **not** imply that the assembly is sufficiently contiguous for genome binning. In Version 1.0, QUAST showed that the assemblies contained too few sufficiently long contigs to support successful MetaBAT2 bin recovery. The mapping results therefore strengthen the interpretation that the zero-bin outcome was not simply caused by failure to perform read recruitment; assembly fragmentation remained the principal downstream constraint documented in this training dataset.

## Reproducibility note

The original Galaxy mapping-statistics collection contained six text outputs, one for each demonstration sample. Version 1.0 preserves this stage as evidence for the read-recruitment → BAM → contig-depth → MetaBAT2 workflow. Future datasets should be inspected both for alignment/coverage behavior and for assembly contiguity before interpreting binning performance.