# Read Trimming, Quality Control, and Metagenomic Assembly

## 1. Adapter identification

Initial read quality was assessed using FastQC and summarized using
MultiQC.

The MultiQC results showed adapter-associated sequence content in the
sequencing reads. The principal sequence types identified during this
assessment included Nextera-associated sequences and poly-A sequence
content.

These observations were used to guide read preprocessing rather than
removing sequences solely on the basis of FastQC warning or failure
flags.

---

## 2. Adapter and poly-A trimming

Read preprocessing was performed using Cutadapt in Galaxy.

The paired-end reads were processed to remove the identified adapter
sequence and poly-A/poly-T sequence content while preserving the
paired-end structure of the sequencing data.

Cutadapt was configured to trim matching adapter sequences rather than
discard all reads containing adapter matches.

---

## 3. Post-trimming quality assessment

Following Cutadapt processing, FastQC was run again on the trimmed
paired-end reads.

The FastQC outputs were flattened into individually named datasets and
combined using MultiQC.

Post-trimming MultiQC results were inspected to evaluate:

- adapter content
- per-sequence quality scores
- sequence-length distribution
- sequence duplication
- GC content
- overrepresented sequences

Adapter-associated sequence content was substantially reduced compared
with the initial QC assessment. Variation in sequence length was
expected after trimming because adapter and poly-A/poly-T sequence
removal shortened affected reads.

The post-trimming per-sequence quality distributions remained
predominantly within the high-quality range.

---

## 4. Metagenomic assembly

Quality-controlled paired-end reads were assembled using MEGAHIT in
Galaxy.

MEGAHIT was run separately for each paired-end dataset rather than
merging all sequencing runs into a single co-assembly.

The assembly used multiple k-mer sizes:

21, 29, 39, 59, 79, 99, 119, 141

The minimum k-mer multiplicity was set to 2.

A minimum contig output length of 200 bp was used so that the complete
assembly output could be retained for assessment.

MEGAHIT log files were also generated to preserve information about the
assembly process.

---

## 5. Assembly quality assessment

The MEGAHIT assemblies were evaluated using QUAST.

The preliminary QUAST results indicate that some assemblies are highly
fragmented.

For example, SRR18276513 produced:

- 656 contigs when all contig lengths were considered
- 32 contigs ≥500 bp
- 0 contigs ≥1,000 bp
- largest contig: 743 bp
- N50: 559 bp
- GC content: 44.44%

SRR18276515 produced:

- 264 contigs when all contig lengths were considered
- 25 contigs ≥500 bp
- 1 contig ≥1,000 bp
- largest contig: 1,696 bp
- N50: 582 bp
- GC content: 43.51%

These results indicate limited assembly contiguity for these datasets.
Because this project is being performed as a practical reconstruction
of the complete reads-to-MAG workflow, the assemblies will be retained
for subsequent binning and MAG-quality assessment.

Any bins recovered downstream will therefore be evaluated critically
using genome-quality metrics before being considered MAGs.

---

## Next Step

The next stage of the workflow is genome binning.

Where required by the selected binning method, quality-controlled reads
will first be mapped back to the assembled contigs to obtain coverage
information. The resulting assembly and coverage information will then
be used for genome binning with tools such as MetaBAT2 and MaxBin2.
