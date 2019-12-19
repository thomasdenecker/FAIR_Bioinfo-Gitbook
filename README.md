# Gain in bioinformatic analysis reproducibility as a goal

A recent study published in Nature has shown that nearly 70% of experiments in Biology are not reproducible. It is therefore essential to put in place good practices to ensure data integrity and reproducibility of analytical results. Concerning data, the FAIR-data principles are increasingly being used. These same principles can be used in the analysis department to guarantee identical results from the same data set and over time ... The objective of this document is to propose a range of functionalities to make a complete bioinformatics analysis reproducible. 

{% hint style="info" %}
This course is Open Source \(under the CeCILL 2.1 license\). You can find it on the github of the FAIR\_Bioinfo project: [here](https://github.com/thomasdenecker/FAIR_Bioinfo/blob/master/LICENCE).
{% endhint %}

## Objectives

The purpose of this document is to provide a concrete example of the implementation of a tools panel to increase reproducibility in science in the case of processed data by successive computational and analysis steps. 

The aim of this concrete example is to select genes that do not behave in the same way between two experimental conditions. In short, we retrieve the data from public databases \(ENA/SRA\), we perform a reproducible analysis with a workflow system \(snakemake\) in a virtual environment \(docker\) whose entire code, versioned \(git\), is available in open source \(Github and dockerhub\). The visualization of the results is dynamic \(shiny app\) and a report \(Rmarkdown\) in pdf or html is available. It groups the results of the analysis and details all the parameters chosen by the user. The features presented are not dependent on this example, which could be replaced by any type of analysis.

The tools we choose to manage the reproducibility have others equivalents but the solution proposed is based on our own daily research experiences in bioinformatics. 

We also choose to start from a very low level of computer skills so that all this documentation can be followed by our biologist colleagues. During this training, we will use a number of tools. To use a computer tool, there is three steps to follow: i\) install it and configure the environment, ii\) use it, iii\) develop it. In this book, these 3 steps will be discussed for several of the tools presented because the development step is the one that allows maximum autonomy.

