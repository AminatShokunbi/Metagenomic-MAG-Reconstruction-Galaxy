# 05. Metagenomic Assembly

## Purpose

Following read preprocessing and post-trimming quality assessment, the quality-controlled paired-end metagenomic reads were assembled into longer contiguous sequences (contigs).

Metagenomic assembly was performed using **MEGAHIT v1.2.9** in Galaxy.

## Why Metagenomic Assembly Is Required

Short-read sequencing generates large numbers of relatively short DNA sequences. Metagenomic assembly attempts to reconstruct longer genomic fragments by identifying overlaps among these reads.

The resulting contigs provide the sequence framework required for downstream genome-resolved analyses, including genome binning and reconstruction of metagenome-assembled genomes (MAGs).

Conceptually:

```text
Quality-controlled paired-end reads
                |
                v
             MEGAHIT
                |
                v
       Metagenomic contigs
                |
                v
       Assembly assessment
                |
                v
          Genome binning
