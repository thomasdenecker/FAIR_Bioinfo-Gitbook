---
description: >-
  We present the project, its resulting application, the data, and the analysis
  workflow. We open a terminal, write a script initiating the project, and a
  first improvement by adding the data recovery.
---

# It's not magic

{% hint style="info" %}
Information for trainers: 

Depending on the ease of your audience to practice unix commands, you may or may not be able to go quickly to the different unix concepts and commands \(_eg._ the part concerning how to open a terminal\) presented in this chapter.
{% endhint %}

## The global project and the final application

Our objective is to propose you a range of functionalities to make a complete bioinformatics analysis reproducible. To be more concrete, we have chosen a classic example of bioinformatics analysis which is the identification of differentially expressed genes from RNA-seq data. We will present the minimum concepts and tools necessary to carry out this type of analysis without going into details. Our training objective is that you can replace this example with one of your own bioinformatics analysis rather than becoming an expert in RNAseq data analysis \(there are many other training courses for this\). This example is just here to illustrate in a concrete case the implementation of a reproducible analytical solution. 

For the example chosen, the complete analysis is divided into 2 main parts: from the sequence data the bioinformatics analyses will provide computed data, that will then be proposed for interpretation by the biologist. We will call "workflow" the automated processing chain and "application" the visualization of the computed data in a browser window through which the biologist can interact with them.

### Scientific question

The objective is to carry out a differential gene expression analysis using RNAseq data. In case of RNAseq analysis, the scientific question that the analysis seeks to answer is: which genes are altered in expression between two conditions? And one possible answer is to compare for each gene the amount of transcripts associated with it, considering that the sequences contained in the RNAseq data reflect the quantities of transcripts. Genes that will have a variation in quantity will be considered "differentially expressed" between the two conditions. To establish this answer we will use a statistical model that has been developed for this question.

#### Origin of the data 

