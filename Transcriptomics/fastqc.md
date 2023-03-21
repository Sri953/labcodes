# Quality control of reads using FastQC and MultiQC

## Running FastQC

```bash
cd /path/to/experiment
mkdir -p outputs/FastQC
echo -e "FastQC:\tFastQC output on raw FASTQ files" 1>> metadata/folders_description # change accordingly

fastqc -t 35 -o outputs/FastQC originals/FASTQ_DIRECTORY/*.fastq.gz
```
## Consolidate FastQC data using MultiQC

```bash
cd outputs
multiqc FastQC -o FastQC 
```

## Checking for overrepresented sequence
The code below can be used to preview sequences containing over-represented sequences

```bash
zgrep "seq" /path/to/FASTQ | head -n 5
```