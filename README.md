# Influenza cold-adapted muations detection

Here we described a bioinformatics method to differentiate Ann Arbor and CA/09 influenza virus background; and detecte the presence/absense of cold-adapted mutations.

## Illumina raw reads pre-process

Software: Trimmomatic-0.39 (http://www.usadellab.org/cms/?page=trimmomatic); FastQC (https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

```
java -jar Trimmomatic-0.39/trimmomatic-0.39.jar PE $path-to-inputfolder/*_R1_001.fastq.gz $path-to-inputfolder/*_R2_001.fastq.gz \ 
$path-to-outputfolder/forward_paired.fq.gz $path-to-outputfolder/forward_unpaired.fq.gz \
$path-to-outputfolder/reverse_paired.fq.gz $path-to-outputfolder/reverse_unpaired.fq.gz \
ILLUMINACLIP:Trimmomatic-0.39/adapters/TruSeq3-PE-2.fa:6:8:10:2:keepBothReads \
SLIDINGWINDOW:4:15 LEADING:3 TRAILING:3 MINLEN:36
#Trim adapters, cut off quality < 15 regions
#reverse_paired.fq.gz forward_paired.fq.gz will be kept for following analysis

fastqc -o outputfolder $path-to-outputfolder/forward_paired.fq.gz
fastqc -o outputfolder $path-to-outputfolder/reverse_paired.fq.gz
#check the quality

```

## de novo assembly

Software: SPAdes-3.14.1 (https://github.com/ablab/spades); Quast (https://github.com/ablab/quast); Mummer (https://github.com/mummer4/mummer)

```
SPAdes-3.14.1-Linux/bin/spades.py --rna -1 output_forward_paired.fq.gz -2 output_reverse_paired.fq.gz \
-o $assembly-output-folder
quast-master/quast-lg.py $assembly-output-folder/hard_filtered_transcripts.fasta -m 36 \
-o $assembly-quality-assess-folder
#do novo assembly of influenza genome and quality check

./nucmer --maxmatch -p $name $reference.fasta $assembly-output-folder/hard_filtered_transcripts.fasta
./mummerplot $name.delta --postscript -p $name --color --filter --SNP --fat 
./dnadiff -d $name.delta -p output_$name
#mummer alignment of contigs with reference to check identity; mummerplot to generate alignment graph.

```

## Mapping to reference and variant calling

Software: bwa (https://github.com/lh3/bwa); samtools (https://github.com/samtools/samtools); bcftools (https://github.com/samtools/bcftools); picard(https://github.com/broadinstitute/picard)

```
bwa index $reference.fasta
bwa mem $reference.fasta forward_paired.fq.gz reverse_paired.fq.gz > $sample.sam
samtools sort $sample.sam -o $sample_sorted.bam
samtools index $sample_sorted.bam

java -jar -Xmx2000m picard.jar DownsampleSam INPUT=$sample_sorted.bam\
  OUTPUT=$sample_75pcReads.bam \
  RANDOM_SEED=50 PROBABILITY=0.75 VALIDATION_STRINGENCY=SILENT
samtools index $sample_75pcReads.bam

java -jar -Xmx2000m picard.jar DownsampleSam INPUT=$sample_sorted.bam\
  OUTPUT=$sample_50pcReads.bam \
  RANDOM_SEED=50 PROBABILITY=0.50 VALIDATION_STRINGENCY=SILENT
samtools index $sample_50pcReads.bam

java -jar -Xmx2000m picard.jar DownsampleSam INPUT=$sample_sorted.bam\
  OUTPUT=$sample_25pcReads.bam \
  RANDOM_SEED=50 PROBABILITY=0.25 VALIDATION_STRINGENCY=SILENT
samtools index $sample_25pcReads.bam

bcftools mpileup --redo-BAQ --min-BQ 20 --per-sample-mF -f $reference.fasta \
$sample_sorted.bam \
$sample_75pcReads.bam \
$sample_50pcReads.bam \
$sample_25pcReads.bam |\
bcftools call -mv -Ob -o $sample.bcf

bcftools view $sample.bcf | vcfutils.pl varFilter -p > $sample.vcf

```
## Visualizaion

$sample.vcf file can be visualized in IGV (http://software.broadinstitute.org/software/igv/).
