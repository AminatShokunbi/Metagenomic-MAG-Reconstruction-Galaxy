# Version 2.0 — Reproduce the MAG reconstruction or adapt it to another dataset

**Status (17 October 2026):** The SRR25158482 analysis completed in Galaxy Australia and yielded six DAS Tool-selected genome bins, assessed using CheckM2. The latest exported workflow has **39 steps** and contains a connected DAS Tool → CheckM2 stage. **A full one-click execution of this latest exported workflow has not yet been demonstrated.** The completed Galaxy history and the exported workflow are separate forms of evidence.

## A. Reproduce the demonstrated SRR25158482 analysis

1. Open [Galaxy Australia](https://usegalaxy.org.au/) and sign in.
2. Import the latest Version 2.0 workflow (.ga file) from the `workflow/` directory. Do **not** confuse it with the archived Version 1.0 workflow.
3. Import the repository's corrected `MaxBin2_contigs2bin_corrected.tsv` file into a new Galaxy history as a **tabular** dataset. The file is a **headerless, two-column** mapping: column 1 is the contig identifier and column 2 is the MaxBin2 bin identifier.
4. Open the Version 2.0 workflow's **Run** page. Its SRA retrieval tool is configured with accession **SRR25158482**. Select the corrected MaxBin2 mapping as the required workflow input. Do not select an unrelated MultiQC report.
5. Inspect the run configuration and execute in a new history. The recorded workflow includes FastQC, MEGAHIT, assembly evaluation, read mapping back to assembled contigs, contig-depth calculation, MetaBAT2, MaxBin2, CONCOCT, DAS Tool, and CheckM2.
6. Inspect the **three-input DAS Tool** result and the CheckM2 **quality (tabular)** report. Preserve all output FASTAs, the DAS Tool summary and log, and the CheckM2 report.

**Original completed-history reference:** Galaxy history `MAG Recovery version 2,0`. The original three-binner DAS Tool run used filtered contigs #22, MaxBin2 mapping #401, MetaBAT2 mapping #280 and CONCOCT mapping #158. It produced six FASTA bins (#409–#414) and a CheckM2 report (#417). See [results and exact estimates](version2_dastool_checkm2_results.md). Galaxy dataset numbers are history-specific, not portable workflow inputs.

**Expected reference results, not guaranteed identical outputs:** MetaBAT2: 23 bins; MaxBin2: 23 bins; CONCOCT: 53 bins; DAS Tool: six selected bins. CheckM2 completeness ranged from 49.99% to 93.43%, and contamination from 0.65% to 13.28%. Tool/database versions and execution environments may affect results.

## B. Adapt the workflow to another metagenomic dataset

1. Select a suitable **paired-end** metagenomic SRA accession. In the Galaxy workflow editor, update the **Download and Extract Reads in FASTQ** tool's accession from `SRR25158482` to the new accession. Save as a new workflow copy; preserve the reference version.
2. Verify that the read retrieval produces a paired collection. Inspect FastQC reports. **Do not assume that a successful import validates read quality.**
3. Run MEGAHIT and inspect QUAST and the filtered contig FASTA. Check that sufficient contigs meet the minimum-length requirements for binning.
4. Map the reads back to their **own** assembly with the assembly-recruitment Bowtie2 step; calculate depth and run MetaBAT2, MaxBin2 and CONCOCT.
5. **Generate a new MaxBin2 contig-to-bin mapping for this assembly.** The original corrected TSV must **not** be reused with another assembly. Each mapping row must contain a contig ID from the current assembly and its MaxBin2 bin ID. Distinct MaxBin2 bins must have distinct bin IDs. Use the source MaxBin2 output collection to establish the correct membership; validate that contig IDs exist in the current assembly and that assignments are unambiguous.
6. Upload the new mapping as a tabular dataset and supply it at the workflow's required MaxBin2 input. **Because this workflow runs from SRA retrieval through binning, a new mapping must be prepared in advance from the corresponding new analysis (for example, by first running the binning stages).** This is a documented two-stage dependency, **not** an automated one-click new-sample pipeline.
7. Run the final three-binner DAS Tool with the new MaxBin2 mapping, plus the new MetaBAT2 and CONCOCT mappings; assess the resulting FASTA bins with CheckM2.
8. Report the number of bins and individual completeness/contamination estimates. Do not assume the six-bin result generalizes to a different sample. GTDB-Tk may be added separately for optional taxonomy.

## Why the MaxBin2 mapping is supplied

During the original analysis, the Galaxy conversion of the MaxBin2 FASTA bin collection to a correct two-column DAS Tool mapping proved difficult. A preliminary mapping gave the same `MaxBin2_001` label to all 23 bins. The corrected mapping was therefore constructed separately and uploaded as a Galaxy dataset. This **documented external preprocessing step does not invalidate** the completed bin reconstruction or CheckM2 assessment; it does mean the current exported workflow is **not fully self-contained for new assemblies**.

**Input contract:** plain tabular text, no header, exactly two columns (contig ID and bin ID), matching the contigs and MaxBin2 bin collection of the same assembly. Do not use a MultiQC file or a mapping from a different sample.

## Workflow audit notes

- The latest exported workflow contains 39 steps and its final three-binner DAS Tool `bins` output is connected to CheckM2 `Input MAG/SAG datasets`.
- A separate earlier two-binner DAS Tool branch and an auxiliary sorting/unique-lines branch remain in the export; they are not the source of the final CheckM2 bins.
- One Bowtie2 branch is configured with an **hg38** index, while the assembly-recruitment Bowtie2 maps reads to the MEGAHIT assembly. The hg38 branch must **not** be represented as host-read removal from the reads entering MEGAHIT: the MEGAHIT input is connected directly to SRA retrieval in this export.
- The exported workflow does not include Cutadapt preprocessing in the path feeding MEGAHIT. Do not claim this export performed trimming unless independently demonstrated.
- The latest workflow has **not** been end-to-end rerun and validated; do not describe it as one-click validated.

## Reporting

The original analysis was successful in reconstructing and assessing six candidate bins. Four passed the numerical screen of ≥50% completeness and <10% contamination. These metrics do not establish six high-quality MAGs or formal MIMAG high-quality designations. Taxonomic classification with GTDB-Tk is optional to the reconstruction objective.

See [DAS Tool and CheckM2 results](version2_dastool_checkm2_results.md) for exact per-bin results and limitations.
