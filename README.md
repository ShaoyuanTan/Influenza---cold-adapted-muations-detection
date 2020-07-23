# Influenza cold-adapted muations detection

Here we described a bioinformatics method to differentiate Ann Arbor and CA/09 influenza virus background; and detecte the presence/absense of cold-adapted mutations.

## Illumina raw reads pre-process

Software: T
Trim adapters, cut off quality < 15 regions

```
cd ~/software/trim

java -jar Trimmomatic-0.39/trimmomatic-0.39.jar PE ~/hello/schu1grp_160089_kapa_amplicon-1/2-925039/*_R1_001.fastq.gz ~/hello/schu1grp_160089_kapa_amplicon-1/2-925039/*_R2_001.fastq.gz ~/hello/results/trim/output_forward_paired_39.fq.gz ~/hello/results/trim/output_forward_unpaired_39.fq.gz ~/hello/results/trim/output_reverse_paired_39.fq.gz ~/hello/results/trim/output_reverse_unpaired_39.fq.gz ILLUMINACLIP:Trimmomatic-0.39/adapters/TruSeq3-PE-2.fa:6:8:10:2:keepBothReads SLIDINGWINDOW:4:15 LEADING:3 TRAILING:3 MINLEN:36

#check the quality
cd ~/hello/results/trim
~/software/QC/FastQC/fastqc -o ~/hello/results/QC output_forward_paired_39.fq.gz 
~/software/QC/FastQC/fastqc -o ~/hello/results/QC output_reverse_paired_39.fq.gz 
```
