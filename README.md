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
grep @A00261:902:HGC52DSX7:2: -A 1 fp.fq | grep -v @A00261:902:HGC52DSX7:2: | grep -v ^- | wc -l
```
Counting total number of bases:
```bash
cat fp.fq rp.fq | grep @A00261:902:HGC52DSX7:2: -A 1 | grep -v @A00261:902:HGC52DSX7:2: | grep -v ^- | grep '[AGTCN]' -o | wc -l
```

## 4. MyGenome Assembly
Upload forward and reverse trimmed paired files to the MCC, copy VelvetOptimiser script into directory, and update email using nano.
```bash
cp ../SLURM_SCRIPTS/velvetoptimiser_noclean.sh .
nano velvetoptimiser_noclean.sh
```
Enter screen
```bash
screen -S velvet
```
Run Velvet with the Bcerus file with a range of kmer values:
```bash
velvetoptimiser -s 121 -e 201 -x 10 -d Bcereus_velvet_optimal -f '-shortPaired -fastq.gz -separate Br80_S1_L001_R1_001.fastq Br80_S1_L001_R2_001.fastq' -t 1
```
Run the SLURM on genome assemblies with a step size of 10:
```bash
sbatch velvetoptimiser_noclean.sh U248 61 131 10
```
Inspect the file to determine a range of values to test with a step size of 2:
```bash
cat slurm-21274794.out
```
Run the SLURM on genome assemblies with a step size of 2:
```bash
sbatch velvetoptimiser_noclean.sh U248 103 119 2
```
Use perl to remane the sequence headers to standard format.
```bash
perl /project/farman_s24cs485g/SCRIPTs/SimpleFastaHeaders.pl U248/velvet_U248_103_119_2_noclean/U248.fasta U248
```
## 5.BLASTing MyGenome

## 6. Assessing Genome with BUSCO
In the MCC, get the BuscoSingularity.sh script by copying it to the working directory.
```bash
scp SLURM_SCRIPTS/BuscoSingularity.sh agba242/
```
Run BUSCO:
```bash
sbatch /project/farman_s24cs485g/SLURM_SCRIPTS/BuscoSingularity.sh MyGenome.fasta
```
Analyze the output file:
```bash
cat slurm-21290970.out
```

## 7. Gene Prediction
