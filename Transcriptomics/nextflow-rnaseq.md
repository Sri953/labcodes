# nf-core/rnaseq template

The usage of this docker container can be found [here](https://nf-co.re/rnaseq/usage)

A typical command for running the pipeline
```bash
nextflow run nf-core/rnaseq --input samplesheet.csv --genome GRCh37 -profile docker

```

Usage can be invoked as such
```bash
nextflow run nf-core/rnaseq --help

```
Some of the parameters are listed [here](https://nf-co.re/rnaseq/3.0/parameters)

This is an example of an input file  

group,replicate,fastq_1,fastq_2,strandedness  
control,1,AEG588A1_S1_L002_R1_001.fastq.gz,AEG588A1_S1_L002_R2_001.fastq.gz,forward  
control,2,AEG588A2_S2_L002_R1_001.fastq.gz,AEG588A2_S2_L002_R2_001.fastq.gz,forward  
control,3,AEG588A3_S3_L002_R1_001.fastq.gz,AEG588A3_S3_L002_R2_001.fastq.gz,forward  
treatment,1,AEG588A4_S4_L003_R1_001.fastq.gz,,forward  
treatment,2,AEG588A5_S5_L003_R1_001.fastq.gz,,forward  
treatment,3,AEG588A6_S6_L003_R1_001.fastq.gz,,forward  
treatment,3,AEG588A6_S6_L004_R1_001.fastq.gz,,forward  

Where the columns refer to this:

|Column |	Description|
|:---|:---|
|group |	Group identifier for sample. This will be identical for replicate samples from the same experimental group.|
|replicate |	Integer representing replicate number. Must start from 1..<number of replicates>.|
|fastq_1 |	Full path to FastQ file for read 1. File has to be zipped and have the extension ".fastq.gz" or ".fq.gz".|
|fastq_2 |	Full path to FastQ file for read 2. File has to be zipped and have the extension ".fastq.gz" or ".fq.gz".|
|strandedness |	Sample strand-specificity. Must be one of unstranded, forward or reverse.|

My default 
```bash
nextflow run nf-core/rnaseq \
	--input /path/to/samplesheet.csv --outdir ./outputs/ \
	--aligner hisat2 --hisat2_index /path/to/hisat2index --splicesites /path/to/hisat2knownsplicesites \
	--fasta /path/to/genomefasta --gtf /path/to/gtfannotation \
	--max_cpus 35 -profile docker
nextflow run nf-core/rnaseq \
	--input ../metadata/nf_samples.txt --outdir ./ \
	--genome GRCm38 --stringtie_ignore_gtf \
	--igenomes_base /media/cdn-bc/RAID/Genomes/igenomes \
	--save_trimmed \
	--max_cpus 42 -profile docker

```

Some custom genomes that have been created on cdn-bc  
cdn-GRCm38-ens 	(tested)  
cdn-GRCm38-gencode (not-yet-tested)  
cdn-GRCm39-gencode	(not-yet-tested)  
cdn-GRch37-ens	(not-yet-tested)  

run using
--genome genome-id


**Some useful flags/parameters**  

 |||
 |:---|:---|
|--save\_merged\_fastq |Save FastQ files after merging re-sequenced libraries in the results directory. | 
|--public\_data\_ids 	|File containing SRA/ENA/GEO identifiers one per line in order to download their associated FastQ files.  |
|--remove\_ribo\_rna	|Enable the removal of reads derived from ribosomal RNA using SortMeRNA. **Program didn't complete its run** |
|--iGenomes 			|Name of iGenomes reference.  |
|--GENCODE 			|Specify if your GTF annotation is in GENCODE format.  |
|--stringtie\_ignore\_gtf 	|Perform reference-guided de novo assembly of transcripts using StringTie i.e. dont restrict to those in GTF file.  |
|--save_trimmed	 	|Save the trimmed FastQ files in the results directory.  |


 **Several switches to switch off analyses**  

 |||
 |:---|:---|
|--skip_trimming |Skip the adapter trimming step.  |
|--skip_alignment|Skip all of the alignment-based processes within the pipeline  |
|--skip_markduplicates| Skip picard MarkDuplicates step.  |
|--rseqc_modules| Specify the RSeQC modules to run. default:'bam_stat,inner_distance,infer_experiment,junction_annotation,junction_saturation,read_distribution,read_duplication'  |
|--deseq2_vst|Use vst transformation instead of rlog with DESeq2.  |
|--skip_bigwig|Skip bigWig file creation.  |
|--skip_stringtie|Skip StringTie.  |
|--skip_fastqc|Skip FastQC.  |
|--skip_preseq|Skip Preseq.  |
|--skip_dupradar|Skip dupRadar.  |
|--skip_qualimap|Skip Qualimap.  |
|--skip_rseqc|Skip RSeQC.|
|--skip\_biotype\_qc|Skip additional featureCounts process for biotype QC.| 
|--skip\_deseq2\_qc| Skip DESeq2 PCA and heatmap plotting.  |
|--skip_multiqc	|Skip MultiQC.|  
|--skip_qc |Skip all QC steps except for MultiQC.  |


