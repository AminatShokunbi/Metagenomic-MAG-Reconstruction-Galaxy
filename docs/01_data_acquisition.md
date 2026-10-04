# 01. Metagenomic Data Acquisition

## Purpose

The first stage of the MAG reconstruction workflow was the acquisition of raw metagenomic sequencing reads from the NCBI Sequence Read Archive (SRA).

Six publicly available metagenomic datasets were selected for the reconstruction exercise.

## SRA Accessions

| Sample | SRA accession |
|---|---|
| 1 | SRR18276513 |
| 2 | SRR30026880 |
| 3 | SRR30026879 |
| 4 | SRR18276515 |
| 5 | SRR18276516 |
| 6 | SRR18276520 |

## Read Retrieval in Galaxy

Sequence data were retrieved within Galaxy using **FasterQ Dump (v3.1.1)**.

FasterQ Dump retrieves sequencing data associated with SRA run accessions and converts the archived SRA data into FASTQ-formatted sequencing reads that can be used by downstream bioinformatic tools.

Each SRA accession was processed to obtain the sequencing reads required for the reconstruction workflow.

## Paired-End Read Organization

The datasets were handled as paired-end sequencing data.

For each sequencing run, the corresponding forward and reverse reads were organized as paired datasets for downstream processing.

Conceptually:

```text
SRA accession
      |
      v
FasterQ Dump
      |
      v
Paired-end sequencing reads
      |
      +---- R1 (forward reads)
      |
      +---- R2 (reverse reads)
