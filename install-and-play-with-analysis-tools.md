---
description: >-
  Presentation of the workflow tools, installation by hand of the first one.
  Then, presentation of conda, a tool that help install other tools.
---

# Play with analysis tools

## Presentation of workflow tools

We already show you a global view of the analysis workflow \(in the session "It's not magic"\). We will briefly present the fonctionality of each of those tools.

### Reads quality control with FastQC

We have downloaded 6 reads sequence files. One good practice is to check of the quality of their sequencing. We do that with the FastQC tool that will provide a report \(in html format, we could open the report into a browser\). In this report, there will be a quality histogram according to the position of the bases, the size of the reads, and many other criteria.

The input for FastQC is a reads file \(see chapter "Data presentation" for more information\):

```text
@SRR3099585.1 HWI-ST365:427:H8K2HADXX:1:1101:1497:2215/1
CTCCCTTGACATCTCGATGTCCTTGGTGAGCTCGTCAACCGCGTCCGCACGATACATTTGGTATGTCTTGAATACCTTCGGGAATCGTTGACGATCTGGA
+
@@@DADDD8:CFH9E3:?AC?BF>AC<+C+<E?:8C?FEE0?D0@:'--;A?B;>>3(6;@B>5;5B@@A@:3>A#########################
@SRR3099585.2 HWI-ST365:427:H8K2HADXX:1:1101:1653:2087/1
GTCGACGAATATAAAGTTATTGGGAGAGACGCTGAAGGTCGCGTTGGAGATGGACTCAATTGCGCTTCGCGTTCGCCTCGTGGGTGTTCGCCCGGCTCAC
+
@CBFFFFFHHGHHJJJHHJJJJJJJJJIJJJJJJIJJJGHJJJJJJHHHHHFFFFFEEEEEEDDDDDDDDDDDDDDDDDDDDDDBDDDDDDDDDDDDDDD
```

and the output will be the html report. Below is an example of a quality histogram, with the basic positions of the reads in abscissa and the sequencing quality values ordered. For each position, a moustache box shows the quality values found in the read file, the highest are the best. 

![Example of a quality histogram provided by FastQC](.gitbook/assets/image%20%28204%29.png)

We can divide in 3 parts the quality histogram below: from base 1 to 20, the quality is good; from base 21 to 26, the quality is medium; and for the last bases, the quality is poor.

What if the quality is not good? Most often, it is necessary to act to modify the reads. Some tools help, for example:

* delete the last 20 databases \(trimmer\)
* remove the adapters \(cutadapt\)

As it is better to manage a manual inspection, we will not provide an automated decision making for this step. 

Here are an example for one of the data files we donwloanded:

![Quality histogram provided by FastQC of one of our file](.gitbook/assets/image%20%2811%29.png)

The quality is very good, so we will apply no sequence correction and we will directly use them.

As we have 6 inputs files, we need to run 6 times this FastQC step.

### Reads mapping on the genome with Bowtie2

We want to associate each read on the genome place it come. We use a mapper tools, bowtie2. Bowtie2 take as input the reads file, the reference genome sequence and give a file containing the maping result, one line for each mapping hit: 

![](.gitbook/assets/image%20%2855%29.png)

