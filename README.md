# Influenza cold-adapted muations detection

Here we described a bioinformatics method to differentiate Ann Arbor and CA/09 influenza virus background; and detecte the presence/absense of cold-adapted mutations.

## Illumina raw reads pre-process

Software: Trimmomatic-0.39 (http://www.usadellab.org/cms/?page=trimmomatic); FastQC (https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)


```

java -jar Trimmomatic-0.39/trimmomatic-0.39.jar PE $path-to-inputfolder/*_R1_001.fastq.gz $path-to-inputfolder/*_R2_001.fastq.gz \ 
$path-to-outputfolder/forward_paired.fq.gz $path-to-outputfolder/forward_unpaired.fq.gz $path-to-outputfolder/reverse_paired.fq.gz $path-to-outputfolder/reverse_unpaired.fq.gz \
ILLUMINACLIP:Trimmomatic-0.39/adapters/TruSeq3-PE-2.fa:6:8:10:2:keepBothReads SLIDINGWINDOW:4:15 LEADING:3 TRAILING:3 MINLEN:36
#Trim adapters, cut off quality < 15 regions
#reverse_paired.fq.gz forward_paired.fq.gz will be kept for following analysis

fastqc -o outputfolder $path-to-outputfolder/forward_paired.fq.gz
fastqc -o outputfolder $path-to-outputfolder/reverse_paired.fq.gz

#check the quality

```
