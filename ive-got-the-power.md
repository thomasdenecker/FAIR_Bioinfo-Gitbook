---
description: >-
  We present several concepts to save time, in particular the parallelization of
  the commands and the use of a computing cluster.
---

# I've got the power !

Point on our reproducibility and speed:

* [x] automatic scripting
* [x] use of a cloud with configurable power

What more can we do?

* [ ] parallelize
* [ ] work on a computing cluster

## Parallelization

In terms on calculation time and even on a cloud, our analysis wokrflow takes a long time... What to do about it?

1. Improve algorithms? Ok but are we ready to optimize Bowtie2? ... hem ... no...
2. Increase computing power? How can this power be harnessed?

How to increase our computing power?

1. Change computer means spend money
2. Increase the power of our VM:
   1. hard disk: 20 Go --&gt; 160 Go
   2. RAM: 2 Go --&gt; 500 Go
   3. CPU: 1 --&gt; 48

### Zoom on CPU

![50 years of CPU evolution: 1 core - 1971, 10 cores in 2019 \(MIPS: Million Instructions Per Second\)](.gitbook/assets/image%20%28216%29.png)

The CPU \(Central Processing Unit\) also nammed processor is the component that executes instructions. A processor is composed of one or more cores: 1 core runs 1 instruction, n cores run n instructions in parallel. 

![A quadricore motherborad](.gitbook/assets/image%20%28132%29.png)

In our case, we have many instructions, we can use many cores!

![Comparison of the linear execution of a workflow that can be parallelized on programs 2 and 3.](.gitbook/assets/image%20%28209%29.png)

### How to parallelize?

Several possible solutions: Snakemake, Nextflow, ... We present you Snakemake solution.

Snakemake is a python project so the commands are first writing in Python programming language. But Snakemake accepts other programming languages like bash and R. 

Why Snakemake?

* It is a workflow system
* Its code is human-readable
* It comes with an automatic resource management
* It will make us more reproducible!

## Parallezise with Snakemake

### Install Snakemake

`conda install -c bioconda snakemake`

### The Snakemake principle: its rule system

Definition of a rule:

* a name
* input files
* output files
* code to switch from input files to output files

![A Snakemake rule](.gitbook/assets/image%20%28184%29.png)

Snakemake must know the future so there is an order to present the rules in the snakemake file: the first rule specify the files we want at the end \(target/target\) and next, we write the rules to achieve this.

The Snakemake will search for its execution order of rule based on the input and output files of each rule.

![Snakemake execution order](.gitbook/assets/image%20%28115%29.png)

### Comparison of the workflow and the rules flux

An example of rule can be a step of our analysis workflow.  Here is an extract of our analysis workflow:

![Extract of our analysis workflow](.gitbook/assets/image%20%2846%29.png)

If we want to represent all the instructions for our 6 inputs to be treated we obtain this picture:

![Data flow of our analysis workflow \(F: FastQC, B: Bowtie2, S: Samtools, H: HTSeq-count, C: count table\)](.gitbook/assets/image%20%281%29.png)

Snakemake provides an equivalent view but automatically created from the rule file:

![Data flow provided by Snakemake](.gitbook/assets/image%20%28149%29.png)

The Snakemake commad to obtain this view is:

```text
snakemake --forceall --dag | dot -Tsvg > dag_input.svg
```

### A small Snakefile example

A snakefile is the file that contains all the rules that Snakemake will load and execute. Its default name is `Snakefile` \(with the first letter in uppercase\). 

Here is a small Snakefile example with two rules: a rule to indicate the final files we want \(the objective here is to compress files\): 

```text
rule targets:
    input:
        "data/toto.fq.gz"
        "data/titi.fq.gz"
```

and a second rule to indicate how to get them:

```text
rule gzip:
    input:
        "{base}"
    output:
        "{base}.gz"
    shell:
        "gzip {input}"
```

Here `{base}` stores the names of the files where we are located.

That's all!

### Creating the Snakefile of our project

We will now create the `Snakefile` of our project.

1- We first declare two variables whose content specifies the working directory. And we go into the working directory with the snakemake command `workdir:`:

```text
BASE_DIR = "/home/rstudio"
WDIR = BASE_DIR + "/Project"
workdir: WDIR
```

2- We also declare variables that contain the access to the genome sequence file and its annotation file:

```text
GFF   = WDIR + "/annotations/O.tauri_annotation.gff"
GENOME = WDIR + "/genome/O.tauri_genome.fna"
```

3- We retrieve the list of files to be processed:

```text
SAMPLES, = glob_wildcards("./samples/{smp}.fastq.gz")
NB_SAMPLES = len(SAMPLES)
```

where `glob_wildcards(...)` is a function that deducts the missing part of the file names from the files present in the system and where `smp` stands for the variable part of the file name. Look at the braces around `smp`denoting we want to access to its content.

