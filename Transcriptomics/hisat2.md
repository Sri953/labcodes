# HISAT2 aligner code templates

Below are the various common codes to run HISAT2 program

## Generating HISAT2 indices
```bash
#Building index
cd /path/to/genome_dir
mkdir -p Hisat2_index

# The name of the index files will be as genome.x.ht# and will be saved in the Hisat2_index directory
hisat2-build -p 38 genome.fa Hisat2_index/genome

# Generate file with known splice junctions to be fed to HISAT2
hisat2_extract_exons.py genome.gtf > genome_known_splice_sites.txt
```

## Aligning Paired-end reads using HISAT2
```bash
cd /path/to/exp
mkdir Hisat2_SAMs
echo -e "/outputs/Hisat2_SAMs:\tSAM files. TO BE DELETED" 1>> ../metadata/folders_description # change accordingly

# parse fastq data into arrays
declare -A hisatArray
declare -A hisatNames
while IFS=$'\t' read -r FASTQ SAMPLEID REP SAMPLENAME REPNAME;do
  key="$SAMPLEID.$REP"
  hisatArray[$key]="18412R/Fastq/trimmed_FASTQ/$FASTQ"
  hisatNames[$SAMPLEID]=$SAMPLENAME
done < /path/to/fastq_list.txt

# loop through samples and align to GRCz11. 
### change n accordingly
for (( n=1; n<=12; n++ ));do
  hisat2 -p 40 --dta --known-splicesite-infile /path/to/genome_known_splice_sites.txt -x /path/to/Hisat2_index/genome \
  -1 ${hisatArray["$n.1"]} -2 ${hisatArray["$n.2"]} -S Hisat2_SAMs/${hisatNames[$n]}.sam --no-discordant
done
```

For Single-end reads, the same code can be used, but replace `-1` with `-U` and delete the pair.

## Converting SAMs to BAMs
Because HISAT output SAM files, we will have to convert to a more portable BAM format.

```bash
# sort and convert .sam files into BAM format
cd /path/to/outputdir
mkdir Hisat2_sorted_BAMs
echo -e "/outputs/Hisat2_sorted_BAMs:\tDirectory containing BAM files" 1>> ../metadata/folders_description # change dir accordingly

for filename in Hisat2_SAMs/*.sam; do
    file=${filename##*/}
    file=${file%.sam}
    samtools view -@ 40 -Su $filename | samtools sort -@ 40 -o Hisat2_sorted_BAMs/$file.bam
done

# index bam files
cd Hisat2_sorted_BAMs
for filename in *.bam; do
    samtools index -@ 40 $filename $filename.bai
done

```