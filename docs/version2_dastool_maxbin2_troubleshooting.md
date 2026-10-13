# Version 2.0 — DAS Tool integration and MaxBin2 assignment troubleshooting (13 October 2026)

**Sample:** SRR25158482. **Platform:** Galaxy Australia, history “MAG Recovery version 2,0”. **Status as originally recorded:** Two-binner DAS Tool completed; three-binner integration was pending at this stage. **Later on 13 October, three-binner DAS Tool and CheckM2 completed**; see [subsequent results](version2_dastool_checkm2_results.md). GTDB-Tk remains pending.

## Inputs and upstream progress

- MEGAHIT assembly: 1,993,724 contigs, 1,150,097,002 bp, N50 586 bp; filtered assembly **#21 (collection)/#22 (individual)** retained **48,763 contigs ≥1,500 bp**.
- Bowtie2 read recruitment to assembly: 23.44% overall alignment; coverage-depth matrix prepared for binning.
- MetaBAT2: **23 candidate bins** (collection **#29**); contig-to-bin conversion/concatenation attempted, including **#279**.
- MaxBin2: **23 candidate bins**, collection **#57**; an individual bin FASTA **#64:001** contains **3,433 sequences**.
- CONCOCT: cut-up contigs, coverage matrix, clustering, merged consensus (**#100**), and extraction of **53 candidate bin FASTAs**. A usable two-column CONCOCT contig-to-bin table was observed at **#158**, 48,763 rows; entries resemble `k141_378523<TAB>8`.

## DAS Tool and observed output

Galaxy DAS Tool **1.1.7+galaxy1** requires a two-column **contig ID / bin ID** table for each binner and the original contig FASTA. An initial tool form had incompatible inputs: **#279** MetaBAT2 concatenation, **#101** CONCOCT extracted FASTAs (implicit conversion), and **#60** MaxBin2 summary. These latter outputs are not appropriate contig-to-bin mappings.

A subsequent two-binner DAS Tool run completed successfully using **individual filtered FASTA #22**, **CONCOCT table #158**, and a second input recorded as **#280** in the successful job title. Output **#298**, “Contigs to bin file for the output bins”, was green and contained **5,105 lines / two columns**. Its preview showed bin identifiers such as `SRR25158482.binner.01_23`. Other reported outputs were **#297** (summary of output bins) and **#299** (log). **The identity/content of #280 and number/quality of DAS Tool bins must be verified from job details and summary before reporting final results.** A successful two-binner run is not evidence that all three binners were integrated.

DAS Tool settings recorded from the Galaxy form: DIAMOND single-copy-gene engine; selection score threshold **0.5**; duplicate-marker penalty **0.6**; megabin penalty **0.5**; max post-threshold iterations **10**; export bin FASTAs **enabled**; input-bin evaluation **disabled**; unbinned contigs export **disabled**.

## MaxBin2 contig-to-bin table: encountered problems

1. Galaxy Australia did not provide an accessible **FASTA_to_Contigs2Bin** tool in the user's search; do not assume this conversion tool is installed.
2. **FASTA-to-Tabular** was applied to MaxBin2 bin FASTA collection **#57**. Output **#306** (one element of collection **#305**) had **3,433 rows / two columns**: contig ID and nucleotide sequence. Galaxy **Cut** selected the contig ID column, giving **#330** (from collection **#329**), 3,433 rows / one column.
3. **Add column to an existing dataset** was mapped over the 23-bin collection, producing **#377** (and corresponding individual **#380**). However, each file's added second-column value was the constant `MaxBin2_001`, rather than a unique bin label. For example the third collection element `003` still displayed `MaxBin2_001`. **This is incorrect for DAS Tool** because distinct MaxBin2 bins collapse into one label.
4. An earlier **Add a column** expression failed with `Invalid expression: ""MetaBAT2_" + str(c2)"`; avoid relying on unverified expression syntax or expecting a collection index to be available as a table column.
5. Galaxy **Replace parts of text**, **Relabel identifiers of collection elements from a file**, **Replace column by values defined in a convert file**, and **Apply rules to reorganize a collection using the rule builder** were explored. Relabeling the collection does **not** itself change the second column inside each tabular dataset. The rule builder displayed two metadata columns: **A = SRR25158482** for all rows, **B = 001–023**. For unique element identifiers choose **B**, not A. No successful repair by these interfaces is established.
6. Downloading the collection and preparing the mapping outside Galaxy was chosen to avoid repeated ineffective GUI transformations.

## Working solution and checks

For each of the 23 MaxBin2 FASTA-derived tabular files, preserve **column 1** (the original contig ID) and replace **column 2** with the bin label derived from that file's actual element identifier, e.g. `001 → MaxBin2_001`, `002 → MaxBin2_002`, through `023 → MaxBin2_023`. Concatenate in a **headerless TSV**:

```text
k141_378841	MaxBin2_001
k141_1640297	MaxBin2_003
```

The examples above illustrate the required format and are not an independently revalidated complete assignment list. A corrected local artifact was supplied in the conversation as `MaxBin2_contigs2bin_corrected.tsv`, with a reported **23 bins**, **31,240 contig assignments**, and **zero duplicate contig IDs**. **These counts should be rechecked against the uploaded TSV and original MaxBin2 collection before publication as audited statistics.** The artifact is not automatically present in the GitHub repository or Galaxy history.

**Subsequent outcome:** The corrected TSV was uploaded as **#401** and successfully used in three-binner DAS Tool with **#22**, **#280**, and **#158**. DAS Tool produced six selected bins; CheckM2 then evaluated all six (quality report **#417**). Full provenance, results and outstanding issues are in [the completed DAS Tool and CheckM2 record](version2_dastool_checkm2_results.md). GTDB-Tk remains pending.

## Interpretation and limitations

All MetaBAT2, MaxBin2, CONCOCT, and DAS Tool outputs described here are **candidate genome bins**. Neither a green job nor a binner's internal completeness estimate is independent MAG quality validation. Preserve unsuccessful attempts and dataset identifiers for reproducibility. This is a running analysis record, not a Version 2.0 release.

**Method:** Sieber et al. (2018), *Nature Microbiology* 3:836–843, https://doi.org/10.1038/s41564-018-0171-1.