{% hint style="danger" %}
Do not forget the comma after "SAMPLES", it is the format of list in python
{% endhint %}

4- Declaration of the files we want at the end of the analysis workflow. As they constitute the targets Snakemake will look for, we declare them as the input of the final rule. Note that if a file is not declared here, the associated rules will not be executed.

```text
rule final:
    input:
        expand("fastqc/{smp}/{smp}_fastqc.zip", smp=SAMPLES),
        expand("htseq/count_{smp}.txt", smp=SAMPLES),
        expand("graphics/graphics-{smp}.pdf", smp=SAMPLES),
        expand("countTable.txt")
```

where `expand(...)` is a function that allows you to use a variable. Here the variable we want is file name. Look how `smp` becomes a already know variable, a "global" variable.

5- We now write the first step of our analysis workflow, the quality control of the reads using fastqc. We define:

* a name for the rule: fastqc
* an input: the list of files that have been uploaded for the analysis
* an output: the list of output files
* a message : the message that will be displayed during the analysis
* and shell: the command launched in the terminal. 

```text
rule fastqc:
        input:  "samples/{smp}.fastq.gz"
        output: "fastqc/{smp}/{smp}_fastqc.zip"
        message: """--- Quality check of raw data with Fastqc."""
        shell: """
        fastqc {input} --outdir  fastqc/{wildcards.smp}
        """
```

To use a variable in a shell: `wildcards.variableName`

6- The next step is the mapping step we realise with bowtie2. Here is the rule for the step of building the genome index for bowtie2 step:

```text
rule bowtie2Build:
    input: GENOME
    params:
        basename= "reference/reference"
    output:
        output1="reference/reference.1.bt2",
        output2="reference/reference.2.bt2",
        output3="reference/reference.3.bt2",
        output4="reference/reference.4.bt2",
        outputrev1="reference/reference.rev.1.bt2",
        outputrev2="reference/reference.rev.2.bt2"
    message: """--- Indexation of the reference genome."""
    shell: "bowtie2-build {input} {params.basename}"
```

The `params:` item allows us to specifies the parameters to be used in the shell \(here the writing area\) command line.

7- The mapping step with bowtie2:

```text
rule bowtie2:
    input:
        file = "samples/{smp}.fastq.gz",
         bt2 = "reference/reference.rev.2.bt2"
    params:
        index = 'reference/reference'
    output:
        sam = "bowtie2/bowtie-{smp}.sam",
        out = "bowtie2/bowtie-{smp}.out"
    message: """--- Alignment of reads with the reference genome."""
    shell: 'bowtie2 -x {params.index} -U {input.file} -S {output.sam} > {output.out}'
```

As there are several inputs, it is necessary to specify the names \(here `file` or `bt2`\) in the shell \(format : `{input.nameImput}`\). It is the same for the output \(`sam` and `out` files\).

8- The samtools view step:

```text
rule samtoolsView:
    input:
        "bowtie2/bowtie-{smp}.sam"
    output:
        "samtools/bowtie-{smp}.bam"
    message: """--- Binary conversion of aligned reads."""
    shell: """
    samtools view -b {input} > {output}
    """
```

9- The samtools sort and index step:

```text
rule samtoolsSortIndex:
    input:
        "samtools/bowtie-{smp}.bam"
    output:
        "samtools/bowtie-{smp}.sorted.bam"
    message: """--- Sorting and Indexingof aligned reads."""
    shell: """
    samtools sort {input} -o {output}
    samtools index {output}
    """
```

10- The HTseq-count step:

```text
rule htseqCount:
    input:
        "samtools/bowtie-{smp}.sorted.bam"
    output:
        "htseq/count_{smp}.txt"
    params:
        GFF
    message: """--- Count."""
    shell: """
    htseq-count --stranded=no --type='gene' --idattr='ID' --order=name --format=bam {input} {params} > {output}
    """
```

It is always the same syntax ...

11- Creation of graphics in R language:

```text
rule graphics:
    input:
        "htseq/count_{smp}.txt"
    output:
        "graphics/graphics-{smp}.pdf"
    params:
        GFF
    message: "--- Histogram"
    shell: """
    Rscript ../R-code/graphics.R {input}
    """
```

It is possible to call all the commands available in the shell as the R command \(done with `Rscript`\).

Et voilà !

### How to launch a snakefile?

`snakemake`

### How to integrate this snakefile into our code?

Just after the download of the genome sequence, we add the snakemake invocation and the code R dealing with the count table:

```text
echo "=============================================================="
echo "Snakemake"
echo "=============================================================="

snakemake

echo "=============================================================="
echo "Create unique count table"
echo "=============================================================="

Rscript R-code/countTable.R
```

### How to start the whole project? 

Almost as it was before! 

