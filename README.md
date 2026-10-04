# Galaxy-Based MAG Reconstruction Workflow from Raw Metagenomic Reads

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23144501.svg)](https://doi.org/10.5281/zenodo.23144501)

## Version 1.0 — Assembly, Coverage Estimation and Genome-Binning Demonstration

This repository documents a reproducible, Galaxy-based workflow for progressing from public paired-end metagenomic sequencing reads through quality control, preprocessing, metagenomic assembly, assembly assessment, read recruitment, coverage estimation, and an initial genome-binning attempt.

**Version 1.0 is a stable training and reproducibility release. It does not claim successful recovery of metagenome-assembled genomes (MAGs) from the six demonstration datasets.** Instead, it documents both the implemented computational procedure and the data-dependent limitation encountered when highly fragmented assemblies did not support genome-bin recovery with MetaBAT2.

A subsequent **Version 2.0** is planned to extend the workflow through multi-binner genome reconstruction, DAS Tool integration, MAG quality assessment with CheckM/CheckM2, and taxonomic classification with GTDB-Tk using a dataset suitable for demonstrating those stages.

## Objective

The broader objective of this project is to independently perform and document a complete bioinformatic workflow from raw metagenomic sequencing reads to reconstructed, quality-assessed, and taxonomically classified MAGs using Galaxy.

Version 1.0 establishes and documents the workflow through the first coverage-informed genome-binning attempt while preserving tool settings, intermediate operations, quality-control decisions, and unsuccessful outcomes required for reproducibility.

## Demonstration Dataset

Six publicly available metagenomic sequencing datasets from the NCBI Sequence Read Archive (SRA) were used:

- SRR18276513
- SRR30026880
- SRR30026879
- SRR18276515
- SRR18276516
- SRR18276520

The datasets were processed as paired-end sequencing reads in Galaxy.

## Platform

The workflow was implemented using the Galaxy platform, which provides a graphical environment for reproducible bioinformatic analysis while retaining relationships among tools, parameters, inputs, and outputs.

Galaxy should be cited independently of the individual bioinformatics tools executed through the platform. The Galaxy Project currently recommends the following primary publication:

> The Galaxy Community. (2026). Galaxy for accessible, reproducible, and collaborative data analyses: 2026 update. *Nucleic Acids Research*, **54**(W1), W105–W116. https://doi.org/10.1093/nar/gkag469

Version 1.0 of this project was executed using a public Galaxy service. Citations for Galaxy do not replace citations for the individual software tools used in the workflow.

## Version 1.0 Workflow

```text
NCBI SRA accessions
        |
        v
   FasterQ Dump
        |
        v
Paired-end FASTQ reads
        |
        v
 FastQC / MultiQC
        |
        v
     Cutadapt
        |
        v
Post-trimming QC
        |
        v
     MEGAHIT
        |
        v
       QUAST
        |
        v
Bowtie2 read recruitment
reads -> corresponding assembly
        |
        v
      BAM files
        |
        v
Calculate contig depths
    for MetaBAT2
        |
        v
    Depth matrices
        |
        v
     MetaBAT2
        |
        v
No bins recovered from
practice assemblies
```

## Tools Implemented in Version 1.0

| Analysis stage | Tool | Version recorded during project | Purpose |
|---|---|---:|---|
| SRA read retrieval | FasterQ Dump | 3.1.1 | Retrieval of sequencing reads from SRA accessions |
| Read quality assessment | FastQC | 0.74 | Evaluation of sequencing-read quality |
| Read preprocessing | Cutadapt | 5.2 | Adapter/poly-A trimming and read preprocessing |
| Combined QC assessment | MultiQC | 1.35 | Aggregation and comparison of QC reports |
| Host-mapping demonstration | Bowtie2 | 2.5.5 | Demonstration of read mapping against hg38 |
| Metagenomic assembly | MEGAHIT | 1.2.9 | De novo assembly of metagenomic reads |
| Assembly assessment | QUAST | 5.3.0 | Assessment of assembly contiguity and fragmentation |
| Assembly read recruitment | Bowtie2 | 2.5.5 | Mapping trimmed reads back to corresponding MEGAHIT contigs |
| Coverage calculation | Calculate contig depths for MetaBAT2 | Galaxy implementation | Generation of MetaBAT2-compatible depth matrices |
| Genome-binning attempt | MetaBAT2 | 2.18.23+galaxy0 | Coverage-informed clustering of assembled contigs into candidate bins |

## Bowtie2 Has Two Distinct Roles in This Project

Bowtie2 was used in two conceptually different contexts.

### 1. Host-mapping demonstration

An earlier workflow branch mapped quality-controlled reads against the human hg38 reference genome to demonstrate the principle of host-read identification/removal.

The MEGAHIT assemblies used in Version 1.0 were generated from the Cutadapt-processed paired reads and were not generated from the unmapped reads of this hg38 demonstration branch.

### 2. Read recruitment for genome binning

After assembly, the Cutadapt-processed reads were mapped back to their corresponding sample-specific MEGAHIT contigs. These BAM alignments were then converted into contig-depth matrices for coverage-informed MetaBAT2 binning.

The archived mapping statistics provide direct evidence for this stage. Four samples had overall Bowtie2 alignment rates of **88.84–93.51%**; two additional samples contained warnings for individual one-nucleotide mates that Bowtie2 skipped. High read recruitment did not overcome the severe assembly fragmentation identified by QUAST. See [`docs/08_bowtie2_read_recruitment_summary.md`](docs/08_bowtie2_read_recruitment_summary.md).

This second mapping step is part of the genome-binning workflow and should not be confused with host screening.

## MetaBAT2 Binning Outcome

The initial MetaBAT2 run used coverage information and a minimum contig size of **2,500 bp**. No bins were recovered from the six demonstration assemblies.

QUAST inspection showed substantial assembly fragmentation. Examples included:

| Sample | Contigs >=500 bp | Contigs >=1,000 bp | Contigs >=5,000 bp | Largest contig | N50 |
|---|---:|---:|---:|---:|---:|
| SRR18276513 | 32 | 0 | 0 | 743 bp | 559 bp |
| SRR30026879 | 61 | 24 | 1 | 10,648 bp | 1,508 bp |
| SRR30026880 | 141 | 35 | 3 | 10,646 bp | 1,104 bp |

For example, SRR18276513 contained no contigs >=1,000 bp and therefore no sequence capable of satisfying the initial 2,500-bp MetaBAT2 binning threshold.

To permit a transparent hands-on investigation of the binning stage, MetaBAT2 was repeated using the lowest primary contig threshold permitted by the Galaxy wrapper used in this exercise: **1,500 bp**, with the minimum small-contig setting reduced to **500 bp**. Other principal binning settings were retained.

No bins were recovered after the relaxed training-specific attempt.

Rather than progressively weakening additional parameters to force an output, Version 1.0 ends at this point and records the absence of bins as a legitimate result of the demonstration dataset and assembly characteristics.

See [`docs/07_metabat2_binning_attempt.md`](docs/07_metabat2_binning_attempt.md) for the complete binning-stage documentation.

## Interpretation and Scope

A reproducible MAG-reconstruction workflow does not guarantee MAG recovery from every sequencing dataset.

Genome binning depends strongly on the quantity, contiguity, coverage, and biological composition of the assembled sequence. Version 1.0 therefore demonstrates the computational workflow and its decision points while explicitly separating **successful execution of the workflow** from **successful recovery of MAGs**.

No output from the Version 1.0 MetaBAT2 attempts is presented as a MAG.

## Documentation

Detailed stage-specific documentation is available in `docs/`:

1. `01_data_acquisition.md` — SRA data acquisition
2. `02_quality_control.md` — FastQC and MultiQC assessment
3. `03_read_preprocessing.md` — Cutadapt preprocessing
4. `04_bowtie2_mapping.md` — Bowtie2 host-mapping demonstration
5. `05_metagenomic_assembly.md` — MEGAHIT assembly
6. `06_assembly_quality.md` — QUAST assessment
7. `07_metabat2_binning_attempt.md` — read recruitment, depth calculation, MetaBAT2 binning attempts, and Version 1.0 endpoint
8. `08_bowtie2_read_recruitment_summary.md` — mapping-statistics evidence supporting the coverage-estimation stage

The executable Galaxy workflow is preserved as [`workflow/MAG_RECOVERY_WORKFLOW.ga`](workflow/MAG_RECOVERY_WORKFLOW.ga). It records the actual Galaxy tool graph and parameters used during Version 1.0. Users should consult the written documentation together with the exported workflow because the historical workflow also preserves collection handling and troubleshooting operations that are important for auditability.

## Reproducibility Principles

This repository intentionally records:

- tool versions where available;
- input/output relationships;
- sample-wise collection processing;
- quality-control decisions;
- assembly statistics relevant to downstream decisions;
- standard/default settings used during the initial binning attempt;
- training-specific parameter changes;
- unsuccessful outputs and their interpretation.

The purpose is to make the analysis auditable and adaptable rather than to imply that identical parameters are universally optimal for all metagenomic datasets.

## Version 1.0 Status

### Completed

- [x] SRA accession selection
- [x] Raw-read retrieval with FasterQ Dump
- [x] Paired-end read organization
- [x] Initial FastQC assessment
- [x] MultiQC aggregation
- [x] Adapter/poly-A preprocessing with Cutadapt
- [x] Post-trimming FastQC and MultiQC
- [x] Bowtie2 host-mapping demonstration
- [x] MEGAHIT metagenomic assembly
- [x] QUAST assembly assessment
- [x] Bowtie2 mapping of trimmed reads back to corresponding assemblies
- [x] MetaBAT2 contig-depth calculation
- [x] MetaBAT2 initial binning attempt
- [x] MetaBAT2 relaxed training-specific binning attempt
- [x] Documentation of the zero-bin outcome and assembly limitations
- [x] Exported Galaxy workflow preserved for reproducibility
- [x] Bowtie2 read-recruitment statistics documented

### Version 1.0 endpoint

**No candidate genome bins were recovered from the demonstration assemblies. Consequently, DAS Tool integration, CheckM/CheckM2 MAG-quality assessment, GTDB-Tk classification, and construction of a final MAG catalogue were not performed in Version 1.0.**

This is intentional: those analyses require candidate genome bins, and Version 1.0 does not claim outputs that were not produced.

## Version 2.0 Roadmap

Version 2.0 will continue the project using a larger **raw shotgun metagenomic read dataset** suitable for hands-on demonstration of genome-resolved reconstruction.

The planned architecture is:

```text
Raw paired-end shotgun metagenomic reads
              |
              v
     QC and preprocessing
              |
              v
      Assembly/co-assembly
              |
              v
Read mapping + coverage estimation
              |
      +-------+-------+
      |       |       |
      v       v       v
  MetaBAT2  MaxBin2  CONCOCT
      |       |       |
      +-------+-------+
              |
              v
           DAS Tool
     bin integration/refinement
              |
              v
         Refined bins
              |
              v
       CheckM / CheckM2
completeness and contamination
              |
              v
           GTDB-Tk
     taxonomic classification
              |
              v
       Final MAG catalogue
```

Version 2.0 will preserve the distinction between raw binner outputs, DAS Tool-refined bins, quality-assessed MAGs, and taxonomically classified genomes.

## Repository Organization

- `data/` — accession and dataset information
- `docs/` — detailed documentation of individual analysis stages
- `workflow/` — exported Galaxy workflow and workflow documentation
- `CITATION.cff` — machine-readable citation metadata for GitHub/archival services
- `CHANGELOG.md` — version history and release scope
- `LICENSE` — MIT software license

## Release and Citation

The repository contains `CITATION.cff`, an MIT `LICENSE`, a changelog, an exported Galaxy workflow, and explicit versioned documentation to support GitHub release and Zenodo archival.

## Citation

If you use this workflow or its documentation, please cite the archived Zenodo release:

Shokunbi, A. O. (2026). *Galaxy-Based MAG Reconstruction Workflow from Raw Metagenomic Reads: Version 1.0 — Assembly, Coverage Estimation and Genome-Binning Demonstration* (Version v1.0.1) [Computer software]. Zenodo. https://doi.org/10.5281/zenodo.23144502

### Software platform citation

The analyses documented in this repository were performed using Galaxy. Users of this workflow should also cite the Galaxy platform:

The Galaxy Community. (2026). Galaxy for accessible, reproducible, and collaborative data analyses: 2026 update. *Nucleic Acids Research*, **54**(W1), W105–W116. https://doi.org/10.1093/nar/gkag469

Individual software tools used within Galaxy should additionally be cited according to their respective publications.

### DOI

- **Project DOI (all versions):** https://doi.org/10.5281/zenodo.23144501
- **Version v1.0.1 DOI:** https://doi.org/10.5281/zenodo.23144502

The project DOI resolves to the latest archived version. The version-specific DOI should be used when referring specifically to Version v1.0.1.

## Author

**Aminat Olamide Shokunbi**

PhD researcher working with metagenomic, bioinformatic, and genome-resolved approaches for the investigation of microbial communities and their functional potential.
