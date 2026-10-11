# Version 2.0 — SRR25158482: assembly to first genome bins (work in progress)

**Status (10 October 2026):** MetaBAT2 completed; MaxBin2 configuration reviewed but **not yet confirmed as executed**. CONCOCT, DAS Tool, CheckM2, and GTDB-Tk remain pending. This is a running analysis log, **not** a finalized v2.0 release or an exported executable workflow.

## Scope and dataset

- Platform: Galaxy Australia; history: [MAG Recovery version 2.0](https://usegalaxy.org.au/histories/view?id=ce807aef12a66e50).
- Sample: NCBI SRA **SRR25158482**, paired-end reads.
- Input: **72,052,784 read pairs** (144,105,568 reads).
- Initial read QC and Cutadapt preprocessing were carried out earlier. The exact reads used for assembly versus read recruitment should be confirmed from Galaxy job provenance before asserting identical preprocessing.
- Bowtie2 mapping to **hg38** gave ~0.00% overall alignment in an earlier *host-screening demonstration*. This does **not** substitute for mapping reads back to assembled contigs, and does not establish complete absence of human DNA.

## Completed steps and results

| Stage | Tool / Galaxy version | Observed result |
|---|---|---|
| Assembly | MEGAHIT 1.2.9+galaxy2 | 1,993,724 contigs; 1,150,097,002 assembled bp; N50 586 bp; largest contig 62,007 bp |
| Assembly assessment | QUAST 5.3.0+galaxy1 | 881,554 contigs ≥500 bp, totaling 723,404,435 bp; highly fragmented assembly |
| Length filtering | Galaxy Filter sequences by length | ≥1,500 bp; **48,763 contigs retained** (~2.45% of original contig count); output ~124.5 MB |
| Read recruitment | Bowtie2 against **MEGAHIT contigs**, not hg38 | 23.44% overall alignment (mapping statistics); reads mapped against original unfiltered assembly |
| Contig coverage | jgi_summarize_bam_contig_depths, MetaBAT2 2.18.23+galaxy0 | 1,993,724 contigs in 5-column depth table; 144,105,568 reads processed; **23,440,736 well-mapped reads (16.2664%)** under 97% minimum identity; ~80.4 MB depth matrix |
| Initial binning | MetaBAT2 2.18.23+galaxy0 | **23 candidate bins** reported by Galaxy output collection; first bin (history item 30:1) contains 687 FASTA sequences (~3 MB) |

The QUAST run included reference-based comparisons; because reference genomes represent only a subset of the metagenomic community, reference-related quality metrics should not be interpreted as comprehensive community-wide accuracy estimates.

### Depth-calculation reproducibility

The successful Galaxy depth job used `jgi_summarize_bam_contig_depths` with `--percentIdentity 97 --minMapQual 0 --weightMapQual 0 --maxEdgeBases 75 --shredLength 16000 --shredDepth 5 --minContigLength 1 --minContigDepth 0`. Job completed with exit code 0, ~9-minute wall time, ~7.7 GiB peak memory. The depth matrix columns are `contigName`, `contigLen`, `totalAvgDepth`, sample mean depth, and sample depth variance. The coverage calculation used the original assembly so the depth matrix includes contigs shorter than the filtered binning input.

### MetaBAT2 caveats

MetaBAT2 used the filtered contig FASTA and the mean-plus-variance depth table (the `--abdFile` input, **not** the separate `--cvExt` field). The working threshold was 1,500 bp. **Verify exact values of all advanced MetaBAT2 parameters from its completed Galaxy job details before presenting them as executed settings.** The 23 outputs are *candidate genome bins*, not yet quality-assessed MAGs. A single sample does not supply cross-sample differential coverage. Low read recruitment and severe assembly fragmentation may constrain completeness and bin purity.

## Next: MaxBin2 (configuration under review)

Galaxy MaxBin2 **2.2.7+galaxy6** currently displays:
- Contigs: history **15**, original MEGAHIT assembly collection.
- Assembly type: **individual assembly**.
- Input type: **sequencing reads**; paired reads: history **1**, `fastq-dump` collection.
- The wrapper can map reads to contigs internally to derive abundance, so the MetaBAT2 mean-and-variance depth matrix is **not** interchangeable with a MaxBin2 abundance input.
- Reassembly is experimental; leave disabled for a baseline run.
- Confirm whether the original or quality-trimmed reads were used in assembly, and review MaxBin2's length threshold and other defaults before running.
- Using the original assembly for MaxBin2 and ≥1,500-bp filtered assembly for MetaBAT2 introduces different input contig sets. This is permissible but should be reported, and a harmonized-input comparison may be preferable before DAS Tool integration.

**No MaxBin2 result is claimed yet.**

## Remaining planned workflow

```text
SRR25158482 paired-end reads
   -> QC / preprocessing
   -> MEGAHIT -> QUAST -> contig filtering
   -> Bowtie2 read-to-contig alignment -> depth estimates
   -> MetaBAT2 [23 candidate bins]
   -> MaxBin2 [pending]
   -> CONCOCT [pending]
   -> DAS Tool: integrate nonredundant bin assignments [pending]
   -> CheckM2: estimate completeness / contamination [pending]
   -> GTDB-Tk: classify quality-assessed bins [pending]
```

DAS Tool needs consistent contig IDs and contig-to-bin assignments for each binner; preparation and harmonization of the three binner outputs must be verified. The recovered-bin count must not be interpreted as a count of validated MAGs before quality assessment.

## Citations

1. Li, D., Liu, C.-M., Luo, R., Sadakane, K., & Lam, T.-W. (2015). MEGAHIT: an ultra-fast single-node solution for large and complex metagenomics assembly via succinct de Bruijn graph. *Bioinformatics*, 31(10), 1674–1676. https://doi.org/10.1093/bioinformatics/btv033
2. Gurevich, A., Saveliev, V., Vyahhi, N., & Tesler, G. (2013). QUAST: quality assessment tool for genome assemblies. *Bioinformatics*, 29(8), 1072–1075. https://doi.org/10.1093/bioinformatics/btt086
3. Langmead, B., & Salzberg, S. L. (2012). Fast gapped-read alignment with Bowtie 2. *Nature Methods*, 9, 357–359. https://doi.org/10.1038/nmeth.1923
4. Kang, D. D., Li, F., Kirton, E., Thomas, A., Egan, R., An, H., & Wang, Z. (2019). MetaBAT 2: an adaptive binning algorithm for robust and efficient genome reconstruction from metagenome assemblies. *PeerJ*, 7, e7359. https://doi.org/10.7717/peerj.7359
5. Wu, Y.-W., Simmons, B. A., & Singer, S. W. (2016 [online 2015]). MaxBin 2.0: an automated binning algorithm to recover genomes from multiple metagenomic datasets. *Bioinformatics*, 32(4), 605–607. https://doi.org/10.1093/bioinformatics/btv638 (**planned; not yet run**).

For platform, SRA Toolkit, FastQC, MultiQC, and Cutadapt citations, see [../CITATIONS.md](../CITATIONS.md). Add references for CONCOCT, DAS Tool, CheckM2 and GTDB-Tk when those methods are implemented.

**Archival note:** Updating GitHub documentation does not automatically establish that a previously reserved Zenodo DOI resolves; verify the Zenodo record and publication status separately before citing it as an accessible archive.
