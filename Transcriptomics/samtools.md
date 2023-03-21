# Playing with SAM/BAM files using Samtools

## Generating compact TDF files of aligned reads
```bash
cd /path/to/outputs/whippet_BAMs

# get the number of alinged reads
for file in *.bam; do
    printf "$file\t" >> /media/emlab/RAID5/Fursham/brad_anx11a/metadata/aligned_read.summary
    samtools view -@ 30 -F 0x4 $file | cut -f 1 | sort | uniq | wc -l >> /media/emlab/RAID5/Fursham/brad_anx11a/metadata/aligned_read.summary
done


cd /path/to/experiment
mkdir -p outputs/whippet_bedGraph
echo -e "whippet_bedGraphs:\tContain aligned read files in bedGraph format" 1>> metadata/folders_description

conda activate py3.7
while IFS=$'\t' read -r FILE DEPTH;do
    scalefactor=$(echo "1000000/$DEPTH" | bc -l)
    out=${FILE%.bam}
    echo $out
    bamCoverage -p 35 -bs 1 -of bedgraph --scaleFactor $scalefactor -b outputs/whippet_BAMs/$FILE -o outputs/whippet_bedGraph/$out.bedGraph
done < metadata/aligned_read.summary
conda deactivate

cd /path/to/experment/outputs
mkdir -p whippet_TDF
echo -e "whippet_TDF:\tContain aligned read files in TDF format" 1>> metadata/folders_description

for BAM in whippet_bedGraph/*.bedGraph; do
    file=${BAM##*/}
    file=${file%.bedGraph}
    igvtools toTDF whippet_bedGraph/$file.bedGraph whippet_TDF/$file.bedGraph.tdf GENOME
done


```