It is just necessary to specify to the docker that it can take up space in CPU \(here 6\) and memory \(here 24 GB\):

```text
$ sudo docker run --rm -d -p 8888:8888 --cpus="6" -m=24g --name fair_bioinfo -v ${PWD}:/home/rstudio tdenecker/fair_bioinfo
```

Then run our script:

```text
$ sudo docker exec -it fair_bioinfo bash FAIR_script.sh
```

And wait... 

### Gain more time with aspera

Here we propose you to replace the wget command we used to download the input data files by the aspera command. Aspera is faster than wget but as the recovered file can be corrupted, we have to check it! And since Aspera does not work on all servers, it should be coupled with another method.

We modify the part of data download \(see part "Automation of data downloading: the loop concept", in the Chater "Play with analysis tools"\) to use Aspera:

```text
for j in $(tail -n +2 ../../conditions.txt)
do

    access=$( echo "$j" |cut -f7 )
    id=$( echo "$j" |cut -f1 )

    echo "--------------------------------------------------------------"
    echo ${id}
    echo "--------------------------------------------------------------"

    ascp -QT --file-checksum=md5 --file-manifest=text --file-manifest-path=. -l 300m -P33001 -i /home/miniconda3/etc/asperaweb_id_dsa.openssh ${access} .

    md5_local="$(md5sum $id.fastq.gz | cut -d' ' -f1)"
    echo $md5_local

    if grep -q $md5_local *.txt
    then
        echo "Done"
    else
        rm $id.fastq.gz
        access=$( echo "$j" |cut -f6 )
        wget ${access} # wget method
    fi
    rm *.txt
done
```

### Retrieval of the results 

We have run the analysis on a virtual machine into the cloud. Now we have to retrieve the results onto our local machine:

![retrieve the results of the cloud from the local machine](.gitbook/assets/image%20%285%29.png)

#### In a terminal

We use `scp`, a "**s**ecure **c**o**p**y" with ssh. The structure of the `scp` command is `scp acces:localizationOfRemoteFile localizationOnLocalSpace`

Concrete example:

```text
$ scp ubuntu@134.212.213.90:/mnt/FAIR_Bioinfo/Project/countTable.txt countTable.txt
```

#### With Filezilla

Filezilla is an interface to retrieve files from a server.

![](.gitbook/assets/image%20%2881%29.png)

Here is the two-step progession:

1- Fill in the key at Filezilla \(Edit &gt; Settings &gt; SFTP\)

and add your **private** key file \(the file we created to connect to the cloud\), `./ssh/id_rsa` and not the public key.

2. Connect with Filezilla:

![Interface for Filezilla connexion](.gitbook/assets/image%20%2889%29.png)

Fill the boxes with: **Hôte** : 134.212.213.90 \(following the ssh connexion\), **Identifiant** : ubuntu, **Mot de passe** : \(nothing, the reconnaissance will be done thank to the ssh-keys couple\), **Port** : 22, and click on the button "**Connexion rapide**".

The following message will be "Host Key Unknown", answer "ok". You will have access to the clickable server architecture, just drag and drop the files you want to retrieve. 

### Kill the VM

Now, we don't need of the virtual machine, we can kill it and free the cloud resources.

### Finally, have we bought ourselves some time with the paralelization?

|  | Machine 1 | Machine2 |
| :--- | :--- | :--- |
| Machine configuration | 1 CPU, 2 Go RAM | 8 CPU, 32 Go RAM |
| Duration of the analysis | 1 hour and 23 minutes | 28 minutes |

Yes we have: almost an hour for the same analysis but with parallel data processing using snakemake!

## Save even more time with a cluster

A cluster is a grouping of several independent computers \(nodes\) to allow global management and exceed the limitations of a unique computer.

Here are some advantages:

* Increased availability \(once the work is completed, resources are released\)
* Optimization of work distribution
* Simplified resource management \(processor, RAM, hard drives, network bandwidth\)

### Comparison of a cloud and a cluster:

* Cloud: accumulation of power to form a cloud \(server\)
* Cluster: connection of several machines optimized to form a single machine

|  | Cloud | Cluster |
| :--- | :--- | :--- |
| Localization | Scattered | Local |
| Materials | Different | Identical |
| Configuration | Different | Identical |

### How to launch calculations on the cluster?

As for using a cloud, you need an account on a cluster resource. Once you have your account, using a cluster requires relatively similar steps than using a cloud. The two mains differencies reside in using **Singularity** and the need of a small bash file to launch our job on the cluster and depending on the **scheduler**. 

#### Singularity

Singularity ****which is also an image-based container system, is used to avoid the need of root rights and will allow you to set the environment. It can exploit docker images.

![](.gitbook/assets/image%20%28147%29.png)

#### File to launch on a cluster

On a cluster, jobs are launched on a batch mode, _ie_. they are placed in a queue composed of all the jobs launched to wait their turn. 

