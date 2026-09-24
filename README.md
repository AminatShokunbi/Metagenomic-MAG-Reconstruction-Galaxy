# Reconstruction of Metagenome-Assembled Genomes from Raw Reads Using Galaxy

This repository documents a reproducible workflow for reconstructing
metagenome-assembled genomes (MAGs) from raw paired-end metagenomic
sequencing reads using the Galaxy platform.

## Objective

The objective of this project is to independently perform the complete
bioinformatic workflow from raw sequencing reads to taxonomically
classified metagenome-assembled genomes (MAGs).

## Workflow

Raw paired-end reads  
↓  
Quality control  
↓  
Read trimming and filtering  
↓  
Host-read removal  
↓  
Metagenomic assembly  
↓  
Genome binning  
↓  
Bin refinement  
↓  
MAG quality assessment  
↓  
Taxonomic classification  
↓  
Final MAGs  

## Platform

**Galaxy**

## Major Tools

- FastQC
- fastp
- Bowtie2
- MEGAHIT
- MetaBAT2
- MaxBin2
- CONCOCT
- DAS Tool
- CheckM / CheckM2
- GTDB-Tk

## Project Status

🚧 **Work in progress**

The workflow and associated results will be added progressively as each
stage of the analysis is completed.


## Current Progress

- [x] SRA accession selection
- [x] Raw read retrieval using Galaxy
- [x] Paired-end read organization
- [x] Initial FastQC
- [x] MultiQC summary
- [x] Adapter identification
- [x] Adapter and poly-A trimming with Cutadapt
- [ ] Post-trimming FastQC and MultiQC evaluation
- [ ] Metagenomic assembly
- [ ] Genome binning
- [ ] Bin refinement
- [ ] MAG quality assessment
- [ ] Taxonomic classification
