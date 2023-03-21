# Trimming reads using Trimmomatic
Below is the code for trimming reads by base-quality.  
The values at the end can be changed accordingly, and additional parameters can be added to trim adaptor sequence (TO BE ADDED)

```bash
cd /path/to/outputs
mkdir FASTQ_trimmomatic
echo -e "outputs/FASTQ_trimmomatic:\tDirectory trimmomatic-trimmed reads" 1>> /metadata/folders_description # change dir accordingly


# Name of fastq files can be changed depending on experiment
for FASTQ in FASTQdirectory/*_R1_001.fastq.gz;do
    newFASTQ=${FASTQ##*/}
    newFASTQ=${FASTQ%_R1_001.fastq.gz}
    java -jar /usr/bin/Trimmomatic-0.39/trimmomatic-0.39.jar PE -threads 35 -phred33 "FASTQdirectory/${newFASTQ}_R1_001.fastq.gz" "FASTQdirectory/${newFASTQ}_R2_001.fastq.gz" "FASTQ_trimmomatic/${newFASTQ}_R1_001.fastq.gz" "FASTQ_trimmomatic/unpaired_${newFASTQ}_R1_001.fastq.gz" "FASTQ_trimmomatic/${newFASTQ}_R2_001.fastq.gz" "FASTQ_trimmomatic/unpaired_${newFASTQ}_R2_001.fastq.gz" LEADING:10 TRAILING:10 SLIDINGWINDOW:5:20 MINLEN:40
done

```