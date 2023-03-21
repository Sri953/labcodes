# Splicing analyses using whippet

## Prepare whippet index
```bash
cd /path/to/experiment
mkdir -p outputs/whippet_index
echo -e "whippet_index:\tContain whippet indices for _" 1>> metadata/folders_description #change accordingly

whippet-index.jl --fasta /path/to/fasta --gtf /path/to/gtf -x outputs/whippet_index/custom
```

## Whippet quantification
```bash
cd /path/to/experiment
mkdir -p outputs/whippet_quant
mkdir -p outputs/whippet_SAMs
echo -e "whippet_quant:\tOutputs of whippet-quant function on trimmed FASTQs" 1>> metadata/folders_description
echo -e "whippet_SAMs:\tSAM alignments of trimmed FASTQs" 1>> metadata/folders_description

declare -A whippetArray
declare -A whippetNames
while IFS=$'\t' read -r FASTQ GENO SAMPLE REP;do
    whippetArray[$SAMPLE]="${whippetArray[$SAMPLE]} outputs/FASTQ_trimmed/$FASTQ"
done < metadata/fastq_list.txt

# The loop below outputs SAM file . Delete --sam flag and stdout if not needed
for sample in ${!whippetArray[@]}; do
    whippet-quant.jl ${whippetArray[$sample]} \
    -o ./outputs/whippet_quant/$sample -x /media/emlab/RAID5/Fursham/brad_anx11a/outputs/whippet_index/whippet_index.jls --force-gz --biascorrect --sam > ./outputs/whippet_SAMs/$sample.sam
done

```

## Whippet splicing differential
```bash
cd /media/emlab/RAID5/Fursham/brad_anx11a/outputs
mkdir -p whippet_deltapsi
echo -e "whippet_deltapsi:\tContain differential whippet analyses" 1>> ../metadata/folders_description

whippet-delta.jl -a ./path/to/rep1.psi.gz,./path/to/rep1.psi.gz -b ./path/to/rep1.psi.gz,./path/to/rep1.psi.gz -o ./whippet_deltapsi/outname -r 5 -s 3
```
## CLeanup output
```bash
cd /path/to/files
for filename in *.gene.tpm.gz; do
    file=${filename##*/}
    file=${file%.gz}
    gzip -d $filename 
    sed -i ':a;N;$!ba;s/\t\n/\n/g' $file
done
```

To convert the SAM to BAM, refer to hisat2.md document.