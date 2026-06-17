# LIFE748 Prokaryotic Genome Assembly workshop

NOTE
>This document makes the assumption:
>- That you are working in a WSL terminal or HPC environment [installing WSL or loading HPC](WSL.md)
>- That you have installed conda and the [required software](conda_install.md) if working on WSL **OR** you have loaded the conda environment on the HPC [see Option 2](WSL.md)
>- That you have run the command `conda activate genomics_env`

### Assembly datasets and anaylsis provided:
[DATA](https://github.com/tmehta12/LIFE748-genomeassembly-annotation/blob/main/data_downloads.md)

These are found in the `cgr.liv.ac.uk/454/acdarby/LIFE748/raw/` folder

Pacbio E. coli three genomes at 30x coverage:

| raw reads      | fastqc | flye | quast |
| ---------------| ------ |------|-------|
| GN3_hifix30.fastq |        |      |        |
| GN6_hifix30.fastq |        |      |        |
| GN9_hifix30.fastq |        |      |        |


### We can use `fastqc` to check the error profiles of the reads
```
fastqc --help
```
Can be used to remind you on the parameters we can provide/change:
```

            FastQC - A high throughput sequence QC analysis tool

SYNOPSIS

	fastqc seqfile1 seqfile2 .. seqfileN

    fastqc [-o output dir] [--(no)extract] [-f fastq|bam|sam]
           [-c contaminant file] seqfile1 .. seqfileN

DESCRIPTION

    FastQC reads a set of sequence files and produces from each one a quality
    control report consisting of a number of different modules, each one of
    which will help to identify a different potential type of problem in your
    data.

    If no files to process are specified on the command line then the program
    will start as an interactive graphical application.  If files are provided
    on the command line then the program will run with no user interaction
    required.  In this mode it is suitable for inclusion into a standardised
    analysis pipeline.

    The options for the program as as follows:

    -h --help       Print this help file and exit

    -v --version    Print the version of the program and exit

    -o --outdir     Create all output files in the specified output directory.
                    Please note that this directory must exist as the program
                    will not create it.  If this option is not set then the
                    output file for each sequence file is created in the same
                    directory as the sequence file which was processed.

    --casava        Files come from raw casava output. Files in the same sample
                    group (differing only by the group number) will be analysed
                    as a set rather than individually. Sequences with the filter
                    flag set in the header will be excluded from the analysis.
                    Files must have the same names given to them by casava
                    (including being gzipped and ending with .gz) otherwise they
                    won't be grouped together correctly.

    --nano          Files come from nanopore sequences and are in fast5 format. In
                    this mode you can pass in directories to process and the program
                    will take in all fast5 files within those directories and produce
                    a single output file from the sequences found in all files.

    --nofilter      If running with --casava then don't remove read flagged by
                    casava as poor quality when performing the QC analysis.

    --extract       If set then the zipped output file will be uncompressed in
                    the same directory after it has been created. If --delete is
                    also specified then the zip file will be removed after the
                    contents are unzipped.

    -j --java       Provides the full path to the java binary you want to use to
                    launch fastqc. If not supplied then java is assumed to be in
                    your path.

    --noextract     Do not uncompress the output file after creating it.  You
                    should set this option if you do not wish to uncompress
                    the output when running in non-interactive mode.

    --nogroup       Disable grouping of bases for reads >50bp. All reports will
                    show data for every base in the read.  WARNING: Using this
                    option will cause fastqc to crash and burn if you use it on
                    really long reads, and your plots may end up a ridiculous size.
                    You have been warned!

    --min_length    Sets an artificial lower limit on the length of the sequence
                    to be shown in the report.  As long as you set this to a value
                    greater or equal to your longest read length then this will be
                    the sequence length used to create your read groups.  This can
                    be useful for making directly comaparable statistics from
                    datasets with somewhat variable read lengths.

    --dup_length    Sets a length to which the sequences will be truncated when
                    defining them to be duplicates, affecting the duplication and
                    overrepresented sequences plot.  This can be useful if you have
                    long reads with higher levels of miscalls, or contamination with
                    adapter dimers containing UMI sequences.


    -f --format     Bypasses the normal sequence file format detection and
                    forces the program to use the specified format.  Valid
                    formats are bam,sam,bam_mapped,sam_mapped and fastq


    --memory        Sets the base amount of memory, in Megabytes, used to process
                    each file.  Defaults to 512MB.  You may need to increase this if
                    you have a file with very long sequences in it.

    --svg           Save the graphs in the report in SVG format.

    -t --threads    Specifies the number of files which can be processed
                    simultaneously.  Each thread will be allocated 250MB of
                    memory so you shouldn't run more threads than your
                    available memory will cope with, and not more than
                    6 threads on a 32 bit machine

    -c              Specifies a non-default file which contains the list of
    --contaminants  contaminants to screen overrepresented sequences against.
                    The file must contain sets of named contaminants in the
                    form name[tab]sequence.  Lines prefixed with a hash will
                    be ignored.

    -a              Specifies a non-default file which contains the list of
    --adapters      adapter sequences which will be explicity searched against
                    the library. The file must contain sets of named adapters
                    in the form name[tab]sequence.  Lines prefixed with a hash
                    will be ignored.

    -l              Specifies a non-default file which contains a set of criteria
    --limits        which will be used to determine the warn/error limits for the
                    various modules.  This file can also be used to selectively
                    remove some modules from the output all together.  The format
                    needs to mirror the default limits.txt file found in the
                    Configuration folder.

   -k --kmers       Specifies the length of Kmer to look for in the Kmer content
                    module. Specified Kmer length must be between 2 and 10. Default
                    length is 7 if not specified.

   -q --quiet       Suppress all progress messages on stdout and only report errors.

   -d --dir         Selects a directory to be used for temporary files written when
                    generating report images. Defaults to system temp directory if
                    not specified.

BUGS

    Any bugs in fastqc should be reported either to simon.andrews@babraham.ac.uk
    or in www.bioinformatics.babraham.ac.uk/bugzilla/
```

We can view the report by navigating to the unix home using filemanager "clicking on the penguin"

![Model](https://github.com/acdarby/LIFE748/blob/main/Artboard1.png)

And find the `*.html` file click on this to open the file in a browser.

note 
>Refer to the lecture notes and make some observations about the quantity, quality and length of the reads in the different files.

### We are using the `flye` assembler [Flye github](https://github.com/fenderglass/Flye)
```
flye --help
```
Can be used to remind you on the parameters we can provide/change:
```
flye
usage: flye (--pacbio-raw | --pacbio-corr | --pacbio-hifi | --nano-raw |
	     --nano-corr | --nano-hq ) file1 [file_2 ...]
	     --out-dir PATH

	     [--genome-size SIZE] [--threads int] [--iterations int]
	     [--meta] [--polish-target] [--min-overlap SIZE]
	     [--keep-haplotypes] [--debug] [--version] [--help]
	     [--scaffold] [--resume] [--resume-from] [--stop-after]
	     [--read-error float] [--extra-params]
	     [--deterministic]
flye: error: the following arguments are required: -o/--out-dir
(genomics_env) acdarby@gauss08:~/tmp_data$ flye --help
usage: flye (--pacbio-raw | --pacbio-corr | --pacbio-hifi | --nano-raw |
	     --nano-corr | --nano-hq ) file1 [file_2 ...]
	     --out-dir PATH

	     [--genome-size SIZE] [--threads int] [--iterations int]
	     [--meta] [--polish-target] [--min-overlap SIZE]
	     [--keep-haplotypes] [--debug] [--version] [--help]
	     [--scaffold] [--resume] [--resume-from] [--stop-after]
	     [--read-error float] [--extra-params]
	     [--deterministic]

Assembly of long reads with repeat graphs

options:
  -h, --help            show this help message and exit
  --pacbio-raw path [path ...]
                        PacBio regular CLR reads (<20% error)
  --pacbio-corr path [path ...]
                        PacBio reads that were corrected with other methods (<3% error)
  --pacbio-hifi path [path ...]
                        PacBio HiFi reads (<1% error)
  --nano-raw path [path ...]
                        ONT regular reads, pre-Guppy5 (<20% error)
  --nano-corr path [path ...]
                        ONT reads that were corrected with other methods (<3% error)
  --nano-hq path [path ...]
                        ONT high-quality reads: Guppy5+ SUP or Q20 (<5% error)
  --subassemblies path [path ...]
                        [deprecated] high-quality contigs input
  -g size, --genome-size size
                        estimated genome size (for example, 5m or 2.6g)
  -o path, --out-dir path
                        Output directory
  -t int, --threads int
                        number of parallel threads [1]
  -i int, --iterations int
                        number of polishing iterations [1]
  -m int, --min-overlap int
                        minimum overlap between reads [auto]
  --asm-coverage int    reduced coverage for initial disjointig assembly [not set]
  --hifi-error float    [deprecated] same as --read-error
  --read-error float    adjust parameters for given read error rate (as fraction e.g. 0.03)
  --extra-params extra_params
                        extra configuration parameters list (comma-separated)
  --plasmids            unused (retained for backward compatibility)
  --meta                metagenome / uneven coverage mode
  --keep-haplotypes     do not collapse alternative haplotypes
  --no-alt-contigs      do not output contigs representing alternative haplotypes
  --scaffold            enable scaffolding using graph [disabled by default]
  --trestle             [deprecated] enable Trestle [disabled by default]
  --polish-target path  run polisher on the target sequence
  --resume              resume from the last completed stage
  --resume-from stage_name
                        resume from a custom stage
  --stop-after stage_name
                        stop after the specified stage completed
  --debug               enable debug output
  -v, --version         show program's version number and exit
  --deterministic       perform disjointig assembly single-threaded

Input reads can be in FASTA or FASTQ format, uncompressed
or compressed with gz. Currently, PacBio (CLR, HiFi, corrected)
and ONT reads (regular, HQ, corrected) are supported. Expected error rates are
<15% for PB CLR/regular ONT; <5% for ONT HQ, <3% for corrected, and <1% for HiFi. Note that Flye
was primarily developed to run on uncorrected reads. You may specify multiple
files with reads (separated by spaces). Mixing different read
types is not yet supported. The --meta option enables the mode
for metagenome/uneven coverage assembly.

To reduce memory consumption for large genome assemblies,
you can use a subset of the longest reads for initial disjointig
assembly by specifying --asm-coverage and --genome-size options. Typically,
40x coverage is enough to produce good disjointigs.

You can run Flye polisher as a standalone tool using
--polish-target option.
```

### NOTE: commands are specific for the origin of the reads, whether raw, corrected or hi-fi for PacBio or raw, correct or high-quality for ONT!

This command for PacBio HiFi:
```
flye --pacbio-hifi GN3_hifix30.fastq --out-dir GN3_long
```
provide this output to stdout:
```
[2023-11-22 23:19:40] INFO: Starting Flye 2.9.2-b1786
[2023-11-22 23:19:40] INFO: >>>STAGE: configure
[2023-11-22 23:19:40] INFO: Configuring run
[2023-11-22 23:19:42] INFO: Total read length: 250003313
[2023-11-22 23:19:42] INFO: Reads N50/N90: 16096 / 14580
[2023-11-22 23:19:42] INFO: Minimum overlap set to 10000
[2023-11-22 23:19:42] INFO: >>>STAGE: assembly
[2023-11-22 23:19:42] INFO: Assembling disjointigs
[2023-11-22 23:19:42] INFO: Reading sequences
[2023-11-22 23:19:43] INFO: Building minimizer index
[2023-11-22 23:19:43] INFO: Pre-calculating index storage
0% 10% 20% 30% 40% 50% 60% 70% 80% 90% 100%
[2023-11-22 23:20:00] INFO: Filling index
0% 10% 20% 30% 40% 50% 60% 70% 80% 90% 100%
[2023-11-22 23:21:12] INFO: Extending reads
[2023-11-22 23:22:07] INFO: Overlap-based coverage: 32
[2023-11-22 23:22:07] INFO: Median overlap divergence: 0.00101259
0% 70% 100%
[2023-11-22 23:27:23] INFO: Assembled 4 disjointigs
[2023-11-22 23:27:23] INFO: Generating sequence
0% 10% 20% 30% 40% 50% 60% 70% 80% 90% 100%
[2023-11-22 23:27:26] INFO: Filtering contained disjointigs
0% 20% 50% 70% 100%
[2023-11-22 23:27:28] INFO: Contained seqs: 0
[2023-11-22 23:27:28] INFO: >>>STAGE: consensus
[2023-11-22 23:27:28] INFO: Running Minimap2
[2023-11-22 23:28:34] INFO: Computing consensus
[2023-11-22 23:30:47] INFO: Alignment error rate: 0.005580
[2023-11-22 23:30:47] INFO: >>>STAGE: repeat
[2023-11-22 23:30:47] INFO: Building and resolving repeat graph
[2023-11-22 23:30:47] INFO: Parsing disjointigs
[2023-11-22 23:30:47] INFO: Building repeat graph
0% 20% 50% 70% 100%
[2023-11-22 23:30:49] INFO: Median overlap divergence: 0.00633243
[2023-11-22 23:30:49] INFO: Parsing reads
[2023-11-22 23:30:50] INFO: Aligning reads to the graph
0% 10% 20% 30% 40% 50% 60% 70% 80% 90% 100%
[2023-11-22 23:32:24] INFO: Aligned read sequence: 248393755 / 250003313 (0.993562)
[2023-11-22 23:32:24] INFO: Median overlap divergence: 0.000222385
[2023-11-22 23:32:24] INFO: Mean edge coverage: 47
[2023-11-22 23:32:24] INFO: Simplifying the graph
[2023-11-22 23:32:24] INFO: >>>STAGE: contigger
[2023-11-22 23:32:24] INFO: Generating contigs
[2023-11-22 23:32:24] INFO: Reading sequences
[2023-11-22 23:32:25] INFO: Generated 6 contigs
[2023-11-22 23:32:25] INFO: Added 0 scaffold connections
[2023-11-22 23:32:25] INFO: >>>STAGE: polishing
[2023-11-22 23:32:25] INFO: Polishing genome (1/1)
[2023-11-22 23:32:25] INFO: Running minimap2
[2023-11-22 23:33:28] INFO: Separating alignment into bubbles
[2023-11-22 23:36:27] INFO: Alignment error rate: 0.003609
[2023-11-22 23:36:27] INFO: Correcting bubbles
0% 10% 20% 30% 40% 50% 60% 70% 80% 90% 100%
[2023-11-22 23:39:29] INFO: >>>STAGE: finalize
[2023-11-22 23:39:29] INFO: Assembly statistics:

	Total length:	5243798
	Fragments:	5
	Fragments N50:	4947119
	Largest frg:	4947119
	Scaffolds:	0
	Mean coverage:	46

[2023-11-22 23:39:29] INFO: Final assembly: /tmp_data/GN3_long/assembly.fasta
```
Here's a ready-to-use **QUAST protocol** for multiple assemblies, formatted for GitHub:

---

## Running QUAST on Multiple Assemblies

Instructions to run QUAST on multiple genome assemblies in a single command.

### Steps

1. **Prepare Your Assembly Files**:  
   Place all assembly files (e.g., FASTA files) into a single directory.

2. **Run QUAST on Multiple Assemblies**:
   You can specify all assembly files individually or use the directory path containing all assemblies.

   - **Directly Specifying Files**:
     ```bash
     quast.py assembly1.fasta assembly2.fasta assembly3.fasta -o quast_output
     ```

   - **Using a Directory**:
     ```bash
     quast.py /path/to/assemblies/* -o quast_output
     ```

3. **Optional: Use a Reference Genome**  
   If you have a reference genome, include it to get alignment-based metrics.

   NOTE: the E. coli K12 reference genome is provided in the downloaded data: `~/cgr.liv.ac.uk/454/acdarby/LIFE748/EcoliK12/EcoliK12_GCA_000005845.2_ASM584v2_genomic.fna`
   ```bash
   quast.py /path/to/assemblies/* -r reference_genome.fasta -o quast_output
   ```

5. **Additional Options**:  
   QUAST offers various parameters for customization:
   - `--gene-finding` to predict genes
   - `--threads <num>` to specify thread count
   - `--min-contig <size>` to set a minimum contig length (default is 500 bp)

6. **Example Command with Options**:
   ```bash
   quast.py /path/to/assemblies/* -r reference_genome.fasta --gene-finding --threads 4 -o quast_output
   ```

### Results
- All results will be saved in the `quast_output` directory, including summary reports, assembly metrics, and additional analyses.
- Have a look at the `report.html` file and compare the assemblies e.g., total assembly size, number of contigs, N50, largest contig length, assembly completeness
- If these were reference assemblies, which would you pick for downstream analysis? why? 

---

## Additional Work - do this after 'Gene calling and Annotation' !

Additonal reads from other experiments (on Salmonella) are found in the `cgr.liv.ac.uk/454/acdarby/LIFE748/raw/` folder too

Run `fastqc`, `Flye`, and `QUAST` for the following and compare outputs:

Remember make a note if the reads are `ONT` or `PacBio Hi-Fi` as the Flye command is important:

For example, this would be the command for ONT raw (uncorrected) reads:
```
flye --nano-raw S_ONT_shortx30.fastq --out-dir S_ONT_raw_short
```

ONT Salmonella genome at 30X coverage:

| raw reads      | fastqc | flye | quast |
| ---------------| ------ |------|-------|
| S_longx30.fastq |        |      |        |
| S_shortx30.fastq |        |      |        |
| S_raw_longx30.fastq |        |      |        |
| S_longx10.fastq |        |      |        |
| S_longx100.fastq |        |      |        |

And this would be the command for PacBio reads (like what you ran for the E. coli samples):
```
flye --pacbio-hifi S_hifi_longx30.fastq --out-dir S_hifi_long
```


Pacbio Salmonella genome at 30X coverage:
| raw reads      | fastqc | flye | quast |
| ---------------| ------ |------|-------|
| S_hifi_longx30.fastq |        |      |        |

### Assess the results 
- All results will be saved in the `quast_output` directory, including summary reports, assembly metrics, and additional analyses.
- Have a look at the `report.html` file and compare the assemblies e.g., total assembly size, number of contigs, N50, largest contig length, assembly completeness
- If these were reference assemblies, which would you pick for downstream analysis? why? 

