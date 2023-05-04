PLANNED ANALYSES FOR ASSEMBLING DOLOILID GENOME AND TRANSCRIPTOME AND INFERRING PHYLOGENETIC RELATIONSHIPS
Investigators: Adolfo Lara, Joseph Ryan, Bradley Davidson
Draft or Version Number: v.1.0
Date: 4 May 2023

## 1 INTRODUCTION: BACKGROUND INFORMATION AND SCIENTIFIC RATIONALE  

### 1.1 _Background Information_  

The goal of this analysis is to place Doliolum gegenbauri on the tunicate tree of life. To do this we will integrate new genomic data from D. gegenbauri into the phylogenomic matrix from Debiasse et al. (2020). That previous study integrated data from several other recent studies (Kocot et al. 2018; Delsuc et al. 2018; Alie et al. 2018).

### 1.2 _Rationale_  

Besides placing D. gegenbaurin on the tree of tunicates, it also provides a pipeline for integrating new datasets into previous phylogenomic studies.

### 1.3 _Objectives_  

Place Doliolum gegenbauri on the tunicate tree of life. Generate a pipeline for integrating new data into existing phylogenomic matrices.


## 2 STUDY DESIGN AND ENDPOINTS  

#### 2.1 Create hidden Markov model from the individual alignments that made up the phylogenomic matrix in DeBiasse et al. (2020). 

2.1.1 Use hmmbuild from the HMMer package (Eddy, 1998) to create new hmm profiles from each gene alignment that makes up the Tunicata phylogenomic matrix from (DeBiasse et al, 2020)

```hmmbuild -O ORTHOGROUP.msa ORTHOGROUP.hmm ORTHOGROUP_ALN_FROM_DEBIASSE.fa```

2.1.2 hmmbuild often creates an hmm that has fewer columns than the original alignment (it prunes gappy columns).  In the previous step we use the "-O" option to output the alignment that makes up the hmm (gappy cols coded as lower case).  We use a custom script to remove the lowercase columns.

```remove_hmmer_gaps.pl ORTHOGROUP.msa ORTHOGROUP > ORTHOGROUP.nogaps.fa```

2.1.3 Use hmm2aln.pl (https://github.com/josephryan/hmm2aln.pl) to align Doliolid sequences to Corella Genome matrix genes (n=210).

```hmm2aln.pl --name=ORTHOGROUP --hmm=ORTHOGROUP.hmm --fasta=Dgeg.okay_contamannotated.aa --nofillcnf=nofill.conf --threads=45 > ORTHOGROUP.hmm2aln.fa```

2.1.4 Concatenate Doliolid sequences that do not contain the "CONTAM" flag to the alignment that came out of hmmbuild

perl -ne 'chomp; if ($flag) { $flag = 0; next; } if (/^>.*\.CONTAM/) { $flag = 1; } else { print "$_\n"; }' OG#.HMM2ALN_OUTPUT > OG#.concat.fa

cat ORTHOGROUP.nogaps.fa >> OG#.concat.fa

2.1.5 If more than one D. gegenbauri sequence is recovered, we need to test whether the recovered sequences are paralogs. To do this we generate a preliminary tree using the LG model and run PhyloTreePruner (next step).  If only a single sequence is recovered, we skip this step and the PhyloTreePruner step. Prior to this step, we remove any D. gegenbauri sequence that was annotated as potential contaminant (in the definition line).

```iqtree-omp -s ORTHOGROUP.hmm2aln.fa -nt AUTO -m LG -pre ORTHOGROUP > iq.out 2> iq.err```

2.1.6 Prune paralogs in PhyloTreePruner (Kocot et al. 2013)

```java PhyloTreePruner ORTHOGROUP.treefile 28 ORTHOGROUP.hmm2aln.fa 0.5 u > ORTHOGROUP.ptp.out 2> ORTHOGROUP.ptp.err```

2.1.4 Concatenate the alignments using fasta2phylomatrix (https://github.com/josephryan/RyanLabPhylogenomicTools)

``fasta2phylomatrix --dir=DIR_OF_FASTAFILES --nexpartition=OUT_PARTITION_FILE --raxpartition=OUT_PARTITION_FILE``` 

2.2.1.5 IQTREE - To generate a phylogenetic hypothesis using maximum likelihood, we will use iqtree. We will investigate model fit using ModelFinder (including c60 site-heterogeneous models) in IQ-Tree using the -madd option. We included 1000 bootstraps.

iqtree -s $FILENAME -nt 250 -bb 1000 -madd GTR+C60+F+R9,LG+C60+F+R9,LG+FO*H4,GTR+FO*H4, -pre $FILENAME.iqtree

3 LITERATURE REFERENCED

Eddy SR. Profile hidden Markov models. Bioinformatics (Oxford, England). 1998 Jan 1;14(9):755-63.

Kocot KM, Citarella MR, Moroz LL, Halanych KM. PhyloTreePruner: a phylogenetic tree-based approach for selection of orthologous sequences for phylogenomics. Evolutionary Bioinformatics. 2013 Jan;9:EBO-S12813.
