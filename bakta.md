# LIFE748 Prokaryotic Gene Calling and Annotation Workshop

## Using gene calling and annotation software 

### Understand genome assembly quality via gene calling  


NOTE
>This document makes the assumption:
>- That you are working in a WSL terminal [installing WSL](WSL.md)
>- That you have installed conda and the required software [install conda](conda_install.md)

Data for this workshop can be found at [DATA](https://github.com/tmehta12/LIFE748-genomeassembly-annotation/blob/main/data_downloads.md)

ONT Salmonella genome at X coverage:

| raw reads      |
| ---------------|
| S_ONT_longx30.fastq |
| S_ONT_shortx30.fastq |
| S_ONT_raw_longx30.fastq |
| S_ONT_longx10.fastq |
| S_ONT_longx100.fastq |

Pacbio Salmonella genome at 30X coverage:
| raw reads      |
| ---------------|
| S_hifi_longx30.fastq |

the other data follow this pattern

## AIM 
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
#SEE IF YOU CAN WORK IT OUT! e.g., prokka [options] <contigs.fasta>
```
Then, to view annotations on the same genome assembly you can use the command 
```
art GN3_hifix30_flye_assembly.embl + GN3_hifix30_flye_assembly.gbk.txt
```

## Benchmarking 
> You can treat the Prokka directory as a mini “results package”: the .txt, .tsv, .gff, .gbk, .log, and .err files together give you everything they need to evaluate annotation content and performance and then visualise it across genomes

> Below, its assumed that you have multiple genomes (one Prokka folder per genome) and you'll use Bash+R or Python; most examples are in Bash+R though!

---

### 1. Which files to use and what they contain

You should first understand the role of each output file.

- `PROKKA_*.txt`: Plain-text **summary** of the annotation (counts of CDS, rRNA, tRNA, ncRNA, CRISPRs, etc.).
- `PROKKA_*.tsv`: One line per feature (e.g. `locus_tag, ftype, len_bp, gene, EC_number, COG, product`); good for counting features and assessing functional coverage.
- `PROKKA_*.gff`: Master GFF3 including sequences and annotations; can be used to re-derive counts and cross-check.
- `PROKKA_*.gbk` / `PROKKA_*.gbf-r`: GenBank format, useful for inspection in tools such as Artemis or SnapGene.
- `PROKKA_*.faa`: Amino acid FASTA of predicted proteins (CDS only).
- `PROKKA_*.ffn`: Nucleotide FASTA of all predicted transcripts (CDS, rRNA, tRNA, etc.).
- `PROKKA_*.fna` / `PROKKA_*.fsa`: Input contig sequences (the assembly).
- `PROKKA_*.tbl`: Feature table for NCBI submission; parseable but mostly redundant for this workshop.
- `PROKKA_*.sqn`: ASN.1 submission file for NCBI; mainly for deposition, not analysis.
- `PROKKA_*.log`: Run log, including timestamps, counts, databases used, and parameters; useful for runtime estimation and troubleshooting.
- `PROKKA_*.err`: NCBI discrepancy report listing unacceptable or problematic annotations.

For the learning objectives, the “core trio” are `.txt` (summary counts), `.tsv` (per-feature annotation / functional coverage), and `.log` (runtime and methods).

---

### 2. Evaluating annotation content and performance

You can construct a small summary table (one row per genome) with:

- Assembly metrics (from upstream assembly, or derived from `*.fna`).
- Total gene/CDS counts.
- rRNA and tRNA counts.
- Functional annotation coverage (fraction of CDS with a non-hypothetical product).
- Runtime and any warnings.

#### 2.1. Gene, CDS, rRNA, tRNA counts

**Approach A ( using `.txt`):**

The `.txt` file includes lines such as “CDS: 2049”, “tRNA: 57”, etc. (exact labels may vary slightly by version).

- Inspect a file manually:

  ```bash
  less PROKKA_02032026.txt
  ```

#### Extract key lines

```bash
grep -E 'CDS:|rRNA:|tRNA:' PROKKA_02032026.txt
```

You can then parse these values in **R** (e.g. using `readLines()` + regex) into columns:

- `n_CDS`
- `n_rRNA`
- `n_tRNA`

---

**Approach B (more explicit, using .tsv)**

Because `.tsv` has an `ftype` column, you can count feature types directly.

**Quick overview in Bash:**

```bash
cut -f2 PROKKA_02032026.tsv | sort | uniq -c
```

**In R (Rstudio):**

```r
library(readr)
library(dplyr)

tsv <- read_tsv("PROKKA_02032026.tsv", show_col_types = FALSE)

feature_counts <- tsv |>
  count(ftype, name = "n")
```

**Feature mapping:**

- `CDS` → Predicted protein-coding genes  
- `rRNA` → Ribosomal RNAs  
- `tRNA` → Transfer RNAs  

Other `ftype` values (e.g. `tmRNA`, `misc_RNA`, `repeat_region`) can optionally be tracked.

---

#### 2.2 Functional annotation coverage

Using `PROKKA_*.tsv`, you can quantify what proportion of CDS have a functional annotation (non-hypothetical product).

Typical columns include:

- `ftype`
- `gene`
- `product`
- `EC_number`
- `COG`

**Example for a single genome:**

```r
library(readr)
library(dplyr)

tsv <- read_tsv("PROKKA_02032026.tsv", show_col_types = FALSE)

cds <- tsv |>
  filter(ftype == "CDS")

cds_summary <- cds |>
  mutate(has_function = !is.na(product) & product != "hypothetical protein") |>
  summarise(
    total_cds      = n(),
    cds_with_func  = sum(has_function),
    frac_function  = cds_with_func / total_cds
  )
```

**Extensions:**

- Use presence of `EC_number` as a stricter indicator of enzymatic annotation.  
- Use `COG` for orthology-based functional assignment.

---

#### 2.3 Runtime efficiency

Runtime per genome can be estimated from `.log` timestamps.

**Inspect the log:**

```bash
head PROKKA_02032026.log
tail PROKKA_02032026.log
```

Identify start and end timestamps (usually the first and last lines).  
You can calculate elapsed time manually or standardise it by running Prokka with `/usr/bin/time -v` when generating data.

If logs contain consistent timing lines, you can parse elapsed time into a summary table and derive “genes per minute”.

**Example pseudocode in R (Rstudio):**

```r
# Pseudocode for parsing last line if it contains 'real X.Y sec'
log_lines <- readLines("PROKKA_02032026.log")
last_line <- log_lines[length(log_lines)]
# extract numeric time from last_line using a regex in real code
```

**Metrics:**

- `runtime_min`
- `cds_per_min = n_CDS / runtime_min`

This allows you to analyse runtime efficiency across datasets.

---

### 3. Assembly summary metrics from Prokka outputs

If standard assembly reports (e.g. QUAST) are not provided, basic assembly metrics can be computed from `PROKKA_*.fna` or `.fsa`.

**Key metrics:**

- Number of contigs = number of FASTA headers (`>`)
- Total assembled length = sum of sequence lengths
- GC content = proportion of G and C bases

**Example in R (using Biostrings):**

```r
library(Biostrings)

fa <- readDNAStringSet("PROKKA_02032026.fna")

contig_lengths <- width(fa)
total_bp <- sum(contig_lengths)
n_contigs <- length(fa)

gc_counts <- letterFrequency(fa, c("G", "C"))
gc_fraction <- sum(gc_counts) / (2 * total_bp)
```

you can store `total_bp`, `n_contigs`, and `gc_fraction` in the same summary table as the annotation statistics.

To link assembly and annotation:

- Compare `total_bp` vs `n_CDS` (larger genomes may have more genes)
- Explore whether more fragmented assemblies (`n_contigs` high) have lower `frac_function`

---

### 4. Creating comparison figures (R examples)

Assume you build a `annotation_summary.csv` file with one row per genome:

| sample | total_bp | n_contigs | gc_fraction | n_CDS | n_rRNA | n_tRNA | frac_function | runtime_min |
|:--------|----------:|-----------:|--------------:|-------:|--------:|--------:|----------------:|--------------:|
| GenomeA | ... | ... | ... | ... | ... | ... | ... | ... |

---

#### 4.1 Bar plots of gene and RNA counts

**Reshape counts into long format:**

```r
library(readr)
library(dplyr)
library(tidyr)
library(ggplot2)

ann <- read_csv("annotation_summary.csv")

gene_long <- ann |>
  select(sample, n_CDS, n_rRNA, n_tRNA) |>
  pivot_longer(-sample, names_to = "feature", values_to = "count")
```

**Plot:**

```r
ggplot(gene_long, aes(x = sample, y = count, fill = feature)) +
  geom_col(position = "dodge") +
  coord_flip() +
  labs(
    x = "Genome",
    y = "Feature count",
    fill = "Feature type"
  )
```

**Suggestions:**

- Order genomes by `n_CDS` (e.g. using `fct_reorder`)  
- Use `coord_flip()` for readability with many genomes  

---

#### 4.2 Functional annotation coverage figure

A straightforward figure is a bar plot of `frac_function` per genome:

```r
ggplot(ann, aes(x = sample, y = frac_function)) +
  geom_col(fill = "steelblue") +
  coord_flip() +
  scale_y_continuous(labels = scales::percent_format()) +
  labs(
    x = "Genome",
    y = "CDS with non-hypothetical function"
  )
```

**Extensions:**

- Add dashed reference lines (e.g. overall mean)  
- Colour bars by species or assembly method, if available  

---

#### 4.3 Assembly vs annotation plots

**Example 1: Assembly size vs gene count**

```r
ggplot(ann, aes(x = total_bp / 1e6, y = n_CDS)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE) +
  labs(
    x = "Assembly size (Mb)",
    y = "Number of CDS"
  )
```

**Example 2: Contiguity vs functional coverage**

```r
ggplot(ann, aes(x = n_contigs, y = frac_function)) +
  geom_point() +
  labs(
    x = "Number of contigs",
    y = "Functional annotation fraction"
  )
```

**Optional discussion prompts:**

- Do more fragmented assemblies show reduced functional coverage?  
- Identify outlier genomes (e.g. unusually low `n_tRNA` or `frac_function` for their genome size).  
- Suggest possible explanations such as assembly quality, contamination, or strain differences.
