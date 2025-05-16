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
FastqSifter --out=noMT --fasta=Dolioletta_gegenbauri_mtgenome.fa.gz --left=Dgeg.A.fq --right=Dgeg.B.fq --ump =Dgeg.unp.fq
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

Designated k=31 the optimal assembly (had highest N50). Generate artificial matepairs from each sub-optimal assembly below, which will be used to scaffold this assembly.

### MateMaker (generate mates for scaffolding)

##### for each assembly run the following:

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

### Remove short 

NOTE: remove_short_and_sort is available here: https://github.com/josephryan/RyanLabShortReadAssembly

```bash
remove_short_and_sort <SCAFFOLDED_ASSEMBLY> 200 > tmp.fa
```


