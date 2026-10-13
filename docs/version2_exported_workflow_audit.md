# Galaxy Workflow Version 2.0 — exported-file audit (13 October 2026)

**Source:** user-exported `Galaxy-Workflow-MAG_Recovery_version_2.0.ga` (Galaxy Australia). The file was inspected as JSON; it reports `name: MAG Recovery version 2.0`, `format-version`, and **39 steps**: **38 tool steps** and **1 data-input step**.

## Tools present in the exported workflow

- Download and Extract Reads in FASTQ (SRA tools, 3.1.1+galaxy1)
- FastQC 0.74+galaxy1
- Bowtie2 2.5.5+galaxy0 (two steps)
- MEGAHIT 1.2.9+galaxy2
- QUAST 5.3.0+galaxy1
- Filter sequences by length 1.2
- Calculate contig depths (MetaBAT2 JGI) 2.18.23+galaxy0
- MetaBAT2 2.18.23+galaxy0
- MaxBin2 2.2.7+galaxy6
- CONCOCT cut-up, coverage, clustering, merge and FASTA extraction (1.1.0+galaxy3)
- FASTA-to-Tabular, Cut, Remove beginning, Convert, Compute, Add column, Add input name as column, Concatenate datasets, Sort, Unique lines
- DAS Tool 1.1.7+galaxy1 (two separate steps)
- CheckM2 1.1.0+galaxy1

## Connection audit: important reproducibility limitations

**Step 35: DAS Tool, three-binner configuration.** Its `input_connections` link filtered contigs from **step 7**, bin tables from **step 30** and **step 25**, and the first bin table from **step 0 (data_input)**. Step 0's label is `MaxBin2_contigs2bin_corrected.tsv`. This is an **external required input**, not a file generated within the workflow. To rerun the three-binner step, supply the actual corrected headerless two-column MaxBin2 mapping as that input. The export itself does not contain the TSV's rows.

**Step 34: an earlier DAS Tool configuration** takes two binning inputs (steps 30 and 25) and contigs (step 7). It is not the completed three-binner step.

**Step 2: CheckM2** exists in the export, but its `input_connections` is **empty**. It is therefore **not connected to the output FASTA bins from step 35** in this exported workflow. The successful CheckM2 run is documented by Galaxy history datasets **#409–#414** and quality report **#417**, not by an executable downstream link in this .ga export. Before calling the workflow fully automated, connect DAS Tool exported bin FASTAs to CheckM2 using Galaxy's workflow editor and verify a successful workflow execution.

**Step 35 label settings:** The three `labels` fields in its exported `tool_state` are empty. The history log shows automatic labels `MaxBin2_contigs2bin_corrected.tsv,SRR25158482,SRR25158482` and duplicate-label renaming. Set explicit unique labels `MaxBin2`, `MetaBAT2`, `CONCOCT` for clearer provenance in the next saved revision.

**Other caution:** The export includes intermediate attempts (two DAS Tool steps, multiple column manipulation steps, sorting/unique operations). An exported history-derived workflow is not necessarily a cleaned, independently rerunnable pipeline. Check all data-input bindings and test execution before describing it as validated.

## Confirmed Galaxy execution results (history, not inferred from export)

The completed three-binner DAS Tool run produced six FASTA bins **#409–#414**, summary **#406**, contig mapping **#407** (6,281 assignments), and log **#408**. CheckM2 report **#417** assessed all six. For per-bin completeness, contamination and model details, see [DAS Tool and CheckM2 results](version2_dastool_checkm2_results.md).

## Recommended next workflow revision

1. Import this exported .ga file in Galaxy and preserve the original as a snapshot.
2. Verify the corrected MaxBin2 TSV is supplied to the data-input step.
3. Set unique binning labels in the final three-binner DAS Tool step.
4. Wire the DAS Tool exported FASTA bin collection into CheckM2; ensure collection/dataset types are compatible.
5. Add GTDB-Tk after quality control and decide whether to classify all six or a quality-filtered subset.
6. Remove or clearly label abandoned intermediate transformations; run a test end-to-end.
7. Re-export a cleaned workflow as a new revision, without overwriting the prior Version 1.0 release.

This audit records the exported workflow's structure, **not** proof that every included step will execute successfully when the .ga file is imported elsewhere.
