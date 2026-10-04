# Changelog

All notable changes to this project are documented in this file.

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
