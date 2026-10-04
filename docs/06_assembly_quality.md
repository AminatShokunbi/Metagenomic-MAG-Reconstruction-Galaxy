# 06. Assembly Quality Assessment

## Purpose

Following metagenomic assembly with MEGAHIT, assembly quality was evaluated using **QUAST v5.3.0** in Galaxy.

Assembly assessment is necessary before genome binning because successful execution of an assembler does not necessarily indicate that the resulting contigs are sufficiently contiguous for reliable genome reconstruction.

## QUAST Assessment

QUAST was used to summarize characteristics of the MEGAHIT assemblies, including:

- total number of contigs
- contig-length distributions
- largest contig
- N50
- GC content

These metrics provide information about assembly fragmentation and contiguity.

## Preliminary Assembly Results

The preliminary QUAST results indicated substantial fragmentation in some of the assemblies.

### SRR18276513

The assembly produced:

| Metric | Result |
|---|---:|
| Total contigs | 656 |
| Contigs ≥500 bp | 32 |
| Contigs ≥1,000 bp | 0 |
| Largest contig | 743 bp |
| N50 | 559 bp |
| GC content | 44.44% |

### SRR18276515

The assembly produced:

| Metric | Result |
|---|---:|
| Total contigs | 264 |
| Contigs ≥500 bp | 25 |
| Contigs ≥1,000 bp | 1 |
| Largest contig | 1,696 bp |
| N50 | 582 bp |
| GC content | 43.51% |

## Interpretation

These preliminary results indicate limited assembly contiguity for the two datasets summarized above.

In particular, the relatively small largest-contig sizes and low N50 values indicate highly fragmented assemblies.

This is important for downstream MAG reconstruction because genome binning relies on assembled contigs, and highly fragmented assemblies can reduce the amount of genomic information available for recovering coherent genome bins.

However, assembly statistics alone do not determine whether useful genome bins can be recovered.

Because this project is also intended to document the complete practical workflow from raw reads toward MAG reconstruction, these assemblies are being retained for downstream analysis.

Any genome bins recovered from these assemblies will therefore require critical evaluation using MAG-quality metrics before they can be interpreted as reconstructed genomes.

## Important Distinction

At this stage of the workflow, the outputs are **metagenomic assemblies**, not MAGs.

The progression is:

```text
Sequencing reads
       |
       v
    MEGAHIT
       |
       v
     Contigs
       |
       v
     QUAST
       |
       v
Assembly quality assessment
       |
       v
 Genome binning
       |
       v
Candidate genome bins
       |
       v
MAG quality assessment
