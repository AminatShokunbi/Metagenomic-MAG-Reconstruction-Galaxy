# 06. Assembly Quality Assessment

## Purpose

Following metagenomic assembly with MEGAHIT, assembly quality was evaluated using **QUAST v5.3.0** in Galaxy.

Assembly assessment was performed before genome binning because successful execution of an assembler does not necessarily indicate that the resulting contigs are sufficiently contiguous for reliable genome reconstruction.

## QUAST Assessment

QUAST was used to summarize characteristics of the MEGAHIT assemblies, including:

- total number of contigs
- contig-length distributions
- largest contig
- N50
- GC content

These metrics were used to evaluate assembly fragmentation and contiguity.

## Assembly Results

The QUAST results indicated substantial fragmentation in some of the assemblies.

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

These results indicated limited assembly contiguity for the two datasets summarized above.

In particular, the relatively small largest-contig sizes and low N50 values were consistent with highly fragmented assemblies.

This limitation was relevant to downstream MAG reconstruction because genome binning depends on assembled contigs, and extensive fragmentation can reduce the amount of sequence information available for recovering coherent genome bins.

However, assembly statistics alone were not treated as sufficient to determine whether useful genome bins could be recovered. The assemblies were therefore retained for the subsequent hands-on genome-binning stage so that the complete analytical decision process could be documented.

The subsequent MetaBAT2 analyses recovered no candidate genome bins, including after the documented training-specific reduction of the minimum contig threshold. Accordingly, no output from these assemblies was interpreted as a MAG. The complete binning outcome is documented in [`07_metabat2_binning_attempt.md`](07_metabat2_binning_attempt.md).

## Important Distinction

At the assembly-quality stage, the outputs were **metagenomic assemblies**, not MAGs.

The analytical progression was:

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
```

In Version 1.0, the workflow reached the genome-binning attempt, but no candidate bins were recovered; consequently, MAG-quality assessment was not performed.