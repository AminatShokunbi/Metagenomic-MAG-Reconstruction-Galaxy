# 03. Read Preprocessing

## Purpose

Following the initial FastQC and MultiQC assessment, the paired-end metagenomic reads were preprocessed to remove sequence features identified during quality control that could interfere with downstream analysis.

Read preprocessing was performed using **Cutadapt v5.2** in Galaxy.

## Basis for Read Preprocessing

The initial FastQC reports were summarized using MultiQC to evaluate the six metagenomic sequencing datasets collectively.

Inspection of the quality-control results identified adapter-associated sequence content, including:

- Nextera-associated sequences
- poly-A sequence content

These observations were used to guide the preprocessing strategy.

Importantly, preprocessing decisions were based on inspection of the underlying quality-control metrics rather than simply removing reads because individual FastQC modules produced warning or failure flags.

## Adapter and Poly-A Trimming

Cutadapt was used to process the paired-end sequencing reads.

The paired reads were processed together so that the relationship between the forward (R1) and reverse (R2) reads was maintained during preprocessing.

Cutadapt was configured to trim matching adapter-associated sequence content rather than automatically discarding every read containing an adapter match.

Poly-A/poly-T sequence trimming was also incorporated based on the sequence-content patterns observed during the initial quality-control assessment.

Conceptually:

```text
Raw paired-end reads
        |
        v
Initial FastQC
        |
        v
MultiQC assessment
        |
        v
Adapter/poly-A identification
        |
        v
Cutadapt
        |
        v
Trimmed paired-end reads
