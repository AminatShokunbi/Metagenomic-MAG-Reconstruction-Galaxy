# 07. Coverage-Informed Genome Binning with MetaBAT2

## Purpose

This stage extended the Galaxy workflow from assembly assessment into an initial genome-binning demonstration using MetaBAT2.

The objective was to document the practical steps required to move from assembled metagenomic contigs to coverage-informed genome binning, while preserving the limitations encountered with the demonstration datasets.

## Input Assemblies

The six sample-specific assemblies generated with MEGAHIT were used as the contig input for genome binning.

QUAST assessment showed that several assemblies were highly fragmented and contained limited sequence at lengths suitable for genome-resolved binning. This limitation was retained and documented rather than hidden by reporting only successful analyses.

## Read Recruitment for Coverage Estimation

The Cutadapt-processed paired-end reads were mapped back to their corresponding sample-specific MEGAHIT assemblies using Bowtie2.

This Bowtie2 operation is distinct from the earlier hg38 mapping demonstration documented in `04_bowtie2_mapping.md`.

Here, the purpose of Bowtie2 was to recruit the original quality-controlled reads against the contigs from which they were assembled so that contig coverage could be estimated for genome binning.

The sample-wise relationship was maintained:

```text
Sample 1 trimmed reads -> Sample 1 MEGAHIT assembly
Sample 2 trimmed reads -> Sample 2 MEGAHIT assembly
...
Sample 6 trimmed reads -> Sample 6 MEGAHIT assembly
```

Bowtie2 produced a six-member BAM collection. Mapping-statistics outputs were also retained to preserve provenance for the read-recruitment step.

## Calculation of Contig Depth

The BAM alignments were processed using the Galaxy tool **Calculate contig depths for MetaBAT2**.

This generated a six-member tabular depth-matrix collection containing the coverage information required by the Galaxy MetaBAT2 wrapper.

The resulting analysis path was therefore:

```text
Cutadapt-processed paired reads
            +
Sample-specific MEGAHIT contigs
            |
            v
         Bowtie2
            |
            v
       BAM alignments
            |
            v
Calculate contig depths for MetaBAT2
            |
            v
       Depth matrices
            |
            v
         MetaBAT2
```

## Initial MetaBAT2 Run

The first coverage-informed MetaBAT2 run used the Galaxy wrapper defaults, including a minimum contig length of **2,500 bp** for binning.

Key settings included:

| Parameter | Setting |
|---|---:|
| Coverage/depth information | Yes |
| Minimum contig size for binning | 2,500 bp |
| Minimum small-contig size | 1,000 bp |
| Percentage of good contigs (`maxP`) | 95 |
| Minimum edge score (`minS`) | 60 |
| Maximum edges per node | 200 |

No genome bins were recovered from the six demonstration assemblies under these settings.

## Assembly Characteristics Relevant to the Binning Outcome

Inspection of the QUAST reports demonstrated why bin recovery was difficult.

Examples included:

| Sample | Contigs >=500 bp | Contigs >=1,000 bp | Contigs >=5,000 bp | Largest contig | N50 |
|---|---:|---:|---:|---:|---:|
| SRR18276513 | 32 | 0 | 0 | 743 bp | 559 bp |
| SRR30026879 | 61 | 24 | 1 | 10,648 bp | 1,508 bp |
| SRR30026880 | 141 | 35 | 3 | 10,646 bp | 1,104 bp |

For SRR18276513, for example, the largest assembled contig was only 743 bp. Consequently, no contig from that assembly could satisfy a 2,500-bp minimum binning threshold.

The other inspected assemblies contained some longer contigs but remained highly fragmented and represented relatively limited assembled sequence for genome-resolved reconstruction.

## Training-Specific Relaxed Run

Because Version 1.0 is intended to document the practical workflow as well as its decision points, MetaBAT2 was also attempted using the lowest primary contig threshold permitted by the Galaxy wrapper used in this exercise.

The training-specific settings were:

| Parameter | Initial run | Relaxed training run |
|---|---:|---:|
| Minimum contig size for binning | 2,500 bp | 1,500 bp |
| Minimum small-contig size | 1,000 bp | 500 bp |
| `maxP` | 95 | 95 |
| `minS` | 60 | 60 |
| Maximum edges per node | 200 | 200 |

The 1,500-bp threshold was the lowest value accepted by this Galaxy MetaBAT2 wrapper.

The relaxed run was performed specifically to determine whether the constrained practice assemblies could support a hands-on demonstration of downstream bin processing. It was not intended to redefine a universal or production-quality MAG-reconstruction threshold.

No bins were recovered after the relaxed attempt.

## Interpretation

The absence of recovered bins is retained as an analysis result rather than treated as a reason to manufacture a successful output by progressively relaxing additional binning parameters.

Version 1.0 therefore demonstrates the complete computational transition from raw metagenomic reads through quality control, preprocessing, assembly, assembly assessment, read recruitment, coverage estimation, and an initial coverage-informed genome-binning attempt.

It does **not** claim successful MAG recovery from these six demonstration datasets.

The result illustrates an important principle of genome-resolved metagenomics: a reproducible computational pipeline cannot guarantee MAG recovery when the underlying assemblies contain insufficient genomic sequence or are too fragmented for reliable binning.

## Production Use

For larger or more deeply sequenced metagenomic datasets, the same general procedure can be applied while selecting binning parameters appropriate to the assembly characteristics and analysis objectives.

Recovered bins should not be considered validated MAGs solely because a binning algorithm produced them. Downstream quality assessment is required to evaluate completeness and contamination before biological interpretation.

## Version 2.0 Roadmap

Version 2.0 of this project will extend the workflow using a dataset suitable for demonstrating genome-resolved reconstruction beyond the initial binning stage.

The planned downstream architecture is:

```text
Metagenomic assembly
        |
        +----------------+----------------+
        |                |                |
        v                v                v
     MetaBAT2          MaxBin2          CONCOCT
        |                |                |
        +----------------+----------------+
                         |
                         v
                      DAS Tool
              multi-binner integration
                         |
                         v
                    Refined bins
                         |
                         v
                  CheckM / CheckM2
          completeness and contamination
                         |
                         v
                      GTDB-Tk
                taxonomic assignment
                         |
                         v
                 Final MAG catalogue
```

The Version 2.0 analysis will retain the distinction between candidate bins, quality-assessed MAGs, and taxonomically classified final genomes.

## Version 1.0 Endpoint

The MetaBAT2 binning attempt represents the endpoint of the Version 1.0 practical analysis.

Version 1.0 is therefore a reproducible demonstration of the workflow from public SRA read retrieval through assembly, coverage estimation, and the first genome-binning attempt, including transparent documentation of an unsuccessful MAG-recovery outcome.