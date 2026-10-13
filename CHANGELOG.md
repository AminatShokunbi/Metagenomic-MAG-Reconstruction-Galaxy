# Changelog

All notable changes to this project are documented in this file.

### Update — 2026-10-13

- Documented successful two-binner DAS Tool execution and output #298 (5,105 contig-to-bin assignments); final bin count/quality not yet verified.
- Recorded input-format problems, failed Galaxy column expression, incorrect constant `MaxBin2_001` labeling across 23 collection elements, and attempted GUI remedies.
- Documented the corrected headerless MaxBin2 contig-to-bin TSV strategy and the pending three-binner DAS Tool rerun.
- Added [DAS Tool and MaxBin2 troubleshooting log](docs/version2_dastool_maxbin2_troubleshooting.md). CheckM2/GTDB-Tk remain pending.

## [2.0.0 — work in progress] — updated 2026-10-11

- Began independent Version 2.0 analysis using SRA SRR25158482 on Galaxy Australia.
- Recorded MEGAHIT assembly and QUAST results, ≥1,500-bp contig filtering, Bowtie2 read-to-contig mapping, and MetaBAT2 depth calculation.
- Documented **23 candidate MetaBAT2 genome bins**, explicitly not yet validated MAGs.
- Added stage-specific reproducibility notes and references in [docs/version2_assembly_to_metabat2.md](docs/version2_assembly_to_metabat2.md).
- MaxBin2 v2.2.7+galaxy6 produced **23 candidate bins** from filtered contigs and a derived abundance table. Internal completeness estimates were **12.1–80.4%**; 10 bins met the 50% threshold based on MaxBin2 estimates only. Dataset provenance and abundance-header handling require job-log confirmation.
- Completed CONCOCT **1.1.0+galaxy3**: 10,000-bp contig fragmentation, fragment-level BAM coverage generation, clustering (max 400; k=4; 1,000-bp minimum; seed 42; 500 iterations; 90% PCA variance), merging fragment assignments, and extracting **53 candidate genome-bin FASTA datasets**. Added the full reproducibility record in [docs/version2_concoct_binning.md](docs/version2_concoct_binning.md). These are not quality-validated MAGs.
- DAS Tool, CheckM2, and GTDB-Tk remain pending.
- Version 1.0 historical results and releases remain unchanged. No new Zenodo DOI or Version 2.0 release is claimed.

## [1.0.2] - 2026-10-04

### Added
- Added `CITATIONS.md` containing citations for the Galaxy platform and software implemented in the Version 1.0 workflow.
- Added citations for SRA Toolkit/FasterQ Dump, FastQC, MultiQC, Cutadapt, Bowtie 2, MEGAHIT, QUAST, and MetaBAT 2.
- Added links from the README to the complete software citation record.

### Changed
- Updated repository documentation so that the archived workflow citation is clearly distinguished from citations for the software used to perform the analyses.
- Updated the repository-organization and citation sections of the README to include `CITATIONS.md`.

### Notes
Version 1.0.2 is a documentation and citation patch. No scientific analyses, datasets, workflow parameters, assembly statistics, binning outcomes, or conclusions were changed.

Software planned for Version 2.0 but not implemented in Version 1.0 is not presented as software used in the Version 1.0 analysis.

## [1.0.1] - 2026-10-04

### Changed
- Enabled archival of the Galaxy-based MAG reconstruction workflow through Zenodo.
- Prepared repository metadata for persistent DOI-based citation.
- No changes were made to the scientific workflow, analysis parameters, datasets, or conclusions.

### Notes
Version 1.0.1 is an archival and documentation patch release of Version 1.0.0.
The underlying Galaxy workflow and documented genome-binning outcome are unchanged.

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

Version 2.0 will use a larger and more suitable metagenomic read dataset to extend the hands-on workflow beyond the initial binning stage.

Planned additions include:

- reconstruction from raw metagenomic sequencing reads;
- quality control and preprocessing;
- metagenomic assembly/co-assembly as appropriate to the dataset;
- read mapping and coverage estimation;
- MetaBAT2 binning;
- MaxBin2 binning;
- CONCOCT binning where appropriate;
- DAS Tool integration and refinement of multi-binner results;
- CheckM/CheckM2 assessment of completeness and contamination;
- GTDB-Tk taxonomic classification;
- construction of a quality-assessed final MAG catalogue;
- updated Galaxy workflow export and reproducibility documentation.