{% hint style="info" %}
This training was initially designed for a French audience. Indeed, there are few resources on reproducibility in the language of Molière. You can find all the slides presented on the FAIR\_Bioinfo Github repository: [https://github.com/thomasdenecker/FAIR\_Bioinfo](https://github.com/thomasdenecker/FAIR_Bioinfo)
{% endhint %}

## The context of reproducibility

### Some definitions

Recently, [an American report](https://www.nap.edu/catalog/25303/reproducibility-and-replicability-in-science) was published to clearly define what reproducibility is in Science in 2019 .

> **Reproducibility** means computational reproducibility—obtaining consistent computational results using the same input data, computational steps, methods, code, and conditions of analysis.

The report distinguishes between Replicability

> **Replicability** means obtaining consistent results across studies aimed at answering the same scientific question, each of which has obtained its own data.

​We can go further by opposing them to a 3rd term Repeatability  \(adapted from de [Hans E. Plesser, Front. Neuroinf. , 2017](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5778115/)\)

> **Repeatability**  means obtaining the closest possible results by performing an identical experiment \(methods, equipment, experimenters, laboratory and conditions\)

### Recommendations for Reproducibility in 2019

The American report recommends 4 main lines of action to be reproducible in 2019

* **Description of the experimental part**   \(Methods, instruments, procedures, measurements, experimental conditions  \)
* **Description of the computational part**   \(Steps in data analysis and technical choices  \)
* **Description of the statistical part**   \(Analytical decisions: when, how, why\)
* **Discussion of the choices and results obtained**

In short, everything must be described and justified so that another researcher can conduct the same scientific process.

### In reality ?

#### In Biology

There is a real problem of reproducibility in Biology. **70 %** analyses in Experimental Biology are not reproducible \([Monya Baker, Nature, 2016](https://www.nature.com/news/1-500-scientists-lift-the-lid-on-reproducibility-1.19970)\). 

![Monya Baker, Nature, 2016](.gitbook/assets/image%20%28167%29.png)

#### In computer science

The same problem is described in computer science \([Collberg et al, University of Arizona TR 14-04, 2015](http://reproducibility.cs.arizona.edu/)\). Here, there is 65% failure to reuse the software.

![Collberg et al, University of Arizona TR 14-04, 2015](.gitbook/assets/image%20%28146%29.png)

#### In Bioinformatics

Bioinformatics is also affected... Problems still too frequent such as for example :

* **Impossibility to install tools** 
  * OS not compatible     \(Windows - MacOS - Linux\)
  * Dependency no longer available  
* **Update of the tool rendering the codes unusable** 
  * The syntax differences between  Python 2 and Python 3 !
  * Changing the arguments of the functions used \( R \) 
* **Impossibility to reproduce the results of the computational analysis** 
  * IDE : stable version of the language different according to the OS \(Rstudio\)
  * Package versions

### Are there any solutions?

Fortunately, there are solutions and FAIR\_Bioinfo is here to offer you one! To try to solve its reproducibility problems, we will use development solutions.

![Some tools discussed in the FAIR\_Bioinfo](.gitbook/assets/image%20%28129%29.png)

## FAIR principles

We propose to use the FAIR data principles to make an analysis protocol reproducible and always obtain the same results from the same data. 

### FAIR data

FAIR data are data which meet standards of findability, accessibility, interoperability, and reusability \([Wilkinson et al. , Nature, 2016](https://www.nature.com/articles/sdata201618)\). In summary :

* **Findable** : the data must be easy to find by everyone
* **Accessible** : easily recoverable
* **Interoperable** : content in standard format with metadata
* **Reusable** : clear description and standard format for easy data use

![FAIR data principles](.gitbook/assets/image%20%2848%29.png)

### Why FAIR\_bioinfo?

FAIR data concerns best practices to manage data and there is many documentations about this. Here we argue that the processes should also be "FAIR". We "divert" FAIR principles from data to processes as follows:

* **Findable:** The tools used are references in their field and it is easy to find the analysis protocol \(Github pages\)
* **Accessible:** all ressources are available \(code stand in Github and Dockerhub\)  , based on open source tools \(conda\)
* **Interoperable:** The different tools will communicate with each other, cooperation of tools \(snakemake, docker\) both locally and on servers \(cloud or cluster\)
* **Reutilisable / Reproducible:** The protocol can be simply replayed \(snakemake\) in the same way \(Rmarkdown\) in a virtual environment \(docker\)  . Its execution replays the entire analysis identically

To make scripts and protocols in Bioinformatics more reproducible, we base our approach on the principles of FAIR. Thus, we follow the following principles :

### Our creed : So FAIR !

In the end, our creed is SO FAIR. From raw FAIR data, we will apply scripts and protocols that follow the FAIR\_Bioinfo principles. The objective is to obtain FAIR-processed data. 

![Our creed : So FAIR !](.gitbook/assets/image%20%28195%29.png)

## On the training program

### Our solution o**verview**

If you are reading this, you are trying to answer the following question: 

**What are the solutions to be more reproducible in Bioinformatics?**

If you are reading this, you are trying to answer the following question: What are the solutions to be more reproducible in Bioinformatics?

![](.gitbook/assets/image%20%2822%29.png)

{% hint style="info" %}
For pedagogical reasons, the different tools will not necessarily be presented in the same order. However, these 7 steps are followed in this order when you carry out an end-to-end project**.**
{% endhint %}

### Schedule

The document is divided into sessions, each one bringing an additional level of reproducibility to the global analyse. We have chosen a classical bioinformatics analysis as an example of a concrete case around which to develop those levels of reproducibility. In bioinformatics, data is often processed in several successive steps, we call this a workflow \(WF\). The example analysis will be only slightly detailed as it is not the main objective of this document and could be replaced by any other type of analysis. The analysis chosen consists of a classic study to identify genes differentially expressed between two conditions based on high-throughput expression data, "RNA-seq" data.

Here is the schedule of the 8 sessions presented with the objectives of acquiring skills, concerning on the one hand the FAIR principles \(FAIR\) and on the other hand the application to the analysis workflow \(WF\) :

* **Session 1: This is not magic**
  * FAIR: open a terminal, retrieve data \(wget, ascp, md5sum\)
  * WF: inputs
* **Session 2: The code memory**
  * FAIR: initiation to Git & Github
  * WF: save the script to get inputs
* **Session 3: Play with analysis tools**
  * FAIR: tools installation \(conda\)
  * WF: from the fastq file to the table of accounts \(FastQC, bowtie2, samtools, HTseq-counts\)
* **Session 4: A trip to the sea**
  * FAIR: control your computing environment \(Docker\)
  * WF: dockerfile
* **Session 5: I've got the power!**
  * FAIR: calculate elsewhere, parallel calculation without management \(snakemake\)
  * WF: snakefile
* **Session 6: LoveR**
  * FAIR: valuation by adding dynamism \(Shiny\), presentation of some powerful R packages
  * WF: import a count table, display it, and connect it to a graph 
* **Session 7: Valuation and sharing with notebooks**
  * FAIR: valuation \(Jupyter\) sharing \(Binder\)
  * WF: Rmarkdown to create the notebook of the shiny application
* **Session 8: Expose your project**
  * FAIR: Projet end \(Githubpages, License, Release, DOI\)
  * WF: your own data and project

We conducted this course in 8 sessions of one and a half hours each, with one chapter presented per session. The course has been built in such a way as to be able to use all the lines of code presented but, depending on the ease of the audience in unix commands \(and sometimes connection times\), it is then preferable to count 2h30 for each session. The auditors were fellow biologists or bioinformaticians from our research institute. 

At the end of each session, an exercise is proposed to replay alone the concepts that have been discussed. We have also opened a communication channel \(slack\) to answer questions or problems between courses. 

All the resources required for the course, both data and software, are downloaded as needed.

