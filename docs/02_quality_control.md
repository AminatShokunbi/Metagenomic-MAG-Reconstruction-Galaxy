# 02. Read Quality Control

## Purpose

Quality control was performed to evaluate the characteristics of the raw metagenomic sequencing reads before assembly.

The primary tools used for this stage were:

- **FastQC v0.74**
- **MultiQC v1.35**

## FastQC

FastQC was used to examine the quality characteristics of the sequencing reads.

FastQC evaluates multiple properties of FASTQ datasets, including:

- per-base sequence quality
- per-sequence quality scores
- sequence length distribution
- per-base sequence content
- GC content
- sequence duplication
- overrepresented sequences
- adapter content

The FastQC reports were inspected to identify features requiring preprocessing before metagenomic assembly.

## MultiQC

Because multiple samples and paired-end read files were being analyzed, **MultiQC** was used to aggregate individual quality-control results.

Rather than evaluating every FastQC report independently, MultiQC provides a consolidated overview that facilitates comparison of quality metrics across the complete dataset collection.

Conceptually:

```text
Paired FASTQ datasets
        |
        v
      FastQC
        |
        v
Individual QC reports
        |
        v
      MultiQC
        |
        v
Combined QC assessment
