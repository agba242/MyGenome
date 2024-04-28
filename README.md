# MyGenome
Analyses for ABT480 genome assembly

## 1. Analysis of sequence quality
The F1 and R1 sequence datasets were analyzed using FASTQC:
```bash
ssh -Y agba242@agba242.cs.uky.edu
cd MyGenome/A28/
fastqc &
```
Load F1 and R1 datasets into GUI interface.
Take screen shots of output files:

![/data/U248_1.fq.qz.jpg](/data/U248_1.fq.qz.jpg)

## 2. Ran Trimmomatic
Sequences were trimmed using Trimmomatic:
```bash
java -jar trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog ../MyGenome/A28/U248_1.fq.gz U248_2.fq.gz U248_1_paired.fastq U248_1_unpaired.fastq U248_2_paired.fastq U248_2_unpaired.fastq CROP:280 SLIDINGWINDOW:20:20 MINLEN:120
```

## 3. Count number of forawrd reads remaining
Using the paired, forward reads, total number of paired reads can be counted:
```bash
grep -v '^@' U248_1.fq.gz | awk '{print length ($0)}' |
Counting total number of bases:
cat fp.fq rp.fq | grep -v CAGAGAGGAT+TCTACTCTGT | grep -v ^- | grep [AGTCN] -oic
```
