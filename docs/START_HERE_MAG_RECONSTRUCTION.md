# Beginner's guide: Reconstructing MAGs in Galaxy Australia (Version 2.0)

**Goal:** Recover and quality-assess metagenome-assembled genome (MAG) candidates from raw shotgun metagenomic reads, using sample **SRR25158482**. Taxonomic assignment with GTDB-Tk is **optional** and is not required to complete this reconstruction demonstration.

**Platform:** [Galaxy Australia](https://usegalaxy.org.au/). You need a free Galaxy account and enough history storage. Galaxy interfaces and tool versions can change. Use the recorded settings below when available, and check your outputs before proceeding.

**Important reproducibility note:** The uploaded history-derived Galaxy workflow contains 39 steps including exploratory operations. Its CheckM2 step is **not connected** to DAS Tool, and its final DAS Tool step requires a separately supplied corrected MaxBin2 contig-to-bin table. Consequently the original export is a **workflow snapshot, not a verified one-click end-to-end pipeline**. The steps below are the supported manual path. The exact MaxBin2 table used in the completed analysis is named `MaxBin2_contigs2bin_corrected.tsv`; it is a required external input to the exported DAS Tool step. Do not claim full automated reproducibility until a cleaned workflow is tested from start to finish.

## 1. Set up a Galaxy history

1. Open Galaxy Australia and sign in.
2. Create a new history, e.g. `SRR25158482_MAG_reconstruction`.
3. In the **Tools** panel, search by the tool names given below. Select the matching version where Galaxy offers it.
4. Wait until each required output is **green** before selecting it for the next stage. The numeric history IDs below are **from the original run** and **will differ in your own history**.

## 2. Download the sequencing reads

1. Open **Download and Extract Reads in FASTQ** (SRA tools **3.1.1+galaxy1**).
2. Select accession-number input and enter **SRR25158482**.
3. Choose compressed FASTQ output (`fastqsanger.gz`); retain paired-end read structure.
4. Run the tool. The paired-read collection is the input to assembly and read recruitment.

**Check:** Galaxy shows the expected paired FASTQ collection; do not confuse read-pair collections with individual FASTQ datasets.

## 3. Inspect raw read quality

1. Open **FastQC** (**0.74+galaxy1**).
2. Select the paired-read collection from step 2; run it on both mates.
3. Inspect per-base sequence quality, adapter content, and overrepresented sequences.
4. The exported Version 2.0 workflow does **not** document a connected Cutadapt preprocessing stage; do not claim trimming occurred in this particular run without a separate Galaxy history record. If you trim, record exact settings and map/assemble the trimmed reads consistently.

## 4. Assemble reads with MEGAHIT

1. Open **MEGAHIT** (**1.2.9+galaxy2**).
2. Select **paired collection** as input and the FASTQ read-pair collection.
3. Choose custom k-mer list **`21,29,39,59,79,99,119,141`**.
4. Minimum k-mer count **2**; minimum output contig length **200 bp**; keep other settings at the exported defaults.
5. Run and wait for the assembly FASTA.

**Original result:** MEGAHIT produced **1,993,724 contigs**, total **1,150,097,002 bp**, N50 **586 bp**. These are reference observations, not guaranteed values for a different tool version or preprocessing path.

## 5. Assess and filter the assembly

1. Run **QUAST** (**5.3.0+galaxy1**) on the MEGAHIT assembly. Inspect contig number, total length, and N50.
2. Run **Filter sequences by length** (version **1.2**) on the same assembly.
3. Set **minimum length = 1500**, **maximum length = 0** (no maximum).
4. Keep the filtered FASTA for **all three binning tools** and DAS Tool.

**Original filtered assembly:** **48,763 contigs ≥1,500 bp**. The original individual FASTA was **#22** (collection #21).

## 6. Map reads back to the assembly and calculate depth

1. Open **Bowtie2** (**2.5.5+galaxy0**).
2. Select the same paired reads used for assembly.
3. Select **Use a genome from history** and choose the **unfiltered MEGAHIT assembly** as the reference (the exported workflow links Bowtie2 to MEGAHIT, not to the filtered contigs).
4. Use the **--sensitive** preset. Enable mapping statistics.
5. Run; retain the aligned BAM.
6. Open **Calculate contig depths** (**2.18.23+galaxy0**) and select the aligned BAM.
7. Use the exported settings: **percent identity 97**, **minimum mapping quality 0**, **maximum edge bases 75**, other settings default. Run to produce the depth table.

**Original Bowtie2 result:** 23.44% overall alignment. Check that the depth table has contig identifiers corresponding to the assembly.

## 7. Run MetaBAT2

1. Open **MetaBAT2** (**2.18.23+galaxy0**).
2. Select the **filtered contig FASTA** from step 5.
3. Under advanced coverage/depth, supply the depth table from step 6.
4. Set **minimum contig length 1500** and **minimum small contig length 1500**; leave other settings at the exported values, including minimum bin size **200000 bp**, seed **1**.
5. Run and inspect the output **bin FASTA collection**.

**Original result:** **23 MetaBAT2 bins**.

**Prepare a DAS Tool table:** Each row must be `contig_id<TAB>bin_id`, **without a header**. In the original workflow, MetaBAT2 bin FASTAs were converted using **FASTA-to-Tabular**, the contig identifier column was extracted using **Cut**, the collection element name was added as a second column with **Add input name as column**, and datasets were combined with **Concatenate datasets**. Inspect the final table to ensure distinct bin labels and that its contig IDs occur in the filtered assembly. The original consolidated table was **#280** (its collection-level predecessor was #279).

## 8. Run MaxBin2

1. From the depth table, use **Cut** to select **columns 1 and 3** (contig identifier and the selected abundance/depth column). Preserve the required tab-separated format.
2. Open **MaxBin2** (**2.2.7+galaxy6**).
3. Select the **filtered assembly FASTA**.
4. Select **abundance file** and supply the table from the preceding step.
5. Set **minimum contig length 1500**, **maximum iterations 50**, **probability threshold 0.9**; marker set **107**. Run.
6. Inspect the collection of bin FASTAs.

**Original result:** **23 MaxBin2 bins**.

**Prepare a DAS Tool table:** Every contig in each MaxBin2 bin FASTA must be assigned to that bin's **actual identifier**, e.g. `MaxBin2_001`, `MaxBin2_002`, …, `MaxBin2_023`. Convert each FASTA to tabular, retain only the contig ID, add the correct per-file bin ID, and concatenate into **one headerless two-column TSV**. **Do not** append the constant label `MaxBin2_001` to every file. The original run used the corrected file `MaxBin2_contigs2bin_corrected.tsv` uploaded as Galaxy **#401**. The supplied file was reported as **31,240 assignments**, 23 distinct bins, zero duplicate contig IDs; verify against your own input.

## 9. Run CONCOCT

1. Run **CONCOCT: Cut up contigs** (**1.1.0+galaxy3**) on the **filtered FASTA**. Set fragment/chunk size **10000**, overlap **0**, and **generate BED** enabled.
2. Run **CONCOCT: Generate the input coverage table** on the generated BED and the read-alignment BAM from step 6.
3. Run **CONCOCT** (**1.1.0+galaxy3**) using the cut-up FASTA (composition) and coverage table.
4. Use **maximum clusters 400**, **k-mer length 4**, **length threshold 1000**, **read length 100**, **PCA explained variance 90%**, **seed 42**, **iterations 500**.
5. Run **CONCOCT: Merge cut clusters** on the clustering output, restoring original contig-level assignments.
6. Run **CONCOCT: Extract a fasta file** using the merged cluster table and the filtered original FASTA.

**Original result:** **53 CONCOCT candidate bin FASTAs**.

**Prepare a DAS Tool table:** Convert the merged clustering table to a **headerless two-column contig ID / bin ID TSV**. The original analysis used **Convert** (to normalize delimiters) followed by **Remove beginning** (remove one header line). The individual table used by DAS Tool was **#158** (collection predecessor #157). Verify its columns before proceeding.

## 10. Integrate the three predictions using DAS Tool

1. Open **DAS Tool** (**1.1.7+galaxy1**).
2. **Contig sequences:** select the **individual filtered assembly FASTA**.
3. Add **three Bins** entries, each selecting an **individual consolidated two-column table**, **not** a bin FASTA collection or a summary:
   - **MaxBin2**: corrected table (original #401), label **`MaxBin2`**.
   - **MetaBAT2**: consolidated table (original #280), label **`MetaBAT2`**.
   - **CONCOCT**: merged clustering table (original #158), label **`CONCOCT`**.
4. Use **DIAMOND**, **score threshold 0.5**, **duplicate penalty 0.6**, **megabin penalty 0.5**, **maximum post-threshold iterations 10**.
5. Enable **write bins**; keep bin evaluation and unbinned FASTA export off.
6. Run. Inspect the **bin FASTA collection**, **summary**, **contigs-to-bin map**, and **log**.

**Original observed outcome:** six selected bin FASTAs (**#409–#414**); summary **#406**, mapping **#407** (**6,281 assignments**), log **#408**. The original run left the prediction labels blank and DAS Tool renamed nonunique labels automatically; **explicit labels are recommended but were not the exact settings of that successful run**, so bin names may differ.

## 11. Assess genome quality with CheckM2

1. Open **CheckM2** (**1.1.0+galaxy1**).
2. Select **all six individual FASTA bins** from DAS Tool (or a compatible bin collection if supported).
3. Select **CheckM2 DIAMOND database version 1.0.2**.
4. Set **Treat input files as protein files = No**.
5. Set **Model options = None** (automatic model selection).
6. Leave **Prodigal table** empty.
7. Run; inspect the **Quality report** (original **#417**, 7 lines including header, 14 columns).

### Original CheckM2 quality estimates

| FASTA dataset in original Galaxy history | Bin suffix | Completeness (%) | Contamination (%) |
|---|---|---:|---:|
| #409 | `MaxBin2_011_sub` | 49.99 | 5.91 |
| #410 | `02_23` | 93.43 | 8.21 |
| #411 | `02_5_sub` | 58.64 | 3.33 |
| #412 | `03_14_sub` | 71.84 | 13.28 |
| #413 | `03_24_sub` | 66.24 | 1.12 |
| #414 | `03_47` | 81.64 | 0.65 |

**Interpretation:** Four bins meet the common **≥50% completeness and <10% contamination** numerical screen (#410, #411, #413, #414); three meet **≥50% and <5%** (#411, #413, #414). These thresholds alone do **not** confer a formal MIMAG quality designation. No bin meets the ≥90% completeness and <5% contamination high-quality numerical combination. Retain all six results, document their quality, and report screening criteria transparently.

## 12. Save reproducibility outputs

Download or preserve:

- Original FASTQ accession and processing settings.
- MEGAHIT assembly, QUAST report, and filtered FASTA.
- Bowtie2 BAM and contig-depth table.
- MetaBAT2, MaxBin2, and CONCOCT bin FASTAs.
- **Three distinct contig-to-bin tables** (headerless TSVs), including the corrected MaxBin2 table.
- DAS Tool six-bin FASTA collection, summary, mapping, and log.
- CheckM2 quality report and version/database information.
- Exported Galaxy history and workflow (via Galaxy's history/workflow menus).

**Completion criterion:** Another Galaxy user can reconstruct candidate genome bins, run DAS Tool on the three predictions, and generate a CheckM2 report. Results can vary with software/database versions and input preprocessing. The exact original workflow export requires manual reconnection of CheckM2 and a corrected MaxBin2 input before it can be called a tested one-click workflow.

## Optional: GTDB-Tk

GTDB-Tk can assign taxonomy to the quality-assessed bins, but is **not part of the required reconstruction endpoint**.

## References

- Li, D. et al. (2015). MEGAHIT. *Bioinformatics*, 31, 1674–1676. https://doi.org/10.1093/bioinformatics/btv033
- Kang, D. D. et al. (2019). MetaBAT 2. *PeerJ*, 7, e7359. https://doi.org/10.7717/peerj.7359
- Alneberg, J. et al. (2014). Binning metagenomic contigs by coverage and composition. *Nature Methods*, 11, 1144–1146. https://doi.org/10.1038/nmeth.3103
- Wu, Y.-W. et al. (2016). MaxBin 2.0. *Bioinformatics*, 32, 605–607. https://doi.org/10.1093/bioinformatics/btv638
- Sieber, C. M. K. et al. (2018). DAS Tool. *Nature Microbiology*, 3, 836–843. https://doi.org/10.1038/s41564-018-0171-1
- Chklovski, A. et al. (2023). CheckM2. *Nature Methods*, 20, 1203–1212. https://doi.org/10.1038/s41592-023-01940-w
