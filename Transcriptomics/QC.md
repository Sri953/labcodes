---
title: Quality control of short-read sequences
modified: 21st March 2023
modified.by: "Fursham"
tested.date: ""
tested.by: "Fursham"
---

Contents

1. To assess quality of short-reads
2. To trim reads for adapter and low-quality
3. To consolidate reports


## FastQC

https://www.bioinformatics.babraham.ac.uk/projects/fastqc/

```bash
cd {/PATH/TO/EXPERIMENT}
mkdir -p outputs/FastQC

fastqc -t 40 -o outputs/FastQC {/PATH/TO/FASTQ}/*.fastq.gz
```
## Consolidate FastQC outputs using MultiQC [Optional] 

https://multiqc.info/



```bash
cd {/PATH/TO/EXPERIMENT}/outputs
multiqc FastQC -o FastQC 
```

## Trimming reads using Trimmomatic

http://www.usadellab.org/cms/?page=trimmomatic


Below is the code for trimming reads by base-quality.  
The values at the end can be changed accordingly, and additional parameters can be added to trim adaptor sequence (TO BE ADDED)

```bash
cd {/PATH/TO/EXPERIMENT}/outputs
mkdir FASTQ_trimmomatic

# Change the regex pattern of FASTQ files accordingly
for FASTQ in {/PATH/TO/FASTQ}/{*_R1_001.fastq.gz};do
    name=${FASTQ##*/}
    name=${name%{_R1_001.fastq.gz}}
    java -jar trimmomatic PE -threads 40 -phred33 "FASTQdirectory/${newFASTQ}_R1_001.fastq.gz" "FASTQdirectory/${newFASTQ}_R2_001.fastq.gz" "FASTQ_trimmomatic/${newFASTQ}_R1_001.fastq.gz" "FASTQ_trimmomatic/unpaired_${newFASTQ}_R1_001.fastq.gz" "FASTQ_trimmomatic/${newFASTQ}_R2_001.fastq.gz" "FASTQ_trimmomatic/unpaired_${newFASTQ}_R2_001.fastq.gz" LEADING:10 TRAILING:10 SLIDINGWINDOW:5:20 MINLEN:40
done

```