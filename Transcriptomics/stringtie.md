# Generating Custom Transcriptome using StringTie

Here is a template to perform custopm transcriptome assembly using Stringtie

## Assemble GTF for each sample

```bash
cd /path/to/output
mkdir Stringtie_gtf
echo -e "/outputs/Stringtie_gtf:\tDirectory containing Custom GTF for each sample" 1>> ../metadata/folders_description # change dir accordingly

for file in Hisat2_sorted_BAMs/*.bam; do
	newfile=${file##*/}
    newfile=${newfile%.bam}
	stringtie $file -p 35 -o Stringtie_gtf/$newfile.gtf -G /path/to/referenceGTF
done

```

## Merge transcriptome
```bash
mkdir Stringtie_merged
echo -e "/outputs/Stringtie_merged:\tDirectory containing merged custom GTF" 1>> ../metadata/folders_description # change dir accordingly
stringtie --merge -G /path/to/referenceGTF Stringtie_gtf/*.gtf | gzip > Stringtie_merged/+custom_transcriptome.gtf.gz

```