We choose to use data available in the literature from the following paper: [_Ostreococcus tauri_ is a new model green alga for studying iron metabolism in eukaryotic phytoplankton, Lelandais _et al_, BMC Genomics, 2016](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-016-2666-6) \([https://doi.org/10.1186/s12864-016-2666-6](https://doi.org/10.1186/s12864-016-2666-6)\). The organism studied is _O. tauri_  and the sequence data are data from RNAseq single end. And the question here is: which genes are altered in expression \(~ number of transcripts\) in an iron-poor environment?

#### Experimental design

16 different experimental conditions were performed and 3 biological replicas were made for each condition. In this course, we will only use the data of the difference +Fe / -Fe at 9 hours in light condition 2.

![Experimental design](.gitbook/assets/image%20%28152%29.png)

{% hint style="info" %}
Information for trainers: 

During our initial formation, we launched the application in a browser to make the audience want to get involved in the training. 

In the web application, we can focus on the best candidate revealed by the analysis and it is the gene detailed in the article.
{% endhint %}

## The input data

47 experience files are available. You can recover all this data from :

* SRA \(Sequence Read Archive\), maintained by the NCBI : [here](https://www.ncbi.nlm.nih.gov/sra?linkname=bioproject_sra_all&from_uid=304086)
* ENA \(European Nucleotide Archive\), maintained by the EMBL-EBI : [here](https://www.ebi.ac.uk/ena/data/view/PRJNA304086)

![Screenshot from SRA \(NCBI\)](.gitbook/assets/image%20%2891%29.png)

![screenshot from ENA \(EMBL-EBI\)](.gitbook/assets/image%20%28175%29.png)

The 6 results files we exploit for this courses corresponding to the +Fe / -Fe at 9 hours in light condition 2 are: SRR3105697 to SRR3105699 and SRR3099585 to SRR3099587.

### Data type

Data stand in "fastq" formated file where one sequence is described by 4 lines: 

* sequence identifier 
* sequence bases
* "+" 
* base calling score, one letter code for each base. The quality score is between 0 \(bad\) to 41 \(good\) and is encoded by a letter \(the choice of the letter depends on the sequencing machine and the base calling program used, see the [Fastq format](https://en.wikipedia.org/wiki/FASTQ_format) for more explanation\).

Example: the 2 first sequences of the SRR3099585 file:

```text
@SRR3099585.1 HWI-ST365:427:H8K2HADXX:1:1101:1497:2215/1 
CTCCCTTGACATCTCGATGTCCTTGGTGAGCTCGTCAACCGCGTCCGCACGATACATTTGGTATGTCTTGAATACCTTCGGGAATCGTTGACGATCTGGA
+
@@@DADDD8:CFH9E3:?AC?BF>AC+C+<E?:8C?FEE0?D0@:'--;A?B;>3(6;@B>5;5B@@A@:3>A########################### 
@SRR3099585.2 HWI-ST365:427:H8K2HADXX:1:1101:1653:2087/1 
GTCGACGAATATAAAGTTATTGGGAGAGACGCTGAAGGTCGCGTTGGAGATGGACTCAATTGCGCTTCGCGTTCGCCTCGTGGGTGTTCGCCCGGCTCAC
+
@CBFFFFFHHGHHJJJHHJJJJJJJJJIJJJJJJIJJJGHJJJJJJHHHHHFFFFFEEEEEEDDDDDDDDDDDDDDDDDDDDDDBDDDDDDDDDDDDDDD
```

## The analysis workflow

We now present the analysis protocol of the 6 input files that correspond to 3 biological replicates of the measurements of the number of transcripts for each of the 2 conditions, iron _vs._ no iron environment.

![](.gitbook/assets/image%20%2885%29.png)

Metadata are associated with the whole experiment. We extracted the metadata for the 6 input files chosen to fill a file called `conditions.txt`. The content of this file is given in table form:

| SampleID | Iron | Light | Time | Condition |
| :--- | :--- | :--- | :--- | :--- |
| SRR3105699 | Depleted | Light | 9h | CondA |
| SRR3105698 | Depleted | Light | 9h | CondA |
| SRR3105697 | Depleted | Light | 9h | CondA |
| SRR3099587 | Standard | Light | 9h | CondB |
| SRR3099587 | Standard | Light | 9h | CondB |
| SRR3099587 | Standard | Light | 9h | CondB |

### A workflow to reply the scientific question

Many tools are required to answer our question.

{% hint style="danger" %}
As with any bioinformatic analysis, the choice of tools used and associated parameters depends on the sequencing technology, the organism studied and the scientific question asked.
{% endhint %}

We part in two main steps the whole analysis based on the number of input files. The first step concerns the workflow for the transformation of the 6 measurement files \(the short nucleic sequences, ie. "reads"\) into an estimate of the number of transcripts per gene that will be filed into a two-column table \(gene name and its count\), one table for each input file. Those 6 count tables constitute the entries for the second step which is an application of a statistical model designed for our scientific question. After the second step, we will obtain all the graphs and tables validating the statistical decisions and also the list of differentially expressed genes between the 2 conditions. 

The whole analysis requires many computing resources and is achieve with many tools. Our workflow \(the arrangement of the different tools between them\) is illustrated by the next figure, where computational tools stand in blue while input files are in green. 

![Overview of the analysis workflow](.gitbook/assets/fair_wf_init_ve.png)

Input data stands in green boxes and processed data in blue boxes. Reads concern the sequences fastq files that we will download from the data center \(NCBI or EBI\) just after. Reference genome and genome annotations will be provided in a next session. Blue boxes names refer to tools used during the wokflow. Briefly, FASTQC allows us to control the sequencing quality of the sequences, bowtie2 will **align each reads on the genomic sequence**, SAMtools realises **some filters and some format transferts**, IGV can be used to visualize the alignments, HTSeq will **count how many reads are aligned on each gene**, and DESeq2 is the statistical modelisation to infer or not a differential expression for each gene. From a strict point of view, the analysis workflow concerns only the three actions marked in bold.

## Project conception

Each step of a computer workflow provides one or more files. The first thing to do is to organize all these files. To do this, we have chosen to create a folder for each step of the analysis. By hand? No !!!

We will compose a "script" that will create the folders for us. What is the interest of composing a script? As soon as you change data, you just have to replay the script and a new folders architecture will be ready to this new project.

Create the architecture for our project will be done by using a terminal and writing a "bash" script \(.sh\) in a text editor. Bash is a command line interpreter and also the name of a command language. Both are often use in Unix systems.

But, the first think to do is to open a terminal, ie. a windows where your PC will waiting for your commands. 

## The Terminal: the Swiss army knife

In this formation, the terminal is the star. We will use it all the time. So we will first see how to install and open it.

### What is a terminal?

A terminal is the way for us to communicate with the computer using a number of commands. When we send these commands, the computer answers us.

* Where am I? `pwd`
* What are the files in this folder? `ls` 
* What's my login name, or who am I? `whoami` 
* Can you create a "toto" folder for me, please? `mkdir toto`
* ...

The language to speak with our computer is called bash. We change the font to highlight the `bash` commands in the text.

### **In Ubuntu**

Terminal is already installed in Ubuntu. To open terminal in Ubuntu, open the applications by clicking at the bottom left. 

![](.gitbook/assets/image%20%28177%29.png)

After, write "terminal" in the search bar and click on the icon.

![](.gitbook/assets/image%20%28189%29.png)

### **In Mac OsX**

Terminal is already installed. To open terminal in Ubuntu, open Finder and click on Application.

![](.gitbook/assets/image%20%28140%29.png)

### In Windows

**Installation:** Since the last updates, it is possible to install a Ubuntu terminal on Windows. You have to go to the windows store and then search and download: ubuntu 18.04 LTS.

![](.gitbook/assets/image%20%28100%29.png)

**Launch:** Once the installation is complete, all that remains is to launch it:

![](.gitbook/assets/image%20%2894%29.png)

**Configuration at first launch:** You must enter a user name and then type a password twice. This is normal if you do not see any characters when you enter your password. They are hidden.

Example :

```text
Installing, this may take a few minutes...
Please create a default UNIX user account. The username does not need to match your Windows username.
For more information visit: https://aka.ms/wslusers
Enter new UNIX username: tdenecker
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Installation successful!
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

tdenecker@DESKTOP-B8ON598:~$
```

**Find the terminal:** Using cortana, you can type ubuntu to find the terminal easily:

![](.gitbook/assets/image%20%28109%29.png)

Et voilà! You are equipped for the rest of the training!

## The bash language

The terminal is now waiting for your command and offer you a line where you can write it. This line traditionally ends with a `$` \(sometimes it can be another character, check yours\):

```text
tdenecker@DESKTOP-B8ON598:~$ 
```

We use the bash language to talk with the computer throught the terminal. In the next, we present you some of the bash commands. 

A command have always the same structure : the command's name followed or not by options if we want to modify its default behavior. 

Many bash commands are explained by one command, the `man` command ; `man` is an abbreviation of the word "**man**ual". For example, we can write "`man man`", where the first `man` indicates that we are requesting the manual and the second `man` is a command's option which here, completes our request for the manual by specifying that we want the manual for the `man` command.

```text
tdenecker@DESKTOP-B8ON598:~$ man man
```

After entering a carriage return, the bash language print to the terminal the description of the man command \(type "q" to quit the manual\). Try that!

## Initiate the "Fair\_project"

To initiate our project, we will follow these steps:

1. Create a folder for the project
2. Write a code to create a list of folders to organize the data and steps associated with the project
3. Run our code and see the result

The first step will be done with bash commands and launched into a terminal, the 2nd will be done into a text editor and after, the 3rd step will be to execute our code on the terminal as if it was a bash command.

#### How to create a folder with the commad line? `mkdir`

We create a folder with the `mkdir` \(**m**a**k**e **dir**ectory\) bash command. This command take the name of the folder as argument. We choose this name \(eg. "Fair\_project"\):

```text
tdenecker@DESKTOP-B8ON598:~$ mkdir Fair_project
```

This `mkdir` command creates a folder in the working directory \(the folder where we are\). But after typing the carriage return, we can't see anything ... yet the command was carried out. We use another bash command to check the presence of our new folder.

#### How to check the presence of the folder? `ls`

Using the `ls` \(**l**i**s**t\) bash command, we now can see our new folder:

```text
tdenecker@DESKTOP-B8ON598:~$ ls
Fair_project
```

This `ls` command lists all folders and files presents from the working directory. It has many options to modifiy the default behavior, _eg_.: having more details with `-l`, sorting the list by modification times `-t` or file size `-s`, etc. Have a look to its manual with the `man ls` command and try the options, one by one and also together.

#### How to go into the folder? `cd`

Now we want to work on the folder we just create for this project, and for all the rest of the course. The solution is to go into the folder. We use the `cd` \(**c**hange **d**irectory\) bash command for that: 

```text
tdenecker@DESKTOP-B8ON598:~$ cd Fair_project/
tdenecker@DESKTOP-B8ON598:~/Fair_project$
```

After the cd command, we see that the text before the `$` has changed. It now contains the name of our folder. It tells us the folder we are in, nammed the "working directory".

## Script creation

 We plan to create a folder for the data related to the samples, the genome sequence and its reference annotation, for each step of the workflow, and for the graphs of the results. It will constitute the folders achitecture. But we won't do that manually, we prefer to create a code/a script for that.

To create the first version of our script, we first open a **text editor** to write the commands. 

### Open a text editor

**The text editor:** A text editor is software designed for creating and editing text files.There are many solutions \(each programmer has his favorite\). We propose 4 of them:

* [Sublime Text](https://www.sublimetext.com/)
* [Atom](https://atom.io/)
* [Notepad++](https://notepad-plus-plus.org/)
* [Emacs](https://www.gnu.org/software/emacs/)

They should not be confused with word processing tools that format text. You shouldn't use them to write code! The best known are Word and the equivalents at Libre office, WordPad, Google Docs,....

{% hint style="info" %}
For the most courageous among you, it is quite possible to use the text editors available in the terminal such as VI, VIM,... But we do not recommend it for beginners.
{% endhint %}

Here is the command for the script creation with, as _eg.,_ the `nano` editor:

```text
nano FAIR_script.sh
```

In the editor, we write a command that create a folder for our project \(`mkdir` bash command\). We call it "Project". We also create a sub-folder for each step of the analysis, for the data related to the samples, for the genome sequence and its reference annotation, and for the graphs of the results. We save the code \(to save with the nano editor follow the help at the bottom of the screen, where `^` stands for the `crtl` key of your keyboard ; nano is case sensitive\), we give it a file name \(_eg_. Fair\_script.sh\) and we can see its content in the terminal thank to the `cat` bash command:

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ cat Fair_script.sh
mkdir Project
mkdir Project/samples
mkdir Project/annotations
mkdir Project/bowtie2
mkdir Project/fastqc
mkdir Project/genome
mkdir Project/graphics
mkdir Project/htseq
mkdir Project/reference
mkdir Project/samtools
```

Now we launch our script \(add the command `bash` before the script name\):

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ bash Fair_script.sh
```

To see the result \(the folders architecture\), we use the `ls` command with the name of the folder of interest, `Project`:

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ ls Project
annotations  bowtie2  fastqc  genome  graphics  htseq  reference  samples  samtools
```

## Point about the download of the input files

As input data, we look for "RNAseq" data. They are composed of some huge files, each of them often counting more than a Go \(Go stands for **G**iga **o**ctets, where an octet is a unit of digital information and Giga correspond to 10⁹ units\). We present here 3 ways to download files of reads sequences with their pro&cons concerning time transfer duration, reliability of the transferred data, compressed data or not \(compression provide smaller file and so need less time to transfer\), and needed steps if exist before launching the transfer:

| method \(target site\) | pro | cons |
| :--- | :--- | :--- |
| **fastq\_dump** \(NCBI\) | ![](.gitbook/assets/fair_gitbook_logook.png)highly reliable | ![](.gitbook/assets/fair_gitbook_logocaution.png)very slow ![](.gitbook/assets/fair_gitbook_logocaution.png)file in `fastq` format |
| **wget** \(EBI\) | ![](.gitbook/assets/fair_gitbook_logook.png)reliable ![](.gitbook/assets/fair_gitbook_logook.png)files in`fastq.gz` format | ![](.gitbook/assets/fair_gitbook_logocaution.png)medium speed ![](.gitbook/assets/fair_gitbook_logocaution.png)need a preparation step |
| **ascp** \(EBI\) | ![](.gitbook/assets/fair_gitbook_logook.png) high speed ![](.gitbook/assets/fair_gitbook_logook.png)files in`fastq.gz`format | ![](.gitbook/assets/fair_gitbook_logocaution.png)lack of reliability in wifi ![](.gitbook/assets/fair_gitbook_logocaution.png)need a preparation step |

### How to find information for download?

To download data, we must specify which data we want. In order to automate the download we look for the data URLs. These URLs depend on the data center you are exploring \(NCBI-SRA or EBI-ENA\). Here are some screenshots corresponding to the NCBI center.

![1\) find the experiment \(eg. click on the last URL\)](.gitbook/assets/image%20%28157%29.png)

![2\) note the SRRxxx \(fastq-dump, NCBI\) or the PRJNAxxx \(EBI\)](.gitbook/assets/image%20%2888%29.png)

### How to download web content with command line?

We can retrieve content from the web. The easiest way is to click on the links of the pages we display in our browser. But this is a manual action and we want to automate our work \(especially if we wanted to retrieve the 47 experiments!\). There are programs that allow us to retrieve content from the web with the command line and also to manage the downloaded files.

**wget** is a wide use to retrieve content from web servers \(`wget` derives from _**W**orld Wide Web_ and _**get**_\).   It is usually present by default on linux ; but on Mac you need to install `wget` \(follow help : [in french](https://www.memoinfo.fr/tutoriels-mac-osx/installer-wget-mac-osx/), [in english](https://www.wikihow.com/Install-Wget-on-Mac-%28Without-Macports%29)\). The `wget` usage is:

```text
wget <URL>
```

Sometimes the files we download will be compressed. This will be the case for sequence files, but also for some code files. Compression reduces the size of the file and therefore the download time. The compression program that has been used is usually indicated in the file name by its last letters. We will use many files that end in `.gz` indicating that they have been compressed with the `gzip` program.

Files that work together are also often compressed together by the way of creating an archive. The program that creates, compresses and then decompresses archives is `tar` \(for **t**ape **ar**chive, from the way archive are done in computer science\). Like the compression step, the archive of files in indicated in the name of the resulting archive file by adding characters `.tar`. The `tar` command can handle both de-archiving and decompression with appropriate options \(see in the example fastq\_dump just after the `tar` usage with the options `-x` for de-archiving, `-z` for decompression and `-f` for specifying the archive file to be processed\).

1. **fastq-dump** \(NCBI\): i\) the first time, install the fastq-dump pakage, ii\) and next download data:

i\) install the fastq\_dump package:

```text
wget --quiet "https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.9.2/sratoolkit.2.9.2-ubuntu64.tar.gz"
tar -xzf sratoolkit.2.9.2-ubuntu64.tar.gz
rm sratoolkit.2.9.2-ubuntu64.tar.gz
PATH=/home/sratoolkit.2.9.2-ubuntu64/bin:$PATH
```

ii\) download one file:

```text
fastq-dump SRR3105699
```

This method will not be used for the future.

2. **wget** \(EBI\): with `wget` we need to give the URL link to download the sequence file. But how will we find the URL? By requiring the PRJNAxxx number onto the EBI search site and click on it:

![EBI search page](.gitbook/assets/image%20%2835%29.png)

A click on the PRJNA304086 number give access to a table, where we will change the selected columns \(click on the "Select columns" URL in the middle\):

![EBI-ENA: PRJNA304086 metadata table](.gitbook/assets/image%20%2876%29.png)

Among all the available meta-data, we check 4 columns: "Study accession", "Experiment accession", "Run accession", "fastq md5", and "FASTQ file \(FTP\)". 

![EBI-EBA: columns choice panel](.gitbook/assets/image%20%2830%29.png)

Then we click on the "TEXT" link \(just above the columns choice panel\). 

We obtain a PRJNA304086 file which contains the metadata we selected: the SRXxxx and SRRxxx accession numbers, the md5 \(a strange mixte of digits and characters we will need next\), and the URL link for the fastq files :

```text
study_accession	experiment_accession	run_accession	fastq_md5	fastq_ftp
PRJNA304086	SRX1452060	SRR2960338	e854e5445d32bfc64464c03584500f5b	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/008/SRR2960338/SRR2960338.fastq.gz
PRJNA304086	SRX1452063	SRR2960341	d60f71f8b001c85de09a73170d0b3335	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/001/SRR2960341/SRR2960341.fastq.gz
PRJNA304086	SRX1452064	SRR2960343	057bcfd55bdcccab35ba834b08ee84b9	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/003/SRR2960343/SRR2960343.fastq.gz
PRJNA304086	SRX1452071	SRR2960356	9107eb12bd547a0c06a4129427266cf2	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/006/SRR2960356/SRR2960356.fastq.gz
PRJNA304086	SRX1452072	SRR2960357	db5c44d73023adba175b3b36d628299c	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/007/SRR2960357/SRR2960357.fastq.gz
PRJNA304086	SRX1452073	SRR2960358	97222fb332bf28f0cfe038e241d51508	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/008/SRR2960358/SRR2960358.fastq.gz
PRJNA304086	SRX1452825	SRR2961630	2c7d97815a5b4a03005ff5fd35c71e46	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/000/SRR2961630/SRR2961630.fastq.gz
PRJNA304086	SRX1452828	SRR2961631	b6ff7062e7057f99ecd94645d6715f3b	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/001/SRR2961631/SRR2961631.fastq.gz
PRJNA304086	SRX1452829	SRR2961633	4d7b9451064114c5d7ca0b3a59056b97	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/003/SRR2961633/SRR2961633.fastq.gz
PRJNA304086	SRX1452861	SRR2961666	22d6fdb7253ee11413a99f22212c8807	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/006/SRR2961666/SRR2961666.fastq.gz
PRJNA304086	SRX1452862	SRR2961667	b204cdd9cbd8aaf4ae627389b22f31d4	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/007/SRR2961667/SRR2961667.fastq.gz
PRJNA304086	SRX1452863	SRR2961668	2e243a53705aeebdfdf1c6cc9d6111ea	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/008/SRR2961668/SRR2961668.fastq.gz
PRJNA304086	SRX1453625	SRR2962703	f9b516e5bdb9f4170f87bfcedaba94b1	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/003/SRR2962703/SRR2962703.fastq.gz
PRJNA304086	SRX1453626	SRR2962704	369f37a4e923250d4aa4d858f0447627	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/004/SRR2962704/SRR2962704.fastq.gz
PRJNA304086	SRX1453627	SRR2962705	025153cd56b3f454098a977334a25aed	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/005/SRR2962705/SRR2962705.fastq.gz
PRJNA304086	SRX1456515	SRR2967072	1a4b75baeb33bf239233b1d17e4de01a	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/002/SRR2967072/SRR2967072.fastq.gz
PRJNA304086	SRX1456516	SRR2967073	e38333c18b536bd1b113bb542e8755e2	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/003/SRR2967073/SRR2967073.fastq.gz
PRJNA304086	SRX1456517	SRR2967074	dc7d01a5a2565d5605c557558eede9cb	ftp.sra.ebi.ac.uk/vol1/fastq/SRR296/004/SRR2967074/SRR2967074.fastq.gz
PRJNA304086	SRX1459990	SRR2970594	929c9ebfca1e391fe3ae1a581d80daa1	ftp.sra.ebi.ac.uk/vol1/fastq/SRR297/004/SRR2970594/SRR2970594.fastq.gz
PRJNA304086	SRX1459991	SRR2970597	338d0120e9058b6f569ea748e6416917	ftp.sra.ebi.ac.uk/vol1/fastq/SRR297/007/SRR2970597/SRR2970597.fastq.gz
PRJNA304086	SRX1459992	SRR2970598	21590eb0c98c5d62ea084fbabd301c61	ftp.sra.ebi.ac.uk/vol1/fastq/SRR297/008/SRR2970598/SRR2970598.fastq.gz
PRJNA304086	SRX1460213	SRR2970811	727105f42559583d80a134d7f176c3e2	ftp.sra.ebi.ac.uk/vol1/fastq/SRR297/001/SRR2970811/SRR2970811.fastq.gz
PRJNA304086	SRX1460214	SRR2970812	f8ad982ed839353b24f0d87045f3cf48	ftp.sra.ebi.ac.uk/vol1/fastq/SRR297/002/SRR2970812/SRR2970812.fastq.gz
PRJNA304086	SRX1477552	SRR2989038	a1b9d8eb39797e3ed7852fe84d9d27a3	ftp.sra.ebi.ac.uk/vol1/fastq/SRR298/008/SRR2989038/SRR2989038.fastq.gz
PRJNA304086	SRX1477554	SRR2989041	1402b158a43de2cd4766f719688b11aa	ftp.sra.ebi.ac.uk/vol1/fastq/SRR298/001/SRR2989041/SRR2989041.fastq.gz
PRJNA304086	SRX1477556	SRR2989043	dd27a1818185454a5ad84bb7c193764f	ftp.sra.ebi.ac.uk/vol1/fastq/SRR298/003/SRR2989043/SRR2989043.fastq.gz
PRJNA304086	SRX1529449	SRR3099585	87cf18f083c9954ec16070c306a4a3f0	ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/005/SRR3099585/SRR3099585.fastq.gz
PRJNA304086	SRX1529450	SRR3099586	3f20b8a3d7d37d85d1d6a1b1b8ffd97b	ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/006/SRR3099586/SRR3099586.fastq.gz
PRJNA304086	SRX1529451	SRR3099587	a2ad18cc00016e22f262e4071a03f874	ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/007/SRR3099587/SRR3099587.fastq.gz
PRJNA304086	SRX1530331	SRR3101073	cc2673641d3822b6c7e86eb25402257c	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/003/SRR3101073/SRR3101073.fastq.gz
PRJNA304086	SRX1530332	SRR3101074	df546ffc5ef06f8a179af98e8a74a71d	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/004/SRR3101074/SRR3101074.fastq.gz
PRJNA304086	SRX1530333	SRR3101075	730ffe31b15b3ade59a0dbe22495a6ba	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/005/SRR3101075/SRR3101075.fastq.gz
PRJNA304086	SRX1531908	SRR3103205	ce098794833978225409da09bbad65ba	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/005/SRR3103205/SRR3103205.fastq.gz
PRJNA304086	SRX1531909	SRR3103206	c95444f015fec9be0e70060a3c014485	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/006/SRR3103206/SRR3103206.fastq.gz
PRJNA304086	SRX1531910	SRR3103207	b732b6f0834425dc7a57ad2660b57a9e	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/007/SRR3103207/SRR3103207.fastq.gz
PRJNA304086	SRX1533779	SRR3105362	26cda8d7a82a3eb05e20a5476fc7431b	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/002/SRR3105362/SRR3105362.fastq.gz
PRJNA304086	SRX1533780	SRR3105363	298c560c0733e74945448b9a3f7f1395	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/003/SRR3105363/SRR3105363.fastq.gz
PRJNA304086	SRX1533782	SRR3105364	08cc2bc3466deee0b46eba61869b63f1	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/004/SRR3105364/SRR3105364.fastq.gz
PRJNA304086	SRX1534049	SRR3105697	48bd094825243d03bd59e2801ccf9931	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/007/SRR3105697/SRR3105697.fastq.gz
PRJNA304086	SRX1534051	SRR3105698	0102f812c71517b8e550702f320ac607	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/008/SRR3105698/SRR3105698.fastq.gz
PRJNA304086	SRX1534052	SRR3105699	e7a74957778b75ac332b67a87da495ea	ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/009/SRR3105699/SRR3105699.fastq.gz
PRJNA304086	SRX1540739	SRR3112575	80de77bf3e636f44cb291abbd881ce7d	ftp.sra.ebi.ac.uk/vol1/fastq/SRR311/005/SRR3112575/SRR3112575.fastq.gz
PRJNA304086	SRX1540740	SRR3112576	19055c215668a6c4fc1c57c4b598f064	ftp.sra.ebi.ac.uk/vol1/fastq/SRR311/006/SRR3112576/SRR3112576.fastq.gz
PRJNA304086	SRX1540742	SRR3112577	91cd781fec3647499026d30ee8d2fdbf	ftp.sra.ebi.ac.uk/vol1/fastq/SRR311/007/SRR3112577/SRR3112577.fastq.gz
PRJNA304086	SRX1540743	SRR3112578	7ff7bdf92b6ca204d98ed911179edce8	ftp.sra.ebi.ac.uk/vol1/fastq/SRR311/008/SRR3112578/SRR3112578.fastq.gz
PRJNA304086	SRX1540744	SRR3112579	1cac0a93a87f66db4ba876d24d66b26c	ftp.sra.ebi.ac.uk/vol1/fastq/SRR311/009/SRR3112579/SRR3112579.fastq.gz
PRJNA304086	SRX1540741	SRR3112580	dbc540c1b2af452740eec4d8816e409e	ftp.sra.ebi.ac.uk/vol1/fastq/SRR311/000/SRR3112580/SRR3112580.fastq.gz
```

### How to ensure the quality of data transfer?

The download will be perfect if the downloaded file is identical to the original file. But comparing their number of bytes is not enough \(e.g. there could be the same number of letters but not the same letters\). Instead, we compare the 2 digital footprints of the  files, which is a kind of encoding of their content. 

![](https://s3.amazonaws.com/media-p.slid.es/uploads/991142/images/5692571/pasted-from-clipboard.png)

There are several algorithms for the calculating digital footprints. Here are 3 of them:

1. **MD5** \(Message Digest 5\)
2. SHA1: Secure Hash Algorithm, expressed with 32 characters
3. SHA256: 40/64 characters

Sequence data centers offer MD5 footprints.

![Find the difference? Easy, ins&apos;t it?](https://s3.amazonaws.com/media-p.slid.es/uploads/991142/images/5692630/pasted-from-clipboard.png)

And now:

> `CAGL0I06743g ATGCCTAACAAAGTATTTAACGTGGCAGTCTTCTTTGTCGTGTTCAGAGAATGTTTGGAA GCTGTTGTTATCGTTTCCATTCTATTGTCTTTCCTGAAGCAAGCAATTGGTTCCAAAGAT ATTAAATTATATCGCAAATTGCGTAAGCATGTTTGGATTGGTGTCGCACTCGGGTTCTTT ATCTGTTTAGTGATTGGTGCTGGTTTCATTGGTGCCTACTACTCTTTACAGAAAGATATC TTTGGTTCTACAGAAGATTTATGGGAAGGTATTTTCTGTATGATTGCTACTACTATGATC AGTATGATGGGTATTCCAATGTTGCGTATTAACAAGTTGCAAAGTAAATGGCGTGTGAAG CTTGCTCGTTCACTTGTTGATATTCCAAAGAGAAAGCGTGATTACTTCAGGATTGGTTAT TTGACTAGACGTTATGCGATGTTCATCTTACCTTTCATCACTGTTTTAAGAGAAGGTTTG GAAGCTGTCGTTTTTGTCGCTGGTGCCGGTATTACCACGAAGGGTTCTCACGCATCCGCT TATCCTTTACCAGTCGTTGTTGGTCTAATTGCTGGTTTTATAGTTGGATTCCTATTATAT TACGGTACTTCAAAGTCTTCCATGCAAATCTTTTTAGTTATTTCTACCTCCATTCTTTAC TTAATTGCAGCCGGTTTGTTCTCAAGAGGTGCTTGGTACTTCGAAAACTACAGATTCAAC AAGGCAACGGGTGGTGATGCTTCTGAAGGTGGTGACGGTAATGGTTCCTATAATATCGCT AAATCAGTTTACCATGTTAACTGTTGTAATCCTGAACTGGACAACGGTTGGGATATTTTC AACGCTTTGTTGGGATGGCAAAATACTGGTTATCTGTCCAGTATTCTTTGTTACAATATT TACTGGGTTGTTCTAATTATTGTCTTAGGTTTGATGATGCACGAAGAAAGATATGGCCAC TTACCATTTATGAGAAACGTTGGTATGAGACACTTAAACCCAGGTTATTGGATAAAGAAC AAGAAGAAGGACGAATTGACTGATGAACAAAAGGCTGAGCTGCTAAACCGTATGGACAAC ATTCAATTCAATGAAGAAGGTGATATTGTCGCTCATGCTAACGAAGAAGAACACGACCAG GAGAGTTCTTTGTTGAGAGGCAACTCTAACAAGATGGGATCCAAGGAAGAATTGAACTTC AAGGTTACCACTACCAGTTCCGACTAA`

> `CAGL0I06743g ATGCCTAACAAAGTATTTAACGTGGCAGTCTTCTTTGTCGTGTTCAGAGAATGTTTGGAA GCTGTTGTTATCGTTTCCATTCTATTGTCTTTCCTGAAGCAAGCAATTGGTTCCAAAGAT ATTAAATTATATCGCAAATTGCGTAAGCATGTTTGGATTGGTGTCGCACTCGGGTTCTTT ATCTGTTTAGTGATTGGTGCTGGTTTCATTGGTGCCTACTACTCTTTACAGAAAGATATC TTTGGTTCTACAGAAGATTTATGGGAAGGTATTTTCTGTATGATTGCTACTACTATGATC AGTATGATGGGTATTCCAATGTTGCGTATTAACAAGTTGCAAAGTAAATGGCGTGTGAAG CTTGCTCGTTCACTTGTTGATATTCCAAAGAGAAAGCGTGATTACTTCAGGATTGGTTAT TTGACTAGACGTTATGCGATGTTCATCTTACCTTTCATCACTGTTTTAAGAGAAGGTTTG GAAGCTGTCGTTTTTGTCGCTGGTGCCGGTATTACCACGAAGGGTTCTCACGCATCCGCT TATCCTTTACCAGTCGTTGTTGGTCTAATTGCTGGTTTTATAGTTGGATTCCTATTATAT TACGGTACTTCAAAGTCTTCCATGCAAATCTTTTTAGTTATTTCTACCTCCATTCTTTAC TTAATTGCAGCCGGTTTGTTCTCAAGAGGTGATTGGTACTTCGAAAACTACAGATTCAAC AAGGCAACGGGTGGTGATGCTTCTGAAGGTGGTGACGGTAATGGTTCCTATAATATCGCT AAATCAGTTTACCATGTTAACTGTTGTAATCCTGAACTGGACAACGGTTGGGATATTTTC AACGCTTTGTTGGGATGGCAAAATACTGGTTATCTGTCCAGTATTCTTTGTTACAATATT TACTGGGTTGTTCTAATTATTGTCTTAGGTTTGATGATGCACGAAGAAAGATATGGCCAC TTACCATTTATGAGAAACGTTGGTATGAGACACTTAAACCCAGGTTATTGGATAAAGAAC AAGAAGAAGGACGAATTGACTGATGAACAAAAGGCTGAGCTGCTAAACCGTATGGACAAC ATTCAATTCAATGAAGAAGGTGATATTGTCGCTCATGCTAACGAAGAAGAACACGACCAG GAGAGTTCTTTGTTGAGAGGCAACTCTAACAAGATGGGATCCAAGGAAGAATTGAACTTC AAGGTTACCACTACCAGTTCCGACTAA`

Not so easy with huge sequence data files...

When working with downloaded huge sequence data, it is good practice to control the correct execution of the download. We use the `md5sum` command to compute the digital footprint of a file. We can exploit this digital footprint for any kind of file. Here is an example where the second file of text have only one letter changed from the first file \(see the condition's name of the two first lines\):

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ cat conditions.txt
SampleID        Iron    Light   Time    Condition_Name  wget    MD5
SRR3105699      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/009/SRR3105699/SRR3105699.fastq.gz  e7a74957778b75ac332b67a87da495ea
SRR3105698      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/008/SRR3105698/SRR3105698.fastq.gz  0102f812c71517b8e550702f320ac607
SRR3105697      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/007/SRR3105697/SRR3105697.fastq.gz  48bd094825243d03bd59e2801ccf9931
SRR3099587      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/007/SRR3099587/SRR3099587.fastq.gz  a2ad18cc00016e22f262e4071a03f874
SRR3099586      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/006/SRR3099586/SRR3099586.fastq.gz  3f20b8a3d7d37d85d1d6a1b1b8ffd97b
SRR3099585      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/005/SRR3099585/SRR3099585.fastq.gz  87cf18f083c9954ec16070c306a4a3f0
tdenecker@DESKTOP-B8ON598:~/Fair_project$ md5sum conditions.txt
6b61f0cae78171f3837b69446b41e5aa  conditions.txt

tdenecker@DESKTOP-B8ON598:~/Fair_project$ cat conditions2.txt
SampleID        Iron    Light   Time    Condition_Name  wget    MD5
SRR3105699      DEPLETED        LIGHT   9H      CondC   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/009/SRR3105699/SRR3105699.fastq.gz  e7a74957778b75ac332b67a87da495ea
SRR3105698      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/008/SRR3105698/SRR3105698.fastq.gz  0102f812c71517b8e550702f320ac607
SRR3105697      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/007/SRR3105697/SRR3105697.fastq.gz  48bd094825243d03bd59e2801ccf9931
SRR3099587      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/007/SRR3099587/SRR3099587.fastq.gz  a2ad18cc00016e22f262e4071a03f874
SRR3099586      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/006/SRR3099586/SRR3099586.fastq.gz  3f20b8a3d7d37d85d1d6a1b1b8ffd97b
SRR3099585      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/005/SRR3099585/SRR3099585.fastq.gz  87cf18f083c9954ec16070c306a4a3f0
tdenecker@DESKTOP-B8ON598:~/Fair_project$ md5sum conditions2.txt
3302fdec76f8a7db3b08abefee7727b6  conditions2.txt
```

 Their respective MD5 are really differents! 

We will use those MD5 to check the transfert quality of the sequences files.

### Preparation of a design file

For a better reproducibility of the analyses, we automate as many steps as possible using scripts. To do this, we will create a "design file" that will contain all the information for automation. It will the only data file we will manually write.

The mandatory information are:

* A unique name \(to avoid mixing data\)
* A way to distinguish the two conditions

In addition in this file:

* address for the wget \(to automate the download\)
* information to make the difference with other experiences

![The design file, conditions.txt](.gitbook/assets/image%20%28197%29.png)

It is possible to create this file with the excel spreadsheet program provided that you choose the "text" type and "tabulation" as the column separator as the saving format. 

![Saving in text format with tabulation as separator](.gitbook/assets/image%20%2898%29.png)

Save the file as: `conditions.txt` in the main folder. 

Obviously possible in a text editor! \(with tabs to separate the columns\).

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ cat conditions.txt
        Light   Time    Condition_Name  wget
SRR3105699      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/009/SRR3105699/SRR3105699.fastq.gz
SRR3105698      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/008/SRR3105698/SRR3105698.fastq.gz
SRR3105697      DEPLETED        LIGHT   9H      CondA   ftp.sra.ebi.ac.uk/vol1/fastq/SRR310/007/SRR3105697/SRR3105697.fastq.gz
SRR3099587      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/007/SRR3099587/SRR3099587.fastq.gz
SRR3099586      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/006/SRR3099586/SRR3099586.fastq.gz 
SRR3099585      STANDARD        LIGHT   9H      CondB   ftp.sra.ebi.ac.uk/vol1/fastq/SRR309/005/SRR3099585/SRR3099585.fastq.gz
```

#### Some trouble on comparison

{% hint style="warning" %}
_Why when I test my MD5 sum, the comparison is never true while when I display the numeric footprints, they are identical?_
{% endhint %}

If you have this trouble, probably you work with the Windows operating system. The problem comes from the characters used to mention the end of line \(which are not visible\). On linux systems it is `\n` while on windows, they are two `\r\n`. And depending on the text editor you used, one consequence is that `toto\n` is different from `toto\r\n` while they both display `toto`.

To solve it here are two solutions, the first with the `sed` command line on the terminal, or the second with the "Atom" editor.

The sed unix program stands for stream editor and is use to parse and transform text. To replace a motif \(old, here the 2 end windows characters\) by another motif \(new, here the unix end character\), we use the 's/oldMotif/newMotif/' sed syntax:

```text
sed 's/\r\n$/\n/' conditions.txt > conditionsCorr.txt
mv conditionsCorr.txt conditions.txt
```

![How to change the ends of lines in Atom editor](.gitbook/assets/image%20%2838%29.png)

## Automation of data downloading

### The loop concept

We will improve our script with the following objectives: browsing the `conditions.txt`file and for each line : 

* Recover important information: SampleID \(column 1\), address \(column 6\) and md5 \(column 7\)
* Download the fastq.gz file
* Compute the md5 of the downloaded file
* Confirmation of the correct download \(thank to md5 comparison\)

We use a **loop** when we want to make the computer repeat the same things but with different values.

![](.gitbook/assets/image%20%28206%29.png)

### Using a loop

We want to do the same action **for** each line of the `conditions.txt` file:

![Content of the conditions.txt file](.gitbook/assets/image%20%2854%29.png)

In bash, a `for` statement can have the following structure where my\_loop\_variable takes \(in computer science, a variable is a code element whose value changes during code execution\) one value after the other from the my\_list\_of\_values at each repeat: `for my_loop_variable in my_list_of_values ; do [...] ; done`

Here, the list of values are the lines of the file `conditions.txt` except the first row which contains the column titles. To print on screen or to list all lines except the first one, we use the `tail` bash command with the `-n` option which specifies the number of lines to list and with a value `+2` which means all lines from the second. You can try this command that gives all lines of `conditions.txt` but the fisrt one:

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ tail -n +2 ../../conditions.txt
```

To make sure that the result of this command feeds the loop, we add `$(...)` around. Here is our instruction `for` with my\_loop\_variable named `j`:

```text
for j in $(tail -n +2 ../../conditions.txt)
do
    [...]
done
```

The repeated instructions begin with the `do` command. When we meet the `done` command its means that we start again all the code \(at the moment represented by `[...]`\) for each line of `conditions.txt` file.

The necessary information stands in columns and their values change for each line. To manage those values, we create three variables, one for each value we select per line:

* from the 1st column we will feed a variable that we call "id"
* from the 6th column we will feed a variable that we call "access"
* and from the 7th column we will feed a variable that we call "md5"

And for each line or for each value of the loop variable `j`, we recover the 3 values corresponding to the 3 variables `id`, `access`, and `md5`. Here is the code for the `access` variable: 

```text
access=$( echo "${j}" | cut -f6 ) 
```

We decompose the code:

1. access to the content of the line \(the `j` variable\) by adding `${}` arround the variable name: `${j}` 
2. display of the line content as a string: `echo "${j}"` 
3. on this string, apply another command thanks to the symbol : `|`
4. select the content of 6th column: `cut -f 6` 
5. store the result in a new variable we call access: `access=$(` ...`)` 

This is the same for the three variables we want by adapting their name and column number:

```text
access=$( echo "${j}" | cut -f6 )
id=$( echo "${j}" | cut -f1 )
md5=$( echo "${j}" | cut -f7 
```

### Progressive construction of the script

We next provide a step-by-step decomposition of the script. We open our previous script creating the project architecture and we now pursuit by the download of the RNAseq files.

#### Display the step:

```text
echo "=============================================================="
echo "Download data from SRA"
echo "=============================================================="
```

#### Move to the folder where the fastq.gz will be:

`cd Project/samples`

#### Creating the loop: reading the conditions.txt file line by line

```text
IFS=$'\n'       # make newlines the only separator
for j in $(tail -n +2 ../../conditions.txt)
do
   
done
```

#### Retrieving the address, id, and md5 values

```
for j in $(tail -n +2 ../../conditions.txt)
do
    access=$( echo "$j" | cut -f6 )
    id=$( echo "$j" | cut -f1 )
    md5=$( echo "$j" | cut -f7 )
done
```

#### Display the data id

```text
IFS=$'\n'       # make newlines the only separator
for j in $(tail -n +2 ../../conditions.txt)
do

    access=$( echo "$j" | cut -f6 )
    id=$( echo "$j" | cut -f1 )
    md5=$( echo "$j" | cut -f7 )

    echo "--------------------------------------------------------------"
    echo ${id}
    echo "--------------------------------------------------------------"
    
done
```

#### Download the fastq.gz

```text
echo "=============================================================="
echo "Download data from SRA"
echo "=============================================================="

cd Project/samples

IFS=$'\n'       # make newlines the only separator
for j in $(tail -n +2 ../../conditions.txt)
do

    access=$( echo "$j" | cut -f6 )
    id=$( echo "$j" | cut -f1 )
    md5=$( echo "$j" | cut -f7 )

    echo "--------------------------------------------------------------"
    echo ${id}
    echo "--------------------------------------------------------------"
    
    wget ${access} # wget method
done

cd ../..
```

Reminder: **`${`**`access`**`}`** allows you to access the content of the "access" variable.

#### Compute and display the MD5 of the downloaded file:

```text
IFS=$'\n' # make newlines the only separator
for j in $(tail -n +2 ../../conditions.txt)
do
    access=$( echo "$j" | cut -f6 )
    id=$( echo "$j" | cut -f1 )
    md5=$( echo "$j" | cut -f7 )

    echo "--------------------------------------------------------------"
    echo ${id}
    echo "--------------------------------------------------------------"

    wget ${access} # wget method

    md5_local="$(md5sum $id.fastq.gz | cut -d' ' -f1)"
    echo $md5_local
done
```

#### Confirmation that the download is correct

```text
IFS=$'\n' # make newlines the only separator 
for j in $(tail -n +2 ../../conditions.txt) 
do
    access=$( echo "$j" | cut -f6 )
    id=$( echo "$j" | cut -f1 )
    md5=$( echo "$j" | cut -f7 )

    echo "--------------------------------------------------------------"
    echo ${id}
    echo "--------------------------------------------------------------"

    wget ${access} # wget method
    md5_local="$(md5sum $id.fastq.gz | cut -d' ' -f1)"
    echo ${md5_local}

    if [ "${md5_local}" == "${md5}" ]
    then
        echo "MD5SUM : Ok"
    else
       echo "MD5SUM : error"
       exit 1
   fi
done
```

To test if the 2 MD5s \(the local `md5_local` and the expected `md5`\) are identical, we use an `if` statement. In this `if` statement, `==` stands for an equality test \(while  `!=` stands for an inequality test\). 

A natural language translation of this test is: if the md5 present in the `conditions.txt` file \(and stored into the `md5_local` variable\) is equal to the md5 of the downloaded file \(and stored into the `md5` variable\) then display "Done" otherwise display "Nope" and exit with an error 1 \(a bash convention is that when the command run  without trouble, it return a null value\).

#### Back to the main repository

`cd ../..`

### Complete script to this point

The complete script to this point should be saved in a file \(with a `.sh` extension\) on your folder, with a name \(eg. `Fair_script.sh`\).

```text
##------------------------------------------------------------------------------
## FAIR script
## Author: T. Denecker & C. Toffano-Nioche
## Affiliation: I2BC
## Aim: A workflow to process RNA-Seq.
## Organism: O. tauri
## Date: Jan 2019
## Step :
## 1- Create tree structure
## 2- Download data from SRA
##------------------------------------------------------------------------------

echo "=============================================================="
echo "Creation of tree structure"
echo "=============================================================="

mkdir Project
mkdir Project/samples
mkdir Project/annotations
mkdir Project/bowtie2
mkdir Project/fastqc
mkdir Project/genome
mkdir Project/graphics
mkdir Project/htseq
mkdir Project/reference
mkdir Project/samtools

echo "=============================================================="
echo "Download data from SRA"
echo "=============================================================="

cd Project/samples

IFS=$'\n'       # make newlines the only separator
for j in $(tail -n +2 ../../conditions.txt)
do
    
    # Get important information from the line
    access=$( echo "$j" | cut -f6 )
    id=$( echo "$j" | cut -f1 )
    md5=$( echo "$j" | cut -f7 )

    echo "--------------------------------------------------------------"
    echo ${id}
    echo "--------------------------------------------------------------"

    # Download file
    wget ${access} # wget method

    # Get md5 of downloaded file
    md5_local="$(md5sum $id.fastq.gz | cut -d' ' -f1)"
    echo $md5_local
    
    # Test md5 
    if [ "${md5_local}" == "${md5}" ]
    then
        echo "Done"
    else
        echo "Nope"
        exit 1
    fi
done

cd ../..
```

we launch the script

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ bash Fair_script.sh
```

Here is the folder architecture at this point, we see that the 6 fastq.gz files were downloaded \(we add the `-R` option to `ls` that apply `ls` to all the subfolders\):

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ ls -R Project
Project:
annotations  bowtie2  fastqc  genome  graphics  htseq  reference  samples  samtools

Project/annotations:

Project/bowtie2:

Project/fastqc:

Project/genome:

Project/graphics:

Project/htseq:

Project/reference:

Project/samples:
SRR3099585.fastq.gz  SRR3099586.fastq.gz  SRR3099587.fastq.gz  SRR3105697.fastq.gz  SRR3105698.fastq.gz  SRR3105699.fastq.gz

Project/samtools:
```

## Conclusion

In this chapter we have discovered the concrete example chosen to implement the tools that will help us to make our projects more reproducible. We have seen the files corresponding to the input data as well as the different stages of their processing. We have discovered our working environment: accessing a terminal window, launching command line commands with the bash language. For questions of reproducibility, we have created a file containing the commands to automatically create the project architecture \(with a subfolder for each step\) and download the input files.

## It's up to you! 

{% hint style="success" %}
If not done during your reading, repeat the command lines of this chapter. For the next chapter, you should have the 6 fastq.gz files in the `Project/samples` repository 
{% endhint %}

### Resources

Here are some resources to learn differently the same command line concepts:

* [Introduction to the Command Line for Genomics](https://datacarpentry.org/shell-genomics/) : a Carpentry lessons
* The unix part of [Happy Belly Bioinformatics](https://astrobiomike.github.io/unix), a JOSE paper 
* [Explain shell command](https://explainshell.com/) : a web site that allows pasting in a command, brooking it down into individual parts and give the corresponding manual

Digital Object Identifier \(doi\) of the published articles concerning data and tools used in the analysis workflow:

* RNAseq data doi: 10.1186/s12864-016-2666-6 
* fastQC is not published, no doi avalaible
* bowtie2 doi: 10.1038/nmeth.1923 
* samtools doi: 10.1093/bioinformatics/btp352 
* IGV doi: 10.1038/nbt.1754 
* HTseq doi: 10.1093/bioinformatics/btu638 
* DESeq2 doi: 10.1186/s13059-014-0550-8

