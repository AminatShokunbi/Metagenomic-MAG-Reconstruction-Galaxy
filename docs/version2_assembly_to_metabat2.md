# Version 2.0 — SRR25158482: assembly and multi-binner reconstruction (work in progress)

**Status (11 October 2026):** MetaBAT2 and MaxBin2 each produced **23 candidate genome bins**. CONCOCT input preparation is in progress. DAS Tool, CheckM2, and GTDB-Tk remain pending. This is a running analysis log, **not** a finalized v2.0 release or an exported executable workflow.

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

## MaxBin2 — executed binning and preliminary results

Galaxy MaxBin2 **2.2.7+galaxy6** was run with the **length-filtered contig assembly (collection 21; individual dataset 22)** and a **derived contig-abundance table**. This input was prepared from the MetaBAT2 depth output by retaining contig identifiers and the total average depth column. The first sequencing-read-input attempt failed due to Galaxy collection mapping (two FASTQ elements mapped against one assembly element), so a precomputed abundance file was selected. Galaxy's successful result is titled **"MaxBin2 on collection 21 and 53: Summary"** (distinct from a previous job submission referencing datasets 22 and 54). Exact successful job parameters and whether the abundance file included its header require verification from the Galaxy job details/log; do not claim they were validated.

The MaxBin2 summary reports **23 candidate bins**, with genome sizes ranging from **1,515,588 to 9,327,700 bp** and GC content **29.1–74.6%**. The internally reported marker-based completeness estimates range **12.1–80.4%**. **10/23** bins are at least 50% complete by this internal estimate; **13/23** are below 50%. These are **not CheckM2 quality scores**; contamination remains unassessed.

| Bin | Abundance (MaxBin2 summary) | Internal completeness (%) | Genome size (bp) | GC (%) |
|---|---:|---:|---:|---:|
| out.001.fasta | 11.22 | 71.0 | 9327700 | 46.1 |
| out.002.fasta | 10.24 | 29.9 | 4279424 | 72.9 |
| out.003.fasta | 8.44 | 57.9 | 3381545 | 68.9 |
| out.004.fasta | 7.84 | 12.1 | 2573042 | 73.1 |
| out.005.fasta | 7.60 | 30.8 | 3586312 | 29.1 |
| out.006.fasta | 7.23 | 69.2 | 3348356 | 69.8 |
| out.007.fasta | 6.67 | 80.4 | 4226251 | 41.4 |
| out.008.fasta | 6.38 | 59.8 | 3097964 | 69.5 |
| out.009.fasta | 6.16 | 68.2 | 4125454 | 66.1 |
| out.010.fasta | 5.93 | 38.3 | 4194705 | 72.5 |
| out.011.fasta | 5.75 | 55.1 | 2886032 | 59.6 |
| out.012.fasta | 5.68 | 24.3 | 2325028 | 72.3 |
| out.013.fasta | 5.61 | 24.3 | 3252287 | 71.0 |
| out.014.fasta | 5.55 | 44.9 | 1515588 | 65.4 |
| out.015.fasta | 5.54 | 43.0 | 1681673 | 61.9 |
| out.016.fasta | 5.36 | 24.3 | 2025206 | 65.1 |
| out.017.fasta | 5.31 | 31.8 | 3518329 | 74.6 |
| out.018.fasta | 5.23 | 69.2 | 5213515 | 53.8 |
| out.019.fasta | 5.01 | 35.5 | 2520798 | 69.9 |
| out.020.fasta | 4.93 | 15.0 | 4410045 | 74.3 |
| out.021.fasta | 4.88 | 64.5 | 2434306 | 66.1 |
| out.022.fasta | 4.78 | 35.5 | 4037532 | 66.5 |
| out.023.fasta | 4.58 | 51.4 | 2977316 | 69.5 |

Neither identical bin counts across MetaBAT2 and MaxBin2 nor MaxBin2's internal completeness estimates establish that the two algorithms recovered identical genomes or that any bin is independently validated. Genome size alone (e.g., 9.33 Mb for out.001) is not evidence of contamination.

## CONCOCT — input preparation pending

Galaxy **CONCOCT 1.1.0+galaxy3** was opened, but the two required inputs could not yet be selected:
- **Coverage file** (`--coverage_file`): a tabular **contig × sample** average-coverage matrix, typically with contig identifiers and numeric per-sample coverage.
- **Composition file** (`--composition_file`): sequences in **FASTA** format with identifiers corresponding exactly to coverage rows.

The tool's selector displayed **"No datasets with tabular elements available"** and **"No datasets with fasta or fasta.gz elements available"**. This likely concerns input *collection element types/selector compatibility*, rather than proof that the history lacks those files; the history includes a filtered FASTA and depth-derived tables. Review whether selecting the **individual FASTA dataset 22** instead of a parent collection exposes the input. A CONCOCT-compatible coverage table must be prepared, ensuring column names/contig IDs and coverage values are correct. A MetaBAT2 mean-and-variance matrix is **not directly** the CONCOCT matrix; its per-sample coverage column must be extracted, not its variance column. Standard CONCOCT workflows may cut long contigs into parts before coverage calculation; if doing so, both coverage and composition must use matching **fragment IDs**. Since this is one sample, differential cross-sample coverage cannot be exploited; that limitation should be stated.

**Do not report CONCOCT as executed or infer chosen numerical parameters until the job is submitted and inspected.**

## Remaining planned workflow

```text
SRR25158482 paired-end reads
   -> QC / preprocessing
   -> MEGAHIT -> QUAST -> contig filtering
   -> Bowtie2 read-to-contig alignment -> depth estimates
   -> MetaBAT2 [23 candidate bins]
   -> MaxBin2 [23 candidate bins]
   -> CONCOCT [inputs pending]
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
5. Wu, Y.-W., Simmons, B. A., & Singer, S. W. (2016 [online 2015]). MaxBin 2.0: an automated binning algorithm to recover genomes from multiple metagenomic datasets. *Bioinformatics*, 32(4), 605–607. https://doi.org/10.1093/bioinformatics/btv638 (**used in Version 2.0**).

For platform, SRA Toolkit, FastQC, MultiQC, and Cutadapt citations, see [../CITATIONS.md](../CITATIONS.md). 6. Alneberg, J., Bjarnason, B. S., de Bruijn, I., Schirmer, M., Quick, J., Ijaz, U. Z., Lahti, L., Loman, N. J., Andersson, A. F., & Quince, C. (2014). Binning metagenomic contigs by coverage and composition. *Nature Methods*, 11(11), 1144–1146. https://doi.org/10.1038/nmeth.3103 (**CONCOCT planned; not yet run**).

Add references for DAS Tool, CheckM2 and GTDB-Tk when those methods are implemented.

**Archival note:** Updating GitHub documentation does not automatically establish that a previously reserved Zenodo DOI resolves; verify the Zenodo record and publication status separately before citing it as an accessible archive.
