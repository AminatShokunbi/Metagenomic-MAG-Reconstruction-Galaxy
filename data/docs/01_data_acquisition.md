# Step 1: Raw Metagenomic Data Acquisition

## Objective

Raw metagenomic sequencing reads were retrieved from the NCBI Sequence
Read Archive (SRA) for subsequent reconstruction of metagenome-assembled
genomes (MAGs).

## Selected SRA Runs

The following six SRA run accessions were selected:

- SRR18276513
- SRR30026880
- SRR30026879
- SRR18276515
- SRR18276516
- SRR18276520

## Data Retrieval

Raw sequencing reads were imported directly into Galaxy using the
NCBI SRA accession numbers.

The Galaxy tool **Download and Extract Reads in FASTQ** was used
to retrieve the sequencing data and convert the SRA records into FASTQ
datasets for downstream analysis.

For paired-end datasets, forward (R1) and reverse (R2) reads were
retained as paired sequencing datasets.

## Output

The output of this stage consists of the raw FASTQ sequencing reads
corresponding to the selected SRA runs.

The FASTQ files are not stored in this GitHub repository because of
their size. The accession numbers provided above allow the original
sequencing data to be retrieved directly from NCBI SRA.

## Next Step

Quality assessment of the raw sequencing reads using FastQC followed by
summarization of quality-control metrics with MultiQC.
