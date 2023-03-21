## Cleaning up reads using cutadapt

```bash
cd /path/to/experiment
mkdir -p outputs/FASTQ_cutadapt
echo -e "outputs/FASTQ_cutadapt:\tDirectory cutadapt-trimmed reads" 1>> /metadata/folders_description # change dir accordingly

## The pattern below can be changed depending on how the files are named
for FASTQ in originals/FASTQ/*_R1_001.fastq.gz;do 
    newFASTQ=${FASTQ##*/}
    newFASTQ=${newFASTQ%_R1_001.fastq.gz}
    cutadapt -j 35 -m 40 -g 5_adaptor_seq -a 3_adaptor_seq -G 5_adaptor_seq_to_be_removed_from_second_read(optional) -A 3_adaptor_seq_to_be_removed_from_second_read(optional)  -o "./outputs/FASTQ_cutadapt/${newFASTQ}_R1_001.fastq.gz" -p "./outputs/FASTQ_cutadapt/${newFASTQ}_R2_001.fastq.gz" "./originals/FASTQ/${newFASTQ}_R1_001.fastq.gz" "./originals/FASTQ/${newFASTQ}_R2_001.fastq.gz"
done

```