We present you how to write the file to launch jobs on a cluster for two kinds of scheduler, slurm \(Simple Linux Utility for Resource Management\) and PBS \(Portable Bash System\). If the scheduler of your cluster resource is not one of these two, you have to adapt the functions to your scheduler. Their syntax are a little different but schedulers share the same philosophy. 

![Logo of the slurm scheduler](.gitbook/assets/image%20%28154%29.png)

The roles of a scheduler concern:

* Allocation of resources for the necessary time
* Follow-up of tasks \(jobs\)
* Setting up a queue

The file to write may contains both the details of the processing to carry out \(name and version of the application, input and output, etc\) and the directives for the computer resources needed \(number of CPUs, amount of RAM, etc\).

#### Writing of the slurm file:

1. File type bash, it is the first line of the file: `#!/bin/bash`
2. Parameters: the parameters for the slurm scheduler start with `#SBATCH`

   * redirection file of the standard output renamed with the job ID:  `#SBATCH -o slurm.%N.%N.%j.out`
   *  redirection of the standard error file renamed with the job ID:  `#SBATCH -e slurm.%N.%N.%j.err`
   *  choice of calculation nodes/queues \(ex. "fast" =&gt; the calculation will be stopped after n hours ; the names depend on each resource\):  `#SBATCH --partition fast`
   * number of reserved CPUs \(ex. 6 CPUs\): `#SBATCH --cpus-per-task 6`
   * RAM reservation \(ex. 24 Go\): `#SBATCH --mem 24GB`



   3. Launching the docker and `FAIR_script.sh` script

   ```text
   $ singularity exec -B $PWD:/home/rstudio fair_bioinfo.simg bash ./FAIR_script.sh
   ```

The total slurm file, nammed `FAIR_Bioinfo.slurm`:

```text
#!/bin/bash
# parameters for the slurm scheduler start with "#SBATCH" :
#SBATCH -o slurm.%N.%j.out  # redirection file of the standard output renamed with the job ID
#SBATCH -e slurm.%N.%j.err  # redirection of the standard error file renamed with the job ID
#SBATCH --partition fast    # choice of calculation nodes/queues (ex. "fast" => the calculation will be stopped after n hours)
#SBATCH --cpus-per-task 8   # number of reserved CPUs
#SBATCH --mem 30GB          # RAM reservation

# launching the docker and the FAIR_script.sh script
singularity exec -B $PWD:/home/rstudio fair_bioinfo.sif bash ./FAIR_script.sh
```

In case of in case the cluster's scheduler is not slurm but PBS, here is how to modify the job launch script, named for example `FAIR_Bioinfo.pbs` :

```text
#!/bin/sh

#PBS -q lowprio # choice of calculation nodes/queues, the name, lowprio, depends of the cluster resource
#PBS -l select=1:ncpus=6

cd /home/thomas.denecker/FAIR_Bioinfo/
singularity exec -B /home/thomas.denecker/FAIR_Bioinfo/:/home/rstudio fair_bioinfo.simg bash ./FAIR_script.sh
```

#### Steps to launch the analysis workflow on a cluster

1. Connect to the cluster through ssh \(open a terminal on your local machine\): `ssh YourUserID@YourClusterIPAccess`
2. Recovery of the project on Github: `git clone https://github.com/thomasdeneker/FAIR_Bioinfo.git`  
3. Moving around the project repository: `cd FAIR_Bioinfo`
4. Recovery of the container image with singularity: `singularity pull docker://tdenecker/fair_bioinfo`
5. Launching the workflow: `sbatch fair_bioinfo.slurm` or `qsub fair_bioinfo.qsub`
6. File recovery from cluster to local \(from a terminal on your local machine\): `scp YourUserID@YourClusterIPAccess:FAIR_Bioinfo/countTable.txt countTable.txt`

## What about the calculation time?

|  | machine 1 on cloud | machine 2 on cloud | cluster |
| :--- | :--- | :--- | :--- |
| Machine configuration | 1 CPU, 2 Go RAM | 8 CPUs, 32 Go RAM | 6 CPUs, 32Go RAM |
| Duration of the analysis | 1 hour and 23 minutes | 28 minutes | 22 minutes |

## Conclusion

In this Chapter, we gain in reproducibility and speed. We realize the:

* Implementation of an automatic workflow system
* Use of the parallelization power
* Increasing the performance of a dock worker
* Use of a computing cluster

Parallelization associated to the use of a cluster are two solutions that decrease the analysis time and may be seen as:

![](.gitbook/assets/image%20%2840%29.png)

We are confident that: **FAIR raw data** associated to **"FAIR\_bioinfo" scripts/protocols** give **FAIR processed data.**

## It's up to you!

Run the analysis on the cluster or on a VM in parallel

