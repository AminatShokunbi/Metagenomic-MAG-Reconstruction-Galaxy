# Bowtie2 Read-Recruitment Summary

This document records evidence from the Galaxy Bowtie2 mapping-statistics collection generated after the processed paired-end reads were mapped back to their corresponding sample-specific MEGAHIT assemblies. This was the **coverage-estimation/read-recruitment** Bowtie2 step performed before `Calculate contig depths for MetaBAT2`; it was distinct from the earlier hg38 host-mapping demonstration.

## Mapping Results

| Sample | Paired reads reported by Bowtie2 | Overall alignment rate |
|---|---:|---:|
| SRR18276513 | 626,956 | 89.06% |
| SRR18276515 | 621,064 | 88.84% |
| SRR18276516 | 504,835 | 93.51% |
| SRR18276520 | 581,241 | 89.92% |

The mapping-statistics files for SRR30026879 and SRR30026880 additionally contained warnings indicating that some mates were only one nucleotide long after preprocessing. Bowtie2 skipped those individual extremely short mates. Their complete Galaxy mapping-statistics outputs were therefore retained with the analysis record rather than being reduced to a single summary percentage.

## Interpretation

The read-recruitment rates observed for the four samples summarized above showed that a large fraction of their processed reads mapped back to their corresponding assemblies. The resulting BAM alignments provided the coverage information required for subsequent contig-depth estimation and MetaBAT2 binning.

However, a high overall mapping rate was not interpreted as evidence that an assembly was sufficiently contiguous for genome binning. In Version 1.0, QUAST showed that the assemblies contained relatively few sufficiently long contigs, and MetaBAT2 subsequently recovered no bins. Taken together, these observations indicated that successful read recruitment did not compensate for the limited contiguity of the demonstration assemblies. The zero-bin outcome was therefore interpreted in the context of the documented assembly fragmentation rather than being attributed simply to failure of the read-recruitment step.

## Reproducibility Note

The original Galaxy mapping-statistics collection contained six text outputs, one for each demonstration sample. Version 1.0 retained this stage as evidence for the read-recruitment → BAM → contig-depth → MetaBAT2 analysis path.

For subsequent datasets, both alignment/coverage behavior and assembly contiguity should be evaluated before binning performance is interpreted.