# 04. Bowtie2 Read-Mapping Demonstration

## Purpose

Bowtie2 was incorporated into the Galaxy analysis as an additional read-mapping exercise to demonstrate how paired-end metagenomic reads can be aligned against a reference sequence.

This step was included to develop and document the practical use of Bowtie2 in Galaxy and to illustrate the principle by which host-associated reads can be identified during metagenomic preprocessing.

## Tool

**Bowtie2 v2.5.5** was used within Galaxy.

Bowtie2 is a short-read aligner that maps sequencing reads against a reference sequence or reference genome.

In a host-decontamination workflow, reads mapping to the host reference can be identified as potentially host-derived, while unmapped reads can be retained for downstream metagenomic analysis.

Conceptually:

```text
Cutadapt-processed paired reads
              |
              v
           Bowtie2
              |
              v
      Alignment to reference
          /         \
         /           \
   Mapped reads   Unmapped reads
   (reference-    (potentially
    associated)   non-host)
