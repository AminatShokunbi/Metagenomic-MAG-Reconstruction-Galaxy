# Galaxy Workflow

This directory contains the exported Galaxy workflow associated with the
metagenomic MAG reconstruction project.

## Workflow file

`MAG_RECOVERY_WORKFLOW.ga`

The workflow was exported directly from Galaxy and provides a machine-readable
record of the implemented analysis structure.

The current workflow includes:

1. Retrieval of sequencing reads from SRA using FasterQ Dump
2. Read-quality assessment using FastQC
3. Read preprocessing using Cutadapt
4. Aggregated quality-control assessment using MultiQC
5. Read mapping using Bowtie2 as an additional mapping/host-removal demonstration
6. Metagenomic assembly using MEGAHIT
7. Assembly-quality assessment using QUAST

## Important note on Bowtie2

Bowtie2 is included in the exported workflow as a separate read-mapping branch.

In this version of the workflow, MEGAHIT receives the Cutadapt-processed
paired-end reads directly. Bowtie2-derived unmapped reads are therefore not
the input to the MEGAHIT assembly represented by this workflow.

The Bowtie2 branch is retained to document the practical implementation of
read mapping and host-read identification/removal in Galaxy.

## Reproducibility

The `.ga` file preserves the Galaxy workflow structure, including tool
connections, tool versions, and workflow configuration.

The workflow will be updated as additional genome-resolved stages are
implemented, including genome binning, bin refinement, MAG quality assessment,
and taxonomic classification.