The result file is in format `.sam` \(**S**equence **A**lignment **M**ap\) which is a text format with speficied columns. The complete specification of the sam format could be see here \([https://samtools.github.io/hts-specs/SAMv1.pdf](https://samtools.github.io/hts-specs/SAMv1.pdf)\) and here are some columns with our file:

* Read name \(column 1: SRR3099585.18\)
* Position of the read on the genome \(column 4: 518181\)
* CIGAR \(Concise Idiosyncratic Gapped Alignment Report\) \(column 6: 100M\)
* ...

As we have 6 inputs files, we need to run 6 times this bowtie2 step.

#### Explanation of the CIGAR encoding

The CIGAR value is a string that synthetically indicates the alignment of the read with the reference genome. Letters have the following meanings:

| M | Match/Mismatch | Exact match or mismatch of x positions |
| :--- | :--- | :--- |
| N | Alignment gap | Next x positions on ref don’t match |
| D | Deletion | Next x positions on ref don’t match |
| I | Insertion | Next x positions on query don’t match |

Here are 2 CIGAR examples associated to their meanings in terms of alignment of a query sequence onto a reference sequence:

![CIGAR examples](.gitbook/assets/image%20%2867%29.png)

### Manage the alignment files with samtools

At this stage, we have sam files. These are large files and it is possible and desirable to reduce them. We do that in 2 ways: 

1. selecting only reads we want: reads that do not align and reads that align in several places due to repeated regions will not be kept
2. compressing the file: convert the `.sam` format onto the `.bam` \(**b**inary **a**lign **m**ap\) format

A bam file is no longer readable by humans but is lighter than its corresponding sam file:

![Reduction of a sam file by converting it into bam format](.gitbook/assets/image%20%28110%29.png)

By adding an indexing step of the bam file, it allows us to operate the computer with tasks for which it has been optimized. This indexing step provides a `.bai` file that constitutes the index. 

![](.gitbook/assets/image%20%28187%29.png)

Reducing sam files and using samtools with bam and bai files will gain us times due to the lightness of the resulting files.

One time more: as we have 6 inputs files, we need to run 6 times this samtools step.

### Visualization

Many tools offer to visualize the reads mapped to the genome. We will use IGV \(**I**ntegrative **G**enome **V**iewer\). This tool need 3 kinds of input: the sequence of the reference genome \(file in`fasta` format\), the corresponding annotations associated to this sequence \(file in `gff` format\), and one or more reads mapping files \(file in `bam` format\).

![](.gitbook/assets/image%20%28141%29.png)

#### Genomic Feature annotation File

A gff formated file is composed of 9 columns, indicating the genomic positions at the beginning and end of genomic elements of interest.

![Meanings of the gff file columns ](.gitbook/assets/image%20%2857%29.png)

![Content of the gff file for the O.tauri species](.gitbook/assets/image%20%2821%29.png)

The next screenshot, is an IGV visualisation centered on one gene, the gene ostta18g0198 \(gene number 198 on the 18th chromosome of the _Ostreococcus tauri_ genome\). Two mapping files were imported, one for each condition \(uper: standard condition, lower: iron depletion condition\).

![IGV visualisation of the ostta18g0198 gene.](.gitbook/assets/image%20%28197%29.png)

Th_e ostta18g0198_ gene is differentialy expressed between the 2 conditions: it is more expressed in case of iron depletion.

### Reads count by gene with HTseq-count

The transcriptional activity of genes is assumed to be proportional to the number of mapped reads observed. So the next step of the analysis is to count the number of reads per gene. We use HTseq-count tool for that. With a genome annotation file \(gff format\) and the reads alignment file \(bam format\), HTseq-count tool provides a table with 2 columns, gene name and number of reads mapped on it.

![](.gitbook/assets/image%20%28130%29.png)

Once more: as we have 6 inputs files, we need to run 6 times this HTseq-count step.

### Differential analysis with DESeq2

Now we have the data to reply the scientific question: which genes are expressed differently between the 2 conditions of the study? The differential analysis of gene expression is based on a statistical model. We chose DESeq2, a tool designed for this task. Working with all the count tables, DESeq2 will provide a logFC and an adjusted P-value for each gene that allow us to select genes to focus on.

![DESeq2 inputs](.gitbook/assets/image%20%28123%29.png)

DESeq2 provides numerous graphs that illustrate each statistical decision it makes based on default threshold values. It is a good practice to carefully look at the results to eventually change one or more default parameters and re-run this step.

![Example of 2 DESeq2 result graphs: Mean-Average plot \(left\) and Volcano plot \(right\)](.gitbook/assets/image%20%28111%29.png)

The more there are replicas in each condition, the more the statistical test is powerful.

This step, as it associates the 6 count tables is done one time.

### Workflow review

![](.gitbook/assets/image%20%2897%29.png)

## Installing FastQC by hand 

Now we want to realise our workflow. The first thing is to install all the tools it rely on. We begin by the first of them, FastQC.

We download it by a browser from this link: [https://www.bioinformatics.babraham.ac.uk/projects/download.html](https://www.bioinformatics.babraham.ac.uk/projects/download.html), where we choose the FastQC\_V0.11.8 link according to our operating system.

![The FastQC web page](.gitbook/assets/image%20%28143%29.png)

The file we downloaded is compressed, so we have to decompress it and we can read the documentation. To install the tool, we open a terminal and move \(with the `cd` command\) to the folder where FastQC was decompressed. If we launch it \(`./fastqc`\), we probably obtain:

```text
tdenecker@DESKTOP-B8ON598:fastqc_v0.11.8/FastQC$ ./fastqc
Can't exec "java": No such file or directory at ./fastqc line 283.
```

It doesn't work because FastQC need another tool, Java. So we look for Java and discover that we have to install it too.

![](https://s3.amazonaws.com/media-p.slid.es/uploads/991142/images/5773548/pasted-from-clipboard.png)

Before installing java, let's take a step back. These are the problems that appear:

* Installation of java \(not always easy, especially for linux\)
* FastQC usage
* In terms of reproducibility,  how will we manage FastQC update?

Would there be a simpler way to manage those tool installation?

Yes it is! We will use the conda solution.

## Presentation of conda 

![](.gitbook/assets/image%20%28199%29.png)

Conda is an open source package and environment manager. Conda may:

* Installing the packages
* Executing packages
* Updating packages

It is multi-platform: _ie_. for Windows, macOS and Linux and above all it has no compilation problems, dependencies,..., Conda takes care of everything!

Conda works with channels through which packages are accessed. For bioinformatics tools, there is a dedicated channel called "Bioconda".

![](.gitbook/assets/image%20%28198%29.png)

Bioconda is a software distribution channel, usable by the conda package manager and offering many software used in bioinformatics.

All the linux tools used in this project are on bioconda.

### Tool installation with Conda

How does the installation of FastQC with conda work? 

We need to install conda first. We use a lightweight version of conda, "miniconda".

We follow those steps: 

```text
# Download of miniconda
wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

# Installation of miniconda
bash Miniconda3-latest-Linux-x86_64.sh -b

# Suppression of the downloaded file
rm Miniconda3-latest-Linux-x86_64.sh

# Add the conda path in the $path to use the conda command directly
export PATH=~/miniconda3/bin:$PATH

# Update Conda
conda update conda
conda update --all

# Add the channels
conda config --add channels conda-forge
conda config --add channels bioconda
```

Now, to install FastQC, we have simply to do:

```text
conda install -y fastqc 
```

The installation of the Java dependancy was done by conda!

But you may argue that the other method is simpler \(the conda method is quite complex\) and need less command lines. You would be right if there was only one tool, but there are several others to install. Here are the installation of the other tools of our analysis workflow:

```text
conda install -y bowtie2 htseq samtools
```

and that's all!

## It's up to you! 

{% hint style="success" %}
Improve the installation script and add the installation with Conda of all the other tools \(but DEseq2\) of the analysis workflow
{% endhint %}

We resume here the complete tools installation script:

```text
# Installation - conda by the way of miniconda
wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b
rm Miniconda3-latest-Linux-x86_64.sh

## Add in path
export PATH=~/miniconda3/bin:$PATH

## Update
conda update conda
conda update --all

## Add chanel
conda config --add channels conda-forge
conda config --add channels bioconda

## Install fastqc, bowtie2, htseq and samtools
conda install -y fastqc bowtie2 htseq samtools
```

