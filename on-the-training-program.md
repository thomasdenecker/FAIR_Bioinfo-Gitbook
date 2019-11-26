# On the training program

## Our solution o**verview**

If you are reading this, you are trying to answer the following question: 

**What are the solutions to be more reproducible in Bioinformatics?**

If you are reading this, you are trying to answer the following question: What are the solutions to be more reproducible in Bioinformatics?

![](.gitbook/assets/7-steps%20%281%29.png)

{% hint style="info" %}
For pedagogical reasons, the different tools will not necessarily be presented in the same order. However, these 7 steps are followed in this order when you carry out an end-to-end project**.**
{% endhint %}

## Schedule

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

