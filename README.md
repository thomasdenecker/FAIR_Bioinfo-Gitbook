# Introduction

A recent study published in Nature has shown that nearly 70% of experiments in Biology are not reproducible. It is therefore essential to put in place good practices to ensure data integrity and reproducibility of analytical results. Concerning data, the FAIR-data principles are increasingly being used. These same principles can be used in the analysis department to guarantee identical results from the same data set and over time ... The objective of this document is to propose a range of functionalities to make a complete bioinformatics analysis reproducible. The purpose of the example presented is to select genes that do not behave in the same way between two experimental conditions. The features presented are not dependent on this example. In short, we retrieve the data from public databases \(ENA/SRA\), we perform a reproducible analysis with a workflow system \(snakemake\) in a virtual environment \(docker\) whose entire code, versioned \(git\), is available in open source \(Github and dockerhub\). The visualization of the results is dynamic \(shiny app\) and a report \(Rmarkdown\) in pdf or html is available. It groups the results of the analysis and details all the parameters chosen by the user.

{% hint style="info" %}
This course is Open Source \(under the CeCILL 2.1 license\). You can find it on the github of the FAIR\_Bioinfo project: [here](https://github.com/thomasdenecker/FAIR_Bioinfo/blob/master/LICENCE).
{% endhint %}

## Objectives

The purpose of this document is to provide a concrete example of the implementation of a tools panel to increase reproducibility in science. The tools we choose to manage the reproducibility have others equivalents but the solution proposed is based on our own daily research experiences in bioinformatics. We also choose to start from a very low level of computer skills so that all this documentation can be followed by our biologist colleagues. 

{% hint style="info" %}
This training was initially designed for a French audience. Indeed, there are few resources on reproducibility in the language of Molière. You can find all the slides presented on the FAIR\_Bioinfo Github repository: [https://github.com/thomasdenecker/FAIR\_Bioinfo](https://github.com/thomasdenecker/FAIR_Bioinfo)
{% endhint %}

## Why FAIR\_bioinfo?

FAIR data concerns best practices to manage data and there is many documentations about this. Here we argue that the processes should also be "FAIR". We "divert" FAIR principles from data to processes as follows:

* **Findable:** The tools used are references in their field
* **Accessible:** Codes, slides, docker are online
* **Interoperable:** The different tools will communicate with each other
* **Reutilisable / Reproductible:** The protocol is saved in a file whose execution replays the entire analysis identically

## Usage of a computer tool <a id="usage-of-a-computer-tool"></a>

During this training, we will use a number of tools. To use a computer tool, there is three steps to follow: i\) install it and configure the environment, ii\) use it, iii\) develop it. In this document, these 3 steps will be discussed for several of the tools presented because the development step is the one that allows maximum autonomy.

