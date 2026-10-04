# 02. Read Quality Control

## Purpose

Quality control was performed to evaluate the characteristics of the raw metagenomic sequencing reads before downstream preprocessing and assembly.

The primary tools used for this stage were:

- **FastQC v0.74**
- **MultiQC v1.35**

## FastQC

FastQC was used to assess the quality and sequence characteristics of the raw FASTQ datasets. FastQC is a diagnostic tool: it examines sequencing reads and generates quality-control metrics, but it does **not** modify, trim, or remove reads.

### How FastQC evaluates sequencing reads

FastQC examines several properties of sequencing data:

- **Per-base sequence quality:** evaluates the Phred quality-score distribution at each position along the reads.
- **Per-sequence quality scores:** examines the distribution of average quality scores across complete reads.
- **Per-base sequence content:** evaluates the proportions of A, T, G, and C at each position across the reads.
- **GC content:** examines the distribution of GC percentages across the dataset.
- **N content:** identifies positions containing ambiguous bases reported as `N` when the sequencer cannot confidently assign A, T, G, or C.
- **Sequence-length distribution:** evaluates the distribution of read lengths. Read length alone is not a measure of sequencing quality.
- **Sequence duplication:** identifies sequences occurring repeatedly. High duplication can result from technical processes such as PCR amplification or from genuinely abundant biological sequences and therefore should not automatically be interpreted as contamination.
- **Overrepresented sequences:** identifies sequences occurring more frequently than expected; these may represent adapters, contaminants, PCR artifacts, or abundant biological sequences.
- **Adapter content:** detects residual adapter-associated sequences. FastQC identifies these sequences but does not remove them.

FastQC reads the Phred quality scores already encoded in FASTQ files. Higher Phred scores correspond to greater confidence in the reported base calls.

FastQC warning and failure flags were treated as diagnostic indicators requiring interpretation rather than as automatic instructions to discard reads. This distinction is particularly important in metagenomic datasets, where biological complexity can influence metrics such as sequence duplication and GC-content distributions.

## Implementation in Galaxy

The project contained **six paired-end metagenomic samples**. FastQC was applied to the paired reads, producing six pairs of FastQC reports.

The FastQC output collection was then flattened into **12 individual FastQC outputs** before aggregation with MultiQC.

The implemented QC path was:

```text
6 paired-end samples
        |
        v
      FastQC
        |
        v
6 pairs of FastQC reports
        |
        v
Flatten FastQC output collection
        |
        v
12 individual FastQC outputs
        |
        v
      MultiQC
        |
        v
Combined QC report
```

## MultiQC

MultiQC was used to aggregate the individual FastQC results into a consolidated quality-control report. This enabled comparison of the sequencing-quality characteristics across the complete set of samples rather than requiring each FastQC report to be interpreted independently.

## Interpretation of the Initial QC

Inspection of the quality-control results identified sequence features requiring preprocessing before metagenomic assembly, including adapter-associated sequence content. Poly-A sequence content was also considered during preprocessing.

These observations informed the subsequent Cutadapt trimming strategy.

## Post-Trimming Quality Control

FastQC was repeated after Cutadapt preprocessing. The post-trimming FastQC outputs were again combined with MultiQC to evaluate how preprocessing affected the quality characteristics of the datasets.

The post-trimming assessment was used to verify the reduction of adapter-associated sequence content while monitoring other quality metrics and read-length distributions.

## Output of This Stage

The output of this stage consisted of FastQC reports and consolidated MultiQC assessments that informed the preprocessing decisions applied to the paired-end sequencing reads.

## Next Step

Based on the initial quality-control assessment, the reads were processed using **Cutadapt**.

See:

**[03 — Read Preprocessing](03_read_preprocessing.md)**
