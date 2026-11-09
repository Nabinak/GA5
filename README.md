![alt text](image-3.png)

# GA5 answers - Nabina Karki

------------------------------------------------------------------------

## Part A: Your project dir, Git repo, and protocol

### 1: Fill in the top of the protocol

### 2: Markdown formatting

### 3: Create subdirs, and copy the scripts

```bash
mkdir -p scripts data/ref results
```

### 4: Initialize a Git repository, and commit your first files


### 5: Download the reference transcriptome


Code to count the number of transcripts:

```bash
ls -lh data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz 
```

The output of the command was:

```
-rw-rw----+ 1 nabinak77 PAS3493 19M Dec 22  2022 data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz
```

*Answer*: The number of transcripts in the file: 19M

------------------------------------------------------------------------

## Part B: Read trimming

### 6: Run TrimGalore


*Answer*: Whether all batch jobs succeeded, and how you checked that
(and any problems you encountered, and how you solved them): I had a issue with running the batch job because I did not define the input file path correctly and also I also had started a new terminal. Claude was helpful in suggesting if I am not in the right working dir, then I realized may be I it could be because started new terminal. So I run input path and loop together in the new terminal which fixed the issue. 

------------------------------------------------------------------------

## Part C: Transcript quantification with Salmon

### 7: Create the Salmon index

Code to check the size of the index dir:

```bash
du -sh results/salmon_index/
```

The output of the command was:

```
1.1G    results/salmon_index/
```

*Answer*: How long it took to create the index, and how you found out: It took 33 sec. I checked the slurm-salmon_index log file and it shows it started at Sun Nov  8 06:09:16 PM EST 2026 and finished at Sun Nov  8 06:09:39 PM EST 2026. 

*Answer*: The size of the index dir: 1.1G 

### 8: Run Salmon for all samples


### 9: The transcripts with the most reads

*Answer*: What the `NumReads` column contains: This include the estimated number of read done by salmon for the reads which were mappped to each transcript and quantified. This consider the expected number of reads that may be generated from individual transcript for all the mapping. 

```bash
tail -n +2 results/salmon_quant/S63/quant.sf | sort -k5,5nr | head -n 3
```

The output of the command was:

```
XM_039591534.2  6566    6315.086        14518.656821    24004.889
XM_039573735.1  6445    6194.086        5342.073324     8663.254
XM_039573733.1  6591    6340.086        2876.480244     4774.749
```

### 10: The gene of the top transcript

```bash
zgrep "XM_039591534.2" data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | head -n 1
```

The output of the command was:

```
>XM_039591534.2 PREDICTED: Culex pipiens pallens vitellogenin-A1-like (LOC120426760), mRNA
```

*Answer*: The gene that the top transcript belongs to: LOC120426760

Here, I would like to add that I gave the code as grep only and it did not produce result so I asked claude for the help with what can I do and it suggested zgrep, as it decompresses gzip file before searching. 

### 11: Mapping rates

```bash
grep "mapping rate" results/salmon_quant/*/logs/*.log
```

The output of the command was:

```
results/salmon_quant/S63/logs/salmon_quant.log:mapping rate: 82.4560%
results/salmon_quant/S64/logs/salmon_quant.log:mapping rate: 81.9152%
results/salmon_quant/S65/logs/salmon_quant.log:mapping rate: 79.1055%
results/salmon_quant/S66/logs/salmon_quant.log:mapping rate: 81.9350%
results/salmon_quant/S67/logs/salmon_quant.log:mapping rate: 83.0459%
results/salmon_quant/S68/logs/salmon_quant.log:mapping rate: 78.8070%
results/salmon_quant/S69/logs/salmon_quant.log:mapping rate: 82.1824%
results/salmon_quant/S70/logs/salmon_quant.log:mapping rate: 82.3060%
results/salmon_quant/S71/logs/salmon_quant.log:mapping rate: 79.9047%
results/salmon_quant/S74/logs/salmon_quant.log:mapping rate: 78.2753%
results/salmon_quant/S75/logs/salmon_quant.log:mapping rate: 80.3915%
results/salmon_quant/S76/logs/salmon_quant.log:mapping rate: 82.3396%
results/salmon_quant/S77/logs/salmon_quant.log:mapping rate: 82.3281%
results/salmon_quant/S78/logs/salmon_quant.log:mapping rate: 84.2490%
results/salmon_quant/S79/logs/salmon_quant.log:mapping rate: 80.4061%
results/salmon_quant/S80/logs/salmon_quant.log:mapping rate: 82.1721%
results/salmon_quant/S81/logs/salmon_quant.log:mapping rate: 83.6795%
results/salmon_quant/S82/logs/salmon_quant.log:mapping rate: 78.7291%
results/salmon_quant/S83/logs/salmon_quant.log:mapping rate: 76.1504%
results/salmon_quant/S84/logs/salmon_quant.log:mapping rate: 83.2207%
results/salmon_quant/S85/logs/salmon_quant.log:mapping rate: 76.9540%
results/salmon_quant/S86/logs/salmon_quant.log:mapping rate: 83.0532%
```

