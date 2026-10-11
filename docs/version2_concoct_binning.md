# Version 2.0 — Reproducible CONCOCT binning of SRR25158482

**Analysis date:** 11 October 2026  
**Platform:** Galaxy Australia ([MAG Recovery version 2.0 history](https://usegalaxy.org.au/histories/view?id=ce807aef12a66e50))  
**Sample:** SRR25158482 (single paired-end metagenomic library)  
**Tool:** CONCOCT 1.1.0+galaxy3 (Galaxy wrappers)  
**Status:** Completed through extraction of **53 candidate genome-bin FASTA datasets**; DAS Tool, CheckM2, and GTDB-Tk not yet performed.

## Purpose and scope

CONCOCT groups metagenomic contigs using sequence composition and coverage. It was run as a third independent binner alongside MetaBAT2 and MaxBin2, with the intention of subsequently reconciling bin sets using DAS Tool. The outputs are **candidate bins**, not independently quality-assessed MAGs. Because only one sample was supplied, this CONCOCT run does not benefit from differential coverage across samples.

## Input assembly and provenance

- MEGAHIT assembly: collection **15**, individual assembly **17**; **1,993,724 contigs**, total **1,150,097,002 bp**, N50 **586 bp**, longest **62,007 bp**.
- Galaxy **Filter sequences by length** retained contigs **≥1,500 bp**: collection **21**, individual FASTA **22**, **48,763 original contigs**. These identifiers were preserved throughout binning.
- Bowtie2 mapped paired reads back to the original MEGAHIT assembly: BAM collection **23**, individual BAM **25**; reported overall alignment **23.44%**. This read-recruitment step is distinct from host-reference mapping.
- Do not substitute the unfiltered assembly for the filtered contig FASTA during bin extraction; the consensus assignment IDs correspond to the filtered original contigs.

## Step 1 — Fragment the filtered contigs

**Galaxy tool:** `CONCOCT: Cut up contigs` (CONCOCT 1.1.0+galaxy3)

| Setting | Executed value |
|---|---|
| Input contigs | Individual filtered FASTA **22** (collection **21**) |
| Fragment / chunk length | **10,000 bp** |
| Overlap | **0 bp** |
| Merge last fragment | **False** |
| Produce BED coordinates | **True** |

**Outputs:** cut-fragment FASTA collection **87** / individual **89** (123.8 MB), and BED collection **88** / individual **90**. The job completed successfully (approximately 19 s wall time; ~694.9 MiB peak memory).

The BED output maps original contigs to fragment coordinates and names, for example:

```text
k141_1892628    0       10000   k141_1892628.concoct_part_0
k141_1892628    10000   16313   k141_1892628.concoct_part_1
```

**Critical requirement:** The cut FASTA, BED coordinates, and subsequent coverage matrix must use identical fragment IDs.

## Step 2 — Calculate fragment-level coverage

**Galaxy tool:** `CONCOCT: Generate the input coverage table` (CONCOCT 1.1.0+galaxy3)

| Input | Dataset |
|---|---|
| Fragment BED | **90** (collection **88**) |
| Bowtie2 read-to-original-contig BAM | **25** (collection **23**) |
| Galaxy assembly input mode | Individual |

**Output:** coverage table collection **91**, individual **92**; tabular, ~1.7 MB; **49,383 reported lines** (header plus 49,382 data rows). Example:

```text
contig                          cov_mean_sample_SRR25158482
k141_1892628.concoct_part_0    10.394
k141_1892628.concoct_part_1    10.990
```

The fragment-level coverage table was computed from alignments to the original assembly using the BED mapping. **Do not** use the five-column MetaBAT2 depth matrix directly as CONCOCT's coverage input: it is a different format and refers to original, rather than cut, contigs.

## Step 3 — Run CONCOCT clustering

**Galaxy tool:** `CONCOCT` **1.1.0+galaxy3**  
**Composition input:** cut-fragment FASTA **89** (collection **87**)  
**Coverage input:** fragment coverage table **92** (collection **91**)

The Galaxy job details confirmed these executed settings:

| Parameter | Value |
|---|---|
| Maximum number of clusters | **400** |
| K-mer length | **4** |
| Minimum contig/fragment length threshold | **1,000 bp** |
| Read length | **100** |
| PCA explained variance | **90%** |
| Random seed | **42** |
| Maximum iterations | **500** |
| Skip coverage normalization | **False** |
| Eliminate total coverage column | **False** |
| Write convergence | **False** |
| Output process log | **False** |

Three distinct outputs were produced: **Clusters**, **PCA components**, and **PCA transformed clusters**. The correct downstream assignment file is **Clusters**, not PCA-transformed coordinates.

- **Clusters:** collection **93**, individual dataset **96**, CSV, **49,270 reported lines**, two columns `contig_id,cluster_id`, ~1.5 MB.
- **PCA transformed clusters:** collection **95**, individual dataset **98**, CSV, **49,270 reported lines**, **42 columns**, ~32.7 MB; **not** the input to merge cut clusters.
- The ~49,382 coverage records versus ~49,269 assignment records should not be equated without accounting for filtering and line-count conventions; this discrepancy has not been independently audited.

Example cluster assignments:

```text
contig_id,cluster_id
k141_378523.concoct_part_0,8
k141_504696.concoct_part_0,49
k141_1261742.concoct_part_0,29
```

## Step 4 — Merge cut-cluster assignments to original contigs

**Galaxy tool:** `CONCOCT: Merge cut clusters and assign consensus clusters for the original contigs`, **1.1.0+galaxy3**

**Input:** CONCOCT **Clusters** collection **93**, individual **96**. The Galaxy selector initially proposed PCA-transformed clusters (**95**), which is **incorrect** and was replaced by the actual cluster assignment file.

**Output:** collection **99**, individual **100**, CSV, **48,763 reported lines**, two columns, ~736.3 KB. Job completed successfully.

Examples:

```text
contig_id,cluster_id
k141_378523,8
k141_504696,49
k141_1261742,29
k141_883233,6
k141_252374,4
```

The suffix `.concoct_part_N` is absent after consensus assignment, allowing the original filtered contig FASTA to be partitioned into bins. A preview of dataset **100** appeared to show an empty assignment for `k141_1640297`; whether this reflects a truly unassigned contig or a preview artifact is **not yet verified**. Do not assume all original contigs were assigned solely because the displayed line count resembles the input contig count.

## Step 5 — Extract FASTA sequences for each CONCOCT cluster

**Galaxy tool:** `CONCOCT: Extract a fasta file for each cluster`, **1.1.0+galaxy3**

| Parameter | Executed input |
|---|---|
| Original contig file | Filtered original FASTA collection **21** (individual **22**) |
| CONCOCT clusters | Merged assignments collection **99** (individual **100**) |

**Observed result:** a Galaxy output list/collection of **53 FASTA datasets**, each representing one CONCOCT candidate cluster/bin. Extraction succeeded (exit code **0**, empty standard output and error; ~**21 seconds** wall time; **709.4 MiB** peak memory).

Example: dataset **102**, named `0`, is a FASTA of **2 sequences**, **6.2 KB**, and starts with original contig identifier `k141_1920729`. This very small bin illustrates why the **53 extracted FASTA files must not be called 53 validated MAGs**.

The extraction job used the following command structure, captured from Galaxy's job details (paths are Galaxy-instance-specific and are not portable):

```bash
ln -s '<Galaxy filtered original contigs FASTA>' contigs.fa
mkdir outdir
extract_fasta_bins.py contigs.fa '<Galaxy merged clusters CSV>' --output_path outdir
```

The recorded run was successful, with **53 candidate genome-bin FASTA outputs**. Bin quality, completeness, contamination, and taxonomy are not yet determined.

## Reproducibility checks and interpretation

1. Use the **same original contig IDs** in filtered assembly, Bowtie2 BAM reference, BED coordinates, consensus assignments, and extracted bins.
2. Use **cut fragment IDs** for CONCOCT composition and coverage; use **original contig IDs** only after merging assignments.
3. Do not confuse `Clusters` with `PCA transformed clusters`.
4. Confirm CSV headers and investigate any blank cluster assignments before converting outputs to DAS Tool contig-to-bin tables.
5. A one-sample coverage matrix has limited discriminatory power relative to multi-sample differential coverage.
6. Record the number of **extracted bins**, not a validated MAG count. Some bins are extremely small (e.g., cluster 0 has two sequences and 6.2 KB).
7. The 53 CONCOCT bins must not be added to the MetaBAT2/MaxBin2 counts as if all were unique genomes; reconciliation and quality assessment remain outstanding.
8. For exact Galaxy tool provenance, inspect the linked Galaxy history; this Markdown record is a human-readable account, **not an exported executable Galaxy workflow**.

## Downstream steps — not yet executed

```text
MetaBAT2 (23 candidate bins) ---+
MaxBin2 (23 candidate bins) ----+--> DAS Tool --> CheckM2 --> GTDB-Tk
CONCOCT (53 candidate bins) ----+
```

Prepare consistent contig-to-bin mappings for each binner and examine the DAS Tool input form before running. No DAS Tool-refined bin count, CheckM2 quality classification, or GTDB-Tk taxonomy is reported at this stage.

## Reference

Alneberg, J., Bjarnason, B. S., de Bruijn, I., Schirmer, M., Quick, J., Ijaz, U. Z., Lahti, L., Loman, N. J., Andersson, A. F., & Quince, C. (2014). Binning metagenomic contigs by coverage and composition. *Nature Methods*, 11, 1144–1146. https://doi.org/10.1038/nmeth.3103

See [Version 2.0 analysis log](version2_assembly_to_metabat2.md) for assembly, mapping, depth, MetaBAT2, and MaxBin2 provenance.
