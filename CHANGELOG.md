# Changelog

All notable changes to this project are documented in this file.

## [1.0.0] — 2026-10-04

### Release scope

Version 1.0 is the first stable documented release of the Galaxy-based metagenomic workflow. It demonstrates the computational path from public raw sequencing reads through an initial coverage-informed genome-binning attempt.

### Added

- Six public SRA demonstration datasets.
- FasterQ Dump data-retrieval workflow.
- FastQC read-quality assessment.
- MultiQC aggregation of QC reports.
- Cutadapt read preprocessing and post-trimming QC.
- Bowtie2 host-mapping demonstration against hg38.
- MEGAHIT sample-wise metagenomic assembly.
- QUAST assembly-quality assessment.
- Bowtie2 recruitment of trimmed reads to corresponding MEGAHIT assemblies.
- BAM-based contig-depth calculation for MetaBAT2.
- Coverage-informed MetaBAT2 genome-binning attempt.
- Training-specific MetaBAT2 rerun using the lowest primary contig threshold accepted by the Galaxy wrapper used in the exercise.
- Detailed stage-specific documentation under `docs/`.
- `CITATION.cff` citation metadata.
- MIT `LICENSE`.

### MetaBAT2 outcome

The initial MetaBAT2 analysis used a minimum contig size of 2,500 bp. No bins were recovered.

Because the QUAST reports showed substantial assembly fragmentation, a second training-specific attempt used a 1,500-bp minimum contig size and a 500-bp minimum small-contig setting. No bins were recovered from this attempt either.

Version 1.0 therefore ends at the initial genome-binning stage. It does not represent the MetaBAT2 outputs as MAGs and does not claim successful MAG recovery.

### Reproducibility decision

The zero-bin outcome was retained rather than progressively relaxing additional parameters to force bin production. This preserves the distinction between successful execution of a bioinformatic workflow and successful recovery of biologically meaningful genome bins.

### Known limitations

- The six demonstration assemblies are small and/or fragmented for genome-resolved reconstruction.
- The earlier hg38 Bowtie2 step demonstrates host mapping but was not used to generate the reads assembled by MEGAHIT in this release.
- No candidate bins were available for DAS Tool refinement.
- CheckM/CheckM2 quality assessment was not performed because no candidate MAGs were recovered.
- GTDB-Tk classification of recovered MAGs was therefore not performed.

## Planned [2.0.0]

Version 2.0 will use data suitable for extending the hands-on workflow beyond initial binning.

Planned additions include:

- explicit validation of nucleotide inputs before assembly/binning;
- reconstruction from appropriate metagenomic nucleotide data;
- MetaBAT2 binning;
- MaxBin2 binning;
- CONCOCT binning where appropriate;
- DAS Tool integration and refinement of multi-binner results;
- CheckM/CheckM2 assessment of completeness and contamination;
- GTDB-Tk taxonomic classification;
- construction of a quality-assessed final MAG catalogue;
- updated Galaxy workflow export and reproducibility documentation.

Version 2.0 will explicitly distinguish nucleotide contigs/bins (`.fna`, `.fa`, `.fasta`) from predicted protein files (`.faa`) and will retain the distinction between raw binner outputs, refined bins, quality-assessed MAGs, and final classified genomes.
