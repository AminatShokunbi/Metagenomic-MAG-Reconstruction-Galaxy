# Quality Control and Adapter Trimming

## Raw Read Quality Assessment

Quality assessment of the paired-end metagenomic reads was performed using
FastQC (v0.12.1) in Galaxy.

FastQC was used to evaluate sequencing quality without modifying the raw reads.
The analysis included per-base sequence quality, per-sequence quality,
GC content, sequence length distribution, duplication levels,
overrepresented sequences, N content, and adapter content.

FastQC reports from all forward and reverse reads were summarized using
MultiQC to allow comparison across the six sequencing runs.

## Adapter Detection

MultiQC revealed adapter-associated sequences in the raw sequencing reads.

Two major sequence types were identified:

- Nextera transposase-associated sequences
- Poly-A sequences

The Nextera signal was substantial in some datasets, providing evidence that
adapter trimming was required before metagenomic assembly.

## Adapter Trimming

Adapter trimming was performed using Cutadapt in Galaxy.

The paired-end reads were processed together to preserve the relationship
between forward and reverse reads.

Cutadapt was configured to remove Nextera-associated adapter sequences.
Poly-A trimming was also enabled based on the poly-A signal observed during
the initial FastQC/MultiQC assessment.

Reads containing adapter sequences were trimmed rather than automatically
discarded.

## Post-Trimming Quality Control

Following Cutadapt processing, FastQC was repeated on the trimmed reads.

The resulting FastQC reports will be summarized with MultiQC and compared
with the original quality-control results to evaluate:

- reduction in Nextera-associated adapter content
- reduction in poly-A sequences
- changes in read-length distribution
- sequence quality after trimming
- overrepresented sequences
- retention of usable sequencing reads

Only after evaluating the post-trimming QC results will the reads be advanced
to metagenomic assembly.