*Answer*: The sample with the lowest mapping rate: S83

------------------------------------------------------------------------

## Part D: Summarizing all steps with MultiQC

### 12: Update your MultiQC script

<!-- No need to paste the script: we'll look at scripts/multiqc.sh and its Git history directly. -->

### 13: Run MultiQC

*Answer*: For each program, the number of reports that MultiQC found,
and why it found this number: It found 22 reports with salmon and 44 reports with Fastqc and Trimgalore. This is because Trimgalore and Fastqc process individual forward (R1) and reverse (R2) reads but Salmon process a forward and reverse reads as a pair. 

### 14: The General Statistics table

*Answer*: The sample with the lowest Salmon mapping rate,
and whether this matches your answer to question 11: S83 has lowest salmon mapping rate and it matches my answer with question 11.

### 15: Adapter lengths

*Answer*: The most common adapter lengths, whether this surprised you,
and why you think these lengths are the most common: The most common adapter lengths seems to be 1 and 3 bp. I think this is not very surprising because trimgalore has that autodetection of may be 13 bp so this trimming can be considered normal. 

### 16: Ask Claude about the MultiQC report

*Answer*: What you asked, and what you learned from Claude's answer (in your own words):
I asked about the salmon fragment length distribution. It got more clear about this program map both reads to the transcriptome to measure the common size of cDNA fragments generated for the library preparation. I wanted make sure on what peaks suggested on the graph and learned it is the most common fragment length in the library and most of them having the same peak suggests that samples were prepared very consistently and this is a very good thing. 

------------------------------------------------------------------------

## Part E: Improving the workflow with genAI

### 17: Claude's review of your protocol and scripts

*Answer*: At least three of Claude's suggestions,
and whether you agree or disagree with each one, and why:

The first suggestion was to correct about arguments multiqc take as I initially has written that multiqc takes one argument but rather it takes two argument so I corrected that as I agree with it. 

The second suggestion Claude gave was No instruction to verify jobs finished before moving to the next step. Steps 1→2→3→4 are sequential dependencies (salmon_quant needs trimmed reads; multiqc needs everything), but nothing in the protocol says "wait for all N trimgalore jobs to finish and check logs before running Step 2/3." The "check logs after jobs finish" note is generic and placed once at the top, not reinforced per step. I do not necessarily agree with this because when we are write the protocol it is not like the script which we run together. This is simply one step at a time so I feel such instruction can be added but not necessarily important or requirement to add. 

The third suggestion is Give TrimGalore its own output subdir (results/trimgalore/) instead of dumping into bare results/, consistent with salmon_index/salmon_quant/multiqc each having their own subdir. Then update the Step 3 glob to read from that subdir. This is the single highest-value fix — it turns a 400+ file flat mess into a navigable structure. I think this could be good suggestion as I have sub dir for other program I ran but not for the trimgalore. 

### 18: Address a suggestion

*Answer*: Which suggestion(s) you addressed, and what you changed:

I made the changes about multiqc on the protocol. 

------------------------------------------------------------------------

## Part F: Publish your repo on GitHub

### 19: Create a GitHub repository and push


### 20: Get a (test) DOI with Zenodo

*Answer*: Your Zenodo Sandbox DOI: [![DOI](https://sandbox.zenodo.org/badge/1450944700.svg)](https://handle.test.datacite.org/10.5072/zenodo.637584)
