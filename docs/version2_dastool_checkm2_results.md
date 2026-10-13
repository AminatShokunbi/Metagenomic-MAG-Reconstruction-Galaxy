# Version 2.0 — Three-binner DAS Tool integration and CheckM2 genome quality (13 October 2026)

**Sample:** SRR25158482. **Galaxy:** Australia, history `MAG Recovery version 2,0`. **Status:** DAS Tool refinement and CheckM2 completed; GTDB-Tk classification pending. These are candidate genome bins, not yet a taxonomically classified final MAG catalogue.

## Three-binner DAS Tool execution

**Tool:** DAS Tool 1.1.7+galaxy1. Input contigs: filtered assembly **#22** (48,763 contigs ≥1,500 bp). Three input tables:

| Binner | Galaxy dataset | Input |
|---|---|---|
| MaxBin2 | **#401** | `MaxBin2_contigs2bin_corrected.tsv`; 23 bin IDs corrected to distinct labels |
| MetaBAT2 | **#280** | `Concatenate datasets`; individual table (not collection #279) |
| CONCOCT | **#158** | `Remove beginning on dataset 156`; individual table (not collection #157) |

The Galaxy job's DAS Tool log shows the three `--bins` paths and labels **`MaxBin2_contigs2bin_corrected.tsv,SRR25158482,SRR25158482`**. Since the latter two labels were identical, the program reported:

```text
Non-unique bin set labels given. Renaming bin set labels.
Non-unique bin-ids given. Renaming bin-ids.
```

The job nevertheless completed (`Writing bins`). In future reruns, set explicit prediction labels **MaxBin2**, **MetaBAT2**, **CONCOCT** to improve provenance; the current bin-set numbers `01`, `02`, `03` are automatically generated and should not be assumed to identify a particular algorithm without checking job metadata.

**Recorded parameters:** DIAMOND single-copy-gene search; selection score threshold 0.5; duplicate-marker penalty 0.6; megabin penalty 0.5; maximum post-threshold iterations 10; bin FASTA export TRUE; bin evaluations FALSE; unbinned export FALSE; 1 thread.

**Outputs:** summary **#406** (7 lines including header, 11 columns), contig-to-bin map **#407** (**6,281 rows**, 2 columns), log **#408**, and a collection of **six exported FASTA bins** (individual datasets **#409–#414**). The DAS Tool summary included one row with missing SCG metrics (`SRR25158482.binner.03_14_sub`); it was still exported and later evaluated successfully by CheckM2.

### DAS Tool selected-bin summary

| Exported dataset | Bin suffix | Contigs | DAS Tool SCG completeness (%) | SCG redundancy (%) |
|---|---|---:|---:|---:|
| #409 | `MaxBin2_011_sub` | 1,220 | 53 | 0 |
| #410 | `02_23` | 1,268 | 88 | 14 |
| #411 | `02_5_sub` | 351 | 71 | 4 |
| #412 | `03_14_sub` | not shown | missing | missing |
| #413 | `03_24_sub` | 822 | 69 | 0 |
| #414 | `03_47` | 653 | 71 | 0 |

DAS Tool summary reports #409 size 2,834,684 bp; #410 8,051,140 bp; #411 2,062,563 bp; #413 3,191,907 bp; #414 2,790,955 bp. These are **DAS Tool SCG-based estimates**, not CheckM2 estimates. Galaxy displays #413 as 822 FASTA sequences (3.3 MB) and #414 as 653 sequences (2.9 MB).

## CheckM2 quality assessment

**Tool:** CheckM2 **1.1.0+galaxy1**, using all six nucleotide FASTA inputs **#409–#414** in a single run. Reference: CheckM2 DIAMOND database downloaded with version **1.0.2**. `Treat input files as protein files = No`; `Model options = None` (automatic); Prodigal translation table not specified.

**Output:** quality report **#417**, 7 lines including header and 14 columns. DIAMOND_RESULTS **#424** was also produced (8,715 lines, 12 columns); it is a supporting alignment output, **not** the quality report.

