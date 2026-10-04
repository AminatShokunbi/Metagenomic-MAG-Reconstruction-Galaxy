# Galaxy Workflow

This directory contains the exported Galaxy workflow associated with Version 1.0 of the metagenomic MAG-reconstruction project.

## Workflow File

`MAG_RECOVERY_WORKFLOW.ga`

The workflow was exported directly from Galaxy and provides a machine-readable record of the analysis structure captured at the time of export.

The archived workflow includes:

1. retrieval of sequencing reads from SRA using FasterQ Dump;
2. read-quality assessment using FastQC;
3. read preprocessing using Cutadapt;
4. aggregated quality-control assessment using MultiQC;
5. read mapping using Bowtie2 as an additional mapping/host-removal demonstration;
6. metagenomic assembly using MEGAHIT; and
7. assembly-quality assessment using QUAST.

## Important Note on Bowtie2

Bowtie2 was included in the exported workflow as a separate read-mapping branch.

In this workflow export, MEGAHIT received the Cutadapt-processed paired-end reads directly. Bowtie2-derived unmapped reads were therefore not used as the input to the MEGAHIT assembly represented by this file.

The Bowtie2 branch was retained to document the practical implementation of read mapping and host-read identification/removal in Galaxy.

## Relationship to the Complete Version 1.0 Analysis

The exported `.ga` file should not be interpreted as containing every operation subsequently completed during Version 1.0. After the workflow export represented here, the practical analysis was extended through sample-specific read recruitment against the MEGAHIT assemblies, BAM generation, contig-depth estimation, and MetaBAT2 genome-binning attempts.

Those later operations and their settings are documented in:

- [`../docs/07_metabat2_binning_attempt.md`](../docs/07_metabat2_binning_attempt.md)
- [`../docs/08_bowtie2_read_recruitment_summary.md`](../docs/08_bowtie2_read_recruitment_summary.md)

No candidate genome bins were recovered in Version 1.0, so DAS Tool refinement, CheckM/CheckM2 quality assessment, and GTDB-Tk classification were not performed.

## Reproducibility

The `.ga` file preserves the Galaxy tool graph, connections, tool versions, and configuration captured in the exported workflow. The stage-specific documentation should be read together with this file to reconstruct the complete Version 1.0 analytical record.

Version 2.0 is planned as a separate extension using a metagenomic dataset suitable for demonstrating multi-binner genome reconstruction, bin refinement, MAG-quality assessment, and taxonomic classification.