# Genome Assembly of Dolioletta gegenbauri

### Trimmomatic (remove adapters)

```bash
java -jar /usr/local/Trimmomatic-0.39/trimmomatic-0.39.jar PE -threads 45 -phred33 R1.fq R2.fq trim.R1.fq trim.unp1.fq trim.R2.fq trim.unp2.fq ILLUMINACLIP:TruSeq3PE_plus_adapters_from_fastqc.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```

```bash
cat trim.unp1.fq trim.unp2.fq > trim.unp.concat.fq
```

### ErrorCorrectReads.pl (error correct Illumina reads)

```bash
perl /usr/local/allpathslg-44837/src/ErrorCorrectReads.pl PAIRED_READS_A_IN=trim.R1.fq PAIRED_READS_B_IN=trim.R2.fq UNPAIRED_READS_IN=trim.unp.concat.fq PAIRED_SEP=100 PHRED_ENCODING=33 READS_OUT=Dgeg

### FastqSifter (filter out mitochondrial reads)

```bash
FastqSifter --out=noMT --fasta=Dolioletta_gegenbauri_mtgenome.fa --left=Dgeg.A.fq --right=Dgeg.B.fq --unp =Dgeg.unp.fq
```

### Platanus (short-read assemblies)

NOTE: plat.pl automates a set of platanus commands. It's available here https://github.com/josephryan/RyanLabShortReadAssembly
 
```bash
plat.pl --out=plat31 --k=31 --m=500 --left=noMT.A.fq --right=noMT.B.fq --unp=noMT.unp.fq
plat.pl --out=plat45 --k=45 --m=500 --left=noMT.A.fq --right=noMT.B.fq --unp=noMT.unp.fq
plat.pl --out=plat59 --k=59 --m=500 --left=noMT.A.fq --right=noMT.B.fq --unp=noMT.unp.fq
plat.pl --out=plat73 --k=73 --m=500 --left=noMT.A.fq --right=noMT.B.fq --unp=noMT.unp.fq
plat.pl --out=plat87 --k=87 --m=500 --left=noMT.A.fq --right=noMT.B.fq --unp=noMT.unp.fq
plat.pl --out=plat93 --k=93 --m=500 --left=noMT.A.fq --right=noMT.B.fq --unp=noMT.unp.fq
plat.pl --out=plat99 --k=99 --m=500 --left=noMT.A.fq --right=noMT.B.fq --unp=noMT.unp.fq
```

### Evaluate short-read assemblies

Designated k=31 the optimal assembly (had highest N50). Next we will scaffold the k=31 assembly with the sub-optimal platanus assemblies.

### MateMaker (generate mates from sub-optimal shortread assemblies for scaffolding)

##### for each sub-optimal assembly run the following:

NOTE: matemaker is available here: https://github.com/josephryan/MateMaker

```bash
matemaker --print_inserts --print_coords --assembly=<ASSEMBLY> --insertsize=2000 --out=<NAME>.2k
matemaker --print_inserts --print_coords --assembly=<ASSEMBLY> --insertsize=5000 --out=<NAME>.5k
matemaker --print_inserts --print_coords --assembly=<ASSEMBLY> --insertsize=10000 --out=<NAME>.10k
matemaker --print_inserts --print_coords --assembly=<ASSEMBLY> --insertsize=20000 --out=<NAME>.20k
```

### SSPACE (scaffold optimal assembly)

```bash
SSPACE_Standard_v3.0.pl -l illumina_libraries.txt -s scaffolds.reduced.fa -T 48 -k 5 -a 0.7 -x 0 -b Doli_scf
```

### Redundans (remove redundant heterozygous scaffolds) 

NOTE: Redundans https://academic.oup.com/nar/article/44/12/e113/2457531

```bash
redundans.py -v -i Doli_noMT.filtered.A.fq Doli_noMT.filtered.B.fq -f  <SCAFFOLDED_ASSEMBLY> -o rd-tmp.fa --threads 120
```

### MateMaker long reads (generate mates from PacBio and Nanopore reads)

NOTE: all reads were converted to FASTA and placed in a file called all_longreads.fasta

```bash
matemaker --assembly all_longreads.fa --insertsize=1000 --out=01k &
matemaker --assembly all_longreads.fa --insertsize=2000 --out=02k &
matemaker --assembly all_longreads.fa --insertsize=3000 --out=03k &
matemaker --assembly all_longreads.fa --insertsize=4000 --out=04k &
matemaker --assembly all_longreads.fa --insertsize=5000 --out=05k &
matemaker --assembly all_longreads.fa --insertsize=6000 --out=06k &
matemaker --assembly all_longreads.fa --insertsize=7000 --out=07k &
matemaker --assembly all_longreads.fa --insertsize=8000 --out=08k &
matemaker --assembly all_longreads.fa --insertsize=9000 --out=09k &
matemaker --assembly all_longreads.fa --insertsize=10000 --out=10k &
matemaker --assembly all_longreads.fa --insertsize=11000 --out=11k &
matemaker --assembly all_longreads.fa --insertsize=12000 --out=12k &
matemaker --assembly all_longreads.fa --insertsize=13000 --out=13k &
matemaker --assembly all_longreads.fa --insertsize=14000 --out=14k &
matemaker --assembly all_longreads.fa --insertsize=15000 --out=15k &
matemaker --assembly all_longreads.fa --insertsize=16000 --out=16k &
matemaker --assembly all_longreads.fa --insertsize=17000 --out=17k &
matemaker --assembly all_longreads.fa --insertsize=18000 --out=18k &
matemaker --assembly all_longreads.fa --insertsize=19000 --out=19k &
matemaker --assembly all_longreads.fa --insertsize=20000 --out=20k &
matemaker --assembly all_longreads.fa --insertsize=21000 --out=21k &
matemaker --assembly all_longreads.fa --insertsize=22000 --out=22k &
matemaker --assembly all_longreads.fa --insertsize=23000 --out=23k &
matemaker --assembly all_longreads.fa --insertsize=24000 --out=24k &
matemaker --assembly all_longreads.fa --insertsize=25000 --out=25k &
matemaker --assembly all_longreads.fa --insertsize=26000 --out=26k &
matemaker --assembly all_longreads.fa --insertsize=27000 --out=27k &
matemaker --assembly all_longreads.fa --insertsize=28000 --out=28k &
matemaker --assembly all_longreads.fa --insertsize=29000 --out=29k &
matemaker --assembly all_longreads.fa --insertsize=30000 --out=30k &
```

### SSPACE (scaffold short read assembly w artificial mate pairs from long reads)

```bash
SSPACE_Standard_v3.0.pl -l longread_libraries.txt -s tmp.fa -T 48 -k 5 -a 0.7 -x 0 -b Dgeg_redundans_scf_w_pacbio_and_minion
```

### Break big gaps

NOTE: break_big_gaps.pl is in this repository

```bash
break_big_gaps.pl Dgeg_redundans_scf_w_pacbio_and_minion/Dgeg_redundans_scf_w_pacbio_and_minion.final.scaffolds.fasta 5000 > Dgeg_redundans_scf_w_pacbio_and_minion_no5kgaps.fa
```