| Galaxy FASTA | Bin ID (short) | CheckM2 completeness (%) | CheckM2 contamination (%) | Selected completeness model |
|---|---|---:|---:|---|
| #409 | `MaxBin2_011_sub` | 49.99 | 5.91 | Neural Network (specific) |
| #410 | `02_23` | 93.43 | 8.21 | Gradient Boost (general) |
| #411 | `02_5_sub` | 58.64 | 3.33 | Gradient Boost (general) |
| #412 | `03_14_sub` | 71.84 | 13.28 | Gradient Boost (general) |
| #413 | `03_24_sub` | 66.24 | 1.12 | Gradient Boost (general) |
| #414 | `03_47` | 81.64 | 0.65 | Neural Network (specific) |

**Interpretation:** #414 combines substantial completeness (81.64%) and very low contamination (0.65%); #413 has 66.24% completeness and 1.12% contamination; #411 has 58.64% completeness and 3.33% contamination. #410 is most complete (93.43%) but has 8.21% contamination. #412 has 13.28% contamination and should be investigated for refinement. #409 falls just below the 50% completeness cutoff and exceeds 5% contamination. Using the commonly applied **≥50% completeness and <10% contamination** numerical screen, **four** bins (#410, #411, #413, #414) pass. Using **≥50% and <5%**, **three** (#411, #413, #414) pass. **Neither screen alone establishes a formal MIMAG quality designation**: other criteria (including rRNA/tRNA recovery for high quality), classification, and contextual assessment remain necessary. None meets the ≥90% completeness and <5% contamination numerical combination for high-quality bins.

**Important:** DAS Tool single-copy-marker estimates and CheckM2 machine-learning estimates are methodologically different and should not be substituted for each other.

## Encountered issue and resolution

The original 23 MaxBin2 FASTA-derived tabular datasets in Galaxy collection **#377** incorrectly all had the second column `MaxBin2_001`, which would collapse separate bins into a single prediction. Attempts using Galaxy column operations and collection rule builders did not fix the contents. The solution was to derive each row's second-column bin ID from the original collection element identifier `001`–`023`, concatenate a **headerless two-column TSV**, and upload it as **#401**. A previously supplied corrected file was reported as containing **31,240 assignments from 23 bins, zero duplicated contig IDs**; independently audit these figures before presenting them as verified dataset statistics. This corrected file was accepted by DAS Tool and contributed bin #409.

## Remaining work

1. Run **GTDB-Tk** classification on all six FASTA bins; record the Galaxy tool/database versions, taxonomic assignments, warnings, and confidence indicators.
2. Examine contamination of #410 and #412, and potential refinement/reassessment; assess whether #409 is worth retaining for targeted analyses.
3. Check full CheckM2 report, additional genome characteristics, and relevant rRNA/tRNA recovery before final MIMAG quality assignments.
4. Preserve Galaxy history/exported workflow, exact input tables, and the corrected MaxBin2 mapping; document any new tool settings and reruns.
5. Build a final quality-assessed, taxonomically annotated MAG catalogue. **Do not mark Version 2.0 released or assign a new DOI until an actual release is made.**

## References

- Sieber, C. M. K. et al. (2018). Recovery of genomes from metagenomes via a dereplication, aggregation and scoring strategy. *Nature Microbiology*, 3, 836–843. https://doi.org/10.1038/s41564-018-0171-1
- Chklovski, A., Parks, D. H., Woodcroft, B. J., & Tyson, G. W. (2023). CheckM2: a rapid, scalable and accurate tool for assessing microbial genome quality using machine learning. *Nature Methods*, 20, 1203–1212. https://doi.org/10.1038/s41592-023-01940-w
- Bowers, R. M. et al. (2017). Minimum information about a single amplified genome (MISAG) and a metagenome-assembled genome (MIMAG) of bacteria and archaea. *Nature Biotechnology*, 35, 725–731. https://doi.org/10.1038/nbt.3893
