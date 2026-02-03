# Gene calling and Annotation

## Using gene calling and annotation software 

### Understand genome assembly quality via gene calling  


NOTE
>This document makes the assumption:
>- That you are working in a WSL terminal [installing WSL](WSL.md)
>- That you have installed conda and the required software [install conda](conda_install.md)

Data for this workshop can be found at [DATA](https://github.com/tmehta12/LIFE748-genomeassembly-annotation/blob/main/data_downloads.md)

ONT Salonella genome at X coverage:

| raw reads      |
| ---------------|
| S_ONT_longx30.fastq |
| S_ONT_shortx30.fastq |
| S_ONT_raw_longx30.fastq |
| S_ONT_longx10.fastq |
| S_ONT_longx100.fastq |

Pacbio Salonella genome at 30X coverage:
| raw reads      |
| ---------------|
| S_hifi_longx30.fastq |

the other data follow this pattern

### AIM 
> Pick a pair of assemblies from above for comparison
> e.g. S_ONT_longx30 vs  S_ONT_shortx30 or S_hifi_longx30 vs  S_ONT_longx30 or etc etc 
> - Use bakta and/or prokka to call genes on two genomes
> - Use artemis to view and review the genecalls


Create environments and install artemis, bakta and prokka

``` 
conda create -n bakta
conda activate bakta 
conda install -c conda-forge -c bioconda bakta
bakta_db download --output ~/tmp_data/ --type light
#conda deactivate
```

```
conda create -n prokka
conda activate prokka 
conda install -c bioconda prokka

#conda deactivate
```

```
conda create -n artemis
conda activate artemis 
conda install -c bioconda -c conda-forge artemis

#conda deactivate
```


run bakta - using the light database and the sample you have selected 
```
conda activate bakta
bakta --db db-light GN3_hifix30_flye_assembly.fasta --output bakta/GN3_hifix30_flye_assembly
conda deactivate
```
run prokka
```
conda activate prokka
prokka -h
#SEE IF YOU CAN WORK IT OUT
```
you can use the command 
```
art GN3_hifix30_flye_assembly.embl + GN3_hifix30_flye_assembly.gbk.txt
```
to view annotations on the same genome assembly 
