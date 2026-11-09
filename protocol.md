# RNA-seq analysis of _Culex pipiens_

- Author: Nabina Karki
- Date created: 2026-11-08
- Date last modified: 2026-11-08
- This was run at the pitzer cluster of the Ohio Supercomputer Center (<https://www.osc.edu>)
- OSC working dir: GA5
- GitHub repository: https://github.com/Nabinak/GA5

## Prerequisites

- **Input files**:
  - Paired-end RNA-seq reads are in `../garrigos/fastq`

- **Software**:
  - The scripts run programs using Apptainer containers,
    with links contained inside the scripts.
  - If running at OSC, no software installations are needed.
  - If you're not at OSC, you need to have Apptainer and Slurm installed.

- Run all code from the working dir listed above.
  You don't need to create the output dirs, because the scripts will do so.

- Each script writes a Slurm log file (`slurm-<script>-<jobID>.out`) to the working dir.
  After the jobs of a step have finished, check these logs for errors,
  then move them into a `logs/` dir within that step's output dir.

## Set up: Download the reference transcriptome FASTA file from NCBI

```bash
URL=https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/016/801/865/GCF_016801865.2_TS_CPP_V2/GCF_016801865.2_TS_CPP_V2_rna.fna.gz
wget -P data/ref "$URL"
```

## Set up: Set file paths

```bash
# Inputs:
fastq_dir=../garrigos/fastq
ref_transcripts=data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz

# Base output dir:
outdir=results
```

## Step 1: Read trimming with TrimGalore

`trimgalore.sh` runs TrimGalore on the paired-end FASTQ files of 1 sample,
and gives the output files the same names as the input files.
It takes 3 arguments: the R1 FASTQ file, the R2 FASTQ file, and the output dir.

```bash
for fastq_r1 in "$fastq_dir"/*_R1.fastq.gz; do
    fastq_r2=${fastq_r1/_R1/_R2}
    sbatch scripts/trimgalore.sh "$fastq_r1" "$fastq_r2" results
done
```

## Step 2: Creating a transcriptome index with Salmon

`salmon_index.sh` creates a Salmon index of the reference Transcriptome FASTA file. It takes 2 arguments: the transcriptome FASTA file and the output dir. 

```bash
sbatch scripts/salmon_index.sh "$ref_transcripts" "$outdir"/salmon_index
```

## Step 3: Pseudo-alignment and quantification with Salmon

```bash
for fastq_r1 in "$outdir"/*_R1.fastq.gz; do
    fastq_r2=${fastq_r1/_R1/_R2}
    sbatch scripts/salmon_quant.sh "$fastq_r1" "$fastq_r2" "$outdir"/salmon_index "$outdir"/salmon_quant
done
```


## Step 4: Summarizing the results of all steps with MultiQC

`multiqc.sh` runs multiqc. It takes 2 arguments: input dir and output dir. 

```bash
sbatch scripts/multiqc.sh results results/multiqc
```