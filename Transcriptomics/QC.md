---
title: Quality control of short-read sequences
modified: 21st March 2023
modified.by: Fursham
tested.date: ""
tested.by: ""
---

1. To assess quality of short-reads
2. To trim reads for adapter and low-quality


## FastQC [v0.11.9]

https://www.bioinformatics.babraham.ac.uk/projects/fastqc/

```bash
cd {/PATH/TO/EXPERIMENT}
mkdir -p outputs/FastQC

fastqc -t 40 -o outputs/FastQC {/PATH/TO/FASTQ}/*.fastq.gz
```

## Trimmomatic [v0.39]

http://www.usadellab.org/cms/?page=trimmomatic


The code below performs base-quality trimming only
```bash
cd {/PATH/TO/EXPERIMENT}/outputs
mkdir FASTQ_trimmed

# Change the regex pattern of FASTQ files accordingly
for FASTQ in {/PATH/TO/FASTQ}/{*_R1_001.fastq.gz};do
    name=${FASTQ##*/}
    name=${name%{_R1_001.fastq.gz}}
    trimmomatic PE -threads 40 -phred33 \
    	{/PATH/TO/FASTQ}/${newFASTQ}_R1_001.fastq.gz {/PATH/TO/FASTQ}/${newFASTQ}_R2_001.fastq.gz \
    	FASTQ_trimmed/${newFASTQ}_R1_001.fastq.gz FASTQ_trimmed/unpaired_${newFASTQ}_R1_001.fastq.gz \
    	FASTQ_trimmed/${newFASTQ}_R2_001.fastq.gz FASTQ_trimmed/unpaired_${newFASTQ}_R2_001.fastq.gz \
    	LEADING:10 TRAILING:10 SLIDINGWINDOW:5:20 MINLEN:40
done

```
Quality and adaptor trimming can be performed as such:

