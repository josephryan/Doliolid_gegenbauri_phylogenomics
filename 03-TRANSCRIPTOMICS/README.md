# Transcriptomic analyses of Dolioletta gegenbauri

### Trinity (each transcriptomic dataset was assembled using the following command)

```bash
/usr/local/trinityrnaseq-v2.15.0/Trinity --seqType fq --max_memory 200G --CPU 40 --trimmomatic --left SAMPLE_NAME.R1.fastq.gz --right SAMPLE_NAME.R2.fastq.gz --full_cleanup --output SAMPLE_NAME.trinity
```

### Evigene (create a reference transcriptome from all trinity assemblies)

```bash
```
