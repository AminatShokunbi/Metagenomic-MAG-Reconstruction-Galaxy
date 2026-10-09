# Version 2.0 — Data acquisition, QC and host-contamination assessment

Version 2.0 extends the Galaxy-based MAG reconstruction project using a larger paired-end shotgun metagenomic dataset. Documentation is being added as each analysis stage is completed so that computational decisions, including steps that are evaluated but not retained, remain auditable.

## Demonstration dataset

The Version 2.0 demonstration currently uses NCBI SRA accession **SRR25158482**. Reads were retrieved in Galaxy as a paired-end collection using fastq-dump/FasterQ-associated SRA retrieval functionality. The resulting forward and reverse datasets were recognized as `fastqsanger.gz` paired reads.

## Initial read-quality assessment

The paired-end reads were submitted to **FastQC** in Galaxy before assembly. This stage was used to assess raw-read quality and to identify whether preprocessing was warranted before downstream reconstruction.

## Human-host contamination assessment

Because host depletion is not automatically required for every environmental metagenomic dataset, potential human-derived sequence contamination was explicitly assessed before deciding whether a host-removal step should be incorporated into the Version 2.0 preprocessing pathway.

The paired reads from SRR25158482 were mapped against the pre-indexed human reference genome **hg38** using **Bowtie2 v2.5.5+galaxy0** in Galaxy. Bowtie2 was run in **sensitive end-to-end** mode. The run completed successfully (exit code 0).

Bowtie2 reported:

- 72,052,784 paired reads evaluated;
- 72,052,512 pairs aligned concordantly zero times;
- 38 pairs aligned concordantly exactly once;
- 234 pairs aligned concordantly more than once;
- among mates from pairs lacking concordant or discordant paired alignment, 99 mates aligned exactly once and 2,023 aligned more than once; and
- **0.00% overall alignment rate** to hg38 (Bowtie2-reported value, rounded to two decimal places).

The very small number of hg38 alignments relative to the total read set indicated that human-associated sequence contamination was negligible in this dataset. Human-host depletion was therefore **not retained as a mandatory preprocessing step for SRR25158482** before metagenomic assembly.

The 0.00% value should not be interpreted as evidence that literally no reads aligned to hg38: the detailed Bowtie2 output recorded a small number of alignments that round to 0.00% at the precision displayed by Bowtie2.

### Workflow decision

```text
Raw paired-end reads (SRR25158482)
              |
              v
           FastQC
              |
              v
Bowtie2 screening against hg38
              |
              v
  Host mapping negligible?
       /             \
     Yes              No
      |                |
      v                v
Proceed without     Remove host-
host depletion      associated reads
      |                |
      +-------+--------+
              |
              v
            MEGAHIT
```

For this demonstration, the **Yes** branch was followed.

Host screening is retained in the workflow documentation as a **dataset-dependent quality-control decision point**, rather than as a universal requirement. Datasets derived from host-associated material, or datasets showing appreciable mapping to an appropriate host reference, should be evaluated for host-read depletion before assembly.

## Current Version 2.0 status

- [x] SRA paired-end read retrieval
- [x] Raw-read FastQC
- [x] Human-host contamination assessment against hg38
- [x] Evidence-based decision not to perform host depletion for SRR25158482
- [x] MEGAHIT metagenomic assembly
- [ ] Assembly quality assessment
- [ ] Read recruitment and coverage estimation
- [ ] MetaBAT2 binning
- [ ] MaxBin2 binning
- [ ] CONCOCT binning
- [ ] DAS Tool integration/refinement
- [ ] CheckM/CheckM2 quality assessment
- [ ] GTDB-Tk taxonomic classification
- [ ] Final MAG catalogue

## Interpretation

The host-screening experiment is included to document why human-host removal was not carried forward for this dataset. It should not be generalized to all metagenomic datasets. Host filtering should be selected according to sample provenance, study design, relevant host genome availability, and empirical screening results.

## MEGAHIT assembly execution (7 October 2026)

Galaxy Australia showed successful (green/OK) completion of MEGAHIT 1.2.9 (Galaxy wrapper 1.2.9+galaxy2) for the SRR25158482 paired-end collection. The output history contained one FASTA assembly dataset and one text log dataset. The log was approximately 1 MB and contained 8,506 lines. The run used individual assembly mode, rather than merging separate paired-end samples. Earlier parameter screenshots showed minimum multiplicity 2, k-mer list 21,29,39,59,79,99,119,141, and minimum output contig length 200 bp; the final executed settings should be cross-checked against the Galaxy job details. The log was generated successfully. Assembly metrics (total assembled bases, contig count, N50, largest contig, and contig length distribution) have not yet been evaluated; successful tool execution must not be described as successful MAG recovery.

Reference: Li, D., Liu, C.-M., Luo, R., Sadakane, K., & Lam, T.-W. (2015). MEGAHIT: an ultra-fast single-node solution for large and complex metagenomics assembly via succinct de Bruijn graph. *Bioinformatics*, 31(10), 1674–1676. https://doi.org/10.1093/bioinformatics/btv033
