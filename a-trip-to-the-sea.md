---
description: >-
  We add a step to the gain in reproducibility by creating a docker container
  and testing it in a cloud.
---

# A trip to the sea

## Point on our reproducibility status

Up to now, we have increase the status of the our analysis considering this reproducibility items:

* [x] Code: Git and Github 
* [x] Tools used: Conda
* [x] Installation: Installation script, conda
* [x] Analysis: Automatic analysis script

But, what about the reproducibility of the **operating system**?

> **Dependability Benchmarking for Computer Systems** De K. Kanoun, L. Spainhower

In this chapter, we will set the operating system.

## How to set the operating system?

There is at least two solutions that allow us to have several operating systems on the same physical machine: Virtualization or Containerization.

![Differencies between Vitualization \(left\) and Containerization \(rigth\)](.gitbook/assets/image%20%28208%29.png)

This schema show us that Containerization is lighter than Virtualization. As Containerization is lighter, it is also faster to install and therefore easier to share and more portable. 

We will used the Containerization with the **docker** solution.

## Containerization with Docker

[**Docker**](https://docs.docker.com/) is a tool for create and/or use containers.

![](.gitbook/assets/image%20%2859%29.png)

To use Docker, our system must be up to date, we must have "administrator" rights and for this reason, docker is not often recommended by academic institution that prefer the [**singularity**](https://sylabs.io/singularity/) solution \(but at the writing date, the "singularity" solution has just undergone a major update that is not compatible with the reproducibility criteria we are looking for\). The administrator rights come with the user "root". To run a command as "root" we use the `sudo` command. We will use this `sudo` command just after to test the installation of docker.

Like any computer tool, we describe three steps to use it: first, the installation step, and then, how to create a container and how to use containers already created. Note that it is possible to use docker without having to create your own container \(when other people will have created a container for you\).

### Install Docker

To install Docker, follow these links according to your system:

* [windows](https://docs.docker.com/docker-for-windows/install/)
* [MacOS](https://docs.docker.com/docker-for-mac/install/)
* [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

It is very simple for macOS and Windows \(in graphical interface\), and a little more complex for Ubuntu \(if it is not already installed\). But the documentation is very clear. 

Windows and MacOS installations require the [creation of an account](https://hub.docker.com/signup) on the container sharing site, DockerHub. Creating an account will be necessary anyway to share your own containers.

![DockerHub account creation page](.gitbook/assets/image%20%2877%29.png)

Test the installation with your first usage of a container, the "hello-world" container : `sudo docker run hello-world`

You have to do the installation again if you don't have that:

```text
$ sudo docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

Now, docker must still be active \(it is called a "daemon"\). This can be verified by the presence of the docker logo in the tasks menu \(on Windows and MacOS\) or by launching `docker --version` \(on linux\).

### Some definitions

#### Image

An image is a set of functions that allow an application to run. It is fixed \(not modifiable\). It can be stored online and shared \(DockerHub\)

![Docker images are like machines of an computer room](.gitbook/assets/image%20%2874%29.png)

#### Container

A container is an image that is active. It is modifiable. 

![A docker container is like a computer on in an computer room.](.gitbook/assets/image%20%2896%29.png)

An image can be used as a model for several containers that will be independent of each other:

![](.gitbook/assets/image%20%2853%29.png)

The independence is interesting in the context of database queries for example.

### How to create a docker image?

To create an image, we will using a file to drive the creation nammed a "dockerfile".

#### The dockerfile

The architecture of a dockerfile is composed of some key words, writting in capital letters:

```text
FROM XXX

RUN XXX
```

`FROM` specify the basic image used \(Ubuntu for example\) and `RUN` launch a command \(update the Ubuntu version\). This simple example will give us a docker image with an updated ubuntu 18.04:

```text
FROM ubuntu:18.04

RUN apt-get update
```

#### The image creation

And now, to build the image from the dockerfile, we use the `docker build` command \(we add a name with the `--tag` option\):

```text
$ docker build --tag=XXX .
```

To avoid parasitizing the image, put the dockerfile alone in a folder.

### Create the dockerfile of our project

We now present you the creation of the docker FAIR\_Bioinfo. It is more complex than the previous example, but it is ready to carry out the entire project.

**1- entry point**

`FROM`, the entry point. We choose the rocker project \(binder oriented\). Rocker is a set of R dockers with more or less features. For the rest of our project, we want a Rstudio server and a Jupyter application.

```text
FROM rocker/binder
```

**2- change of User and update of packages**

```text
## USER
USER root

## Update
RUN apt-get update
```

**3- addition of an environment variable and change of working folder**

```text
ENV HOME /home
WORKDIR ${HOME}
```

**4- conda installation and use**

```text
## Install Conda
RUN apt-get install -y wget bzip2
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
RUN bash Miniconda3-latest-Linux-x86_64.sh -b
RUN rm Miniconda3-latest-Linux-x86_64.sh

ENV PATH /home/miniconda3/bin:$PATH

## Update
RUN conda update conda
RUN conda update --all

## Add chanel
RUN conda config --add channels conda-forge
RUN conda config --add channels bioconda
```

**5- Snakemake install**

```text
RUN conda install -c bioconda -c conda-forge snakemake
```

**6- Installation of our workflow tools**

```text
RUN conda install -y fastqc bowtie2 htseq samtools
```

**7- Installation of aspera**

```text
RUN conda install -c hcc aspera-cli
```

**8- Installation of R packages**

```text
# Install Bioconductor package
RUN Rscript -e 'if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager") ; BiocManager::install("DESeq2", version = "3.8")'
RUN Rscript -e 'if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager") ; BiocManager::install("edgeR", version = "3.8")'
RUN Rscript -e 'if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager") ; BiocManager::install("genefilter", version = "3.8")'

## Install CRAN package
RUN Rscript -e "install.packages('devtools', repos='https://cran.rstudio.com/', dependencies = TRUE)" \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds
RUN Rscript -e "install.packages(c('shinydashboard','DT', 'FactoMineR', 'corrplot','plotly'), repos='https://cran.rstudio.com/', dependencies = TRUE)" \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds
RUN Rscript -e "install.packages(c('shinyWidgets','colourpicker'), repos='https://cran.rstudio.com/', dependencies = TRUE)" \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds
RUN Rscript -e "install.packages(c('shinycssloaders'), repos='https://cran.rstudio.com/', dependencies = TRUE)" \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds

## Install SARTools
RUN Rscript -e 'library(devtools) ; install_github("PF2-pasteur-fr/SARTools",  install_github("PF2-pasteur-fr/SARTools", build_vignettes=TRUE)'
```

**9- installation of fastq-dump** 

```text
# Install fastq dump

RUN wget --quiet "https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.9.2/sratoolkit.2.9.2-ubuntu64.tar.gz"
RUN tar -xzf sratoolkit.2.9.2-ubuntu64.tar.gz
RUN rm sratoolkit.2.9.2-ubuntu64.tar.gz
ENV PATH /home/sratoolkit.2.9.2-ubuntu64/bin:$PATH
```

**10- change of user and working folder**

```text
USER rstudio
ENV HOME /home/rstudio
WORKDIR ${HOME}
```

**11- command launched when the docker starts up**

```text
CMD jupyter notebook --ip 0.0.0.0 --NotebookApp.token='' --NotebookApp.password=''
```

Not mandatory here, but greatly simplifies use thanks to the elimination of the password.

**Et voilà !**

All that remains is to build the image:

```text
$ docker build --tag=fair_bioinfo .
```

And what... ? 

**12- Save this dockerfile on GitHub!**

### How to share an image?

To share our image, we make it awailable in depots such as **DockerHub**

DokerHub uses the same principles as Github:

* share an image \(push\)
* recover an image \(pull\)

Reminder: you must have an account on DockerHub and have an active docker. We log in with our ID and password:

```text
docker login
Authenticating with existing credentials...
Login Succeeded
```

Prepare name: _ID\_dockerhub/IMAGE\_NAME_

```text
$ docker tag fair_bioinfo tdenecker/fair_bioinfo 
```

Send the image to DockerHub site

```text
$ docker push tdenecker/fair_bioinfo 
```

If we compare the volume of the dockerfile and the image, the dockerfile on GitHub account for 2.46 Kb while the image on DekerHub accont for about 8 Go.

#### **And on DockerHub?**

It's look like:

![](https://s3.amazonaws.com/media-p.slid.es/uploads/991142/images/5941315/pasted-from-clipboard.png)

### How to pick up a docker?

```text
$ docker pull tdenecker/fair_bioinfo
```

And the image will be available locally.

### Useful commands

#### Information on Docker =&gt; docker version 

```text
$ docker version
Client: Docker Engine - Community
 Version:           18.09.2
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        6247962
 Built:             Sun Feb 10 04:12:31 2019
 OS/Arch:           windows/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.2
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.6
  Git commit:       6247962
  Built:            Sun Feb 10 04:13:06 2019
  OS/Arch:          linux/amd64
  Experimental:     true
```

#### Locally available images =&gt; docker images

```text
$ docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
bgruening/galaxy-stable        latest              d2f45c76b85c        4 weeks ago         3.78GB
rocker/tidyverse               latest              1379b4cd1dfa        7 weeks ago         1.86GB
tdenecker/fair_bioinfo         latest              a6c75264ae49        2 months ago        8.83GB
jupyter/datascience-notebook   latest              18c805bb3afb        2 months ago        6.32GB
rocker/verse                   latest              ca3c5d105cb2        3 months ago        2.9GB
rocker/binder                  latest              a96cd7a02ed8        3 months ago        4.06GB
tdenecker/bpeaks_docker        latest              796b1bc0fc2f        8 months ago        2.3GB
tdenecker/bpeaks_db            latest              91c3c0c2f18e        8 months ago        236MB
hello-world                    latest              f2a91732366c        16 months ag
```

### How to launch a docker image? =&gt; docker run

We have already launch a docker image! with the "hello-word" container:

```text
$ docker run hello-world
```

### Launch the image of our project

```text
$ docker run --rm -d -p 8888:8888 --name fair_bioinfo -v WORKING/PATH:/home/rstudio tdenecker/fair_bioinfo
```

Options are as follow:

* `--rm`: cleaning of system files most uesd after the end of the docker
* `-d`: detached mode \(the terminal is not blocked\)
* `-p 8888:8888` : port sharing
* `-name` fair\_bioinfo : name given to the container
* `-v WORKING/PATH:home/rstudio`: sharing a volume \(folder\). Here, I want the contents of the folder WORKING/PATH linked to the folder `/home/rstudio`of the docker.
* tdenecker/fair\_bioinfo : name of the image

Don't panic, there is documentation!

Here an example with the [**Rocker** project](https://www.rocker-project.org/):

![](.gitbook/assets/image%20%28126%29.png)

Another useful docker image is the **galaxy** project : just with a docker run and a little of time, you may have a complete galaxy server on your laptop [bgruening/docker-galaxy-stable](https://github.com/bgruening/docker-galaxy-stable).

### Useful docker commands

#### How to list all the containers? \(active images\)  =&gt; docker ps

```text
$ docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                              NAMES
89ed873eb4da        tdenecker/fair_bioinfo   "/bin/sh -c 'jupyter…"   21 minute           Up 21 minutes       8787/tcp, 0.0.0.0:8888->8888/tcp   fair_bioinfo
```

#### How to go into a container?  `docker exec -it`

Sometimes we want to explore the content of a container. We can do that by executing the container with its iterative mode. In this mode, the container is waiting your `exit` command to close. In the next example, we want to go into the bash of our docker. Once the `docker exec -it` launched, we can see a "prompt", a new command invite which displays the user name we defined in the dockerfile, _ie._ "rstudio". 

```text
$ docker exec -it fair_bioinfo bash
rstudio@89ed873eb4da:~$
```

It is a bash like other so we can execute our analysis script or any other command. To quit this bash and the docker container :

```text
rstudio@89ed873eb4da:~$ exit
```

#### How to stop a container? `docker stop`

We will stop a running container using both its name or its ID. Those values are given by a `docker ps` command:

```text
 docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                              NAMES
89ed873eb4da        tdenecker/fair_bioinfo   "/bin/sh -c 'jupyter…"   30 minutes ago      Up 30 minutes       8787/tcp, 0.0.0.0:8888->8888/tcp   fair_bioinfo

$ docker stop fair_bioinfo 

## or 

$ docker stop 89ed873eb4da

$ docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                              NAMES
```

#### Data share between docker and local repository

When we wish to run a task, we often want to access to the results obtained with the input data. But as docker is also use to isolate a task from the rest of the world, we have to specify to docker the space it can access. We need to share a "volume" with the docker container. The option `-v` \(volume\) associates two volumes/repositories, one from our local architecture and one from the container architecture. 

The schema hereafter shows how using the `-v` option give access to the local repository : with the `-v` option, the `ls` command lists the local file `test.txt` what it does not do when the option is not used.

![Effet of a volumes association](.gitbook/assets/image%20%287%29.png)

It is  volumes association is a bidirectional sharing:

![](.gitbook/assets/image%20%28185%29.png)

The modifications done in the local side are visible into the docker container and the reverse is true, the modifications done inside the docker container are visible in the local repository.

Note that it needs of absolute paths.

#### How to suppress a container? `docker rm`

```text
$ docker rm -f ID_CONTAINER
```

#### How to suppress an image? `docker rmi`

```text
$ docker rmi -f ID_IMAGE
```

## Use a docker image on a cloud

If your personal laptop lacks of power and if you want to go speeder \(by adding power\), one solution is to exploit virtual machines offered by a cloud resource.

A cloud is an association of physical machines where users instantiate virtual machines. One physical machine can accept one or more virtual machine resulting as the global resources sharing. You have to configure the resources \(CPU number, RAM volume, Disk volume\) of the VM you want/need. It is often done by the way of a web interface.

If we have the opportunity to access on a cloud resource, we advise you to use it! 

### Some notions of resource consumption

An account on a shared resources comes with a usage quotas. For example, it may be free for 36,000 hours of CPU time per year per user.

A VM on consumes 24 hours a day, 7 days a week, and depending on its power, we may estimate that it will achive the quotas in:

* 1500 days for a VM with 1 CPU;
* 188 days for a VM with 8 CPUs;
* 47 days for a VM with 32 CPUs.

When the time is out, there is no more than VM.

{% hint style="info" %}
Perform the analysis, **retrieve the results** and turn off your VM!
{% endhint %}

We will next present you the access through the `ssh` \(**s**ecure **sh**ell\) protocol, a common way to access on remonte and shared resources like clouds.

The first thing to do is to follow the documentation of your resource center to get access. Probably you will have to ask for an account. One you have an account, you may configure it. One of the main configuration is the add of your public ssh-key. The next section explains what is a public ssh-key.

### A couple of ssh-keys

 A couple of ssh-keys is an encryption solution that avoid us to reminder multiple passwords because two machines will communicate and exchange their access keys together without us but the first time for a configuration step.

This secure protocol may be necessary when you request an account from a shared resource such as a cloud or a computer cluster. 

If you don't have yet an ssh-key couple \(on Linux it is stored into the `.ssh/`repository and the two files are nammed `id_rsa.pub` for the public key and `id_rsa` for the private key\). You may create one couple with the `ssh-keygen` software:

```text
$ ssh-keygen -t rsa -C "FAIR_BIOINFO"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/tdenecker/.ssh/id_rsa):
/home/tdenecker/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/tdenecker/.ssh/id_rsa.
Your public key has been saved in /home/tdenecker/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:5QwtwncvoLl1j0qnEahb5lT3YyqqPOQzsz+kZPBf4Dc FAIR_BIOINFO
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|     .   .       |
|      o = +      |
|  .   .* O .     |
|   o .+.S * .    |
|    =.o+E+ =     |
|   =.+=oo.o =    |
|   .B*o..= o .   |
|    =O+oo..      |
+----[SHA256]-----+id_rsa.pub
```

Your **private** key should **never be shared** \(but kept in the `.ssh` repository\), only the public key`id_ras.pub`can be shared. Here is an example of a public key, note that it is a one-line file:

```text
$ cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/GcB30kTCSEJkbdo7SpgW2qavEGmVJYy0JzdMmf8EOWz8++ShTb0S4uuuVMsr4uJmztKkTzcEbV6sNWL9dlPi1RtU6dtZ2OTcx1IrDCSY82MgrzPU3hbOlZT6FPcgUvk5M0oQJgAR12ngQRn6yOm6jYXXczPncrIYwcVkRuyepv+Bdbu8//OIAeyY1d469JDQeXEBLRoNp6UxaOfI10VHHeWQ7o3Rd09k0BM+GT7Hn21NGeA0BWYPmMD6YxORii1T5lzhwGnGqxY2CXFL87Y0Cg6V0lsyF/Ze60iBQc6ckNZJMBa8+YtCQquWIh4Fugi0oRvb444v1Usn6HIHsFDb FAIR_BIOINFO
```

During the steps of configuring your account on a cloud/cluster, you may need to copy and paste your public key. Check the one-line format of your copy.

The 2 keys will now be able to exchange between the machines and this secure protocol replaces a log requiring a password.

### Access to the virtual machine

When you have the access and configured it, to open a shell is done on your teminal by:  `ssh user@access` where `user` and `access` are provided by your resource center \(`ubuntu` and `134.212.213.90` in the example\):

```text
$ ssh ubuntu@134.212.213.90
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Mar 26 14:28:36 UTC 2019

  System load:  0.08               Processes:           86
  Usage of /:   13.0% of 19.21GB   Users logged in:     0
  Memory usage: 9%                 IP address for ens3: 10.10.2.28
  Swap usage:   0%


  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ubuntu@machine72ac0a94-37a4-441e-99fc-7005ce8c02e3:~$
```

 Now, you are inside a VM in the cloud!

### Running the fair\_bioinfo docker on the cloud

We can run the fair\_bionfo docker. The limitation of docker to have administrator rights \(user "root"\) is not valid on a VM because we are the "root" user of our VM and so, we can run a command as "root" with the `sudo` command:

```text
$sudo docker run --rm -d -p 8888:8888 --name fair_bioinfo -v ${PWD}:/home/rstudio tdenecker/fair_bioinfo
Unable to find image 'tdenecker/fair_bioinfo:latest' locally
latest: Pulling from tdenecker/fair_bioinfo
54f7e8ac135a: Pull complete
f3ecd3055765: Pull complete
9692014a7b7c: Pull complete
53960df9fec8: Pull complete
7131f89eba02: Pull complete
...
a9ea872cde40: Pull complete
d62a8a9d04d0: Pull complete
cddaad898ff9: Pull complete
c3d423a714fb: Pull complete
Digest: sha256:efc2a01095a0b79e62395fb5573a532b11eefe28341f97f53bab9b94dd40155a
Status: Downloaded newer image for tdenecker/fair_bioinfo:latest
aaf6cce43b366c248677ffb7dc00333c67d578547049d5f05ed265da86ec0428

$ sudo docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
tdenecker/fair_bioinfo   latest              a6c75264ae49        2 months ago        8.83GB
```

With the upwards `sudo docker images` command, we see that the fair\_bioinfo docker is running.

Stop it by: `sudo docker stop fair_bioinfo`

To test the application, clone the FAIR\_Bioinfo repository on GitHub, go into the repository, and create a container with opening a communication port \(if the command don't run for you, you may ask your resource center the number for an open port you could use\):

```text
$ git clone https://github.com/thomasdenecker/FAIR_Bioinfo
$ cd FAIR_Bioinfo

# Create the container
# 80 is one open port on the cloud
$ sudo docker run --rm -d -p 80:8888 --name fair_bioinfo -v ${PWD}:/home/rstudio tdenecker/fair_bioinfo

# Execute the application into the container
$ sudo docker exec -it fair_bioinfo bash R -e "shiny::runApp('./R-code', port=4444)"
```

and on a browser running on your local machine, write the URL \(replace xxx.xxx.xxx.xxx according to your VM IP access and change the port number --80 here-- if necessary\): xxx.xxx.xxx.xxx:80/rstudio/p/4444/

At this point there is no data nor resulting count table, so we have to run the analysis workflow:

```text
$ sudo docker exec -it fair_bioinfo bash ./YOUR_SCRIPT.sh
```

And all that remains is to wait....

Attention! The final project weighs 42 GB. It is therefore necessary to have a VM with the right dimensions.

## Conclusion

### Some user reactions toward the docker solution

> _Super complicated for not much!_
>
> _I don't see the point...._ 
>
> _I've never had any problems of reproducibility between OS._

You're right, you're right! \(in part\)

#### Docker not so simple?

Yes... and no:

* making your own docker: it takes work
* but setting up a Rstudio server \(a docker done by someone else\) takes 5 minutes

#### No reproducibility problems due to OS

Indeed, it is rare but it can happen \(update, sharing, ...\)

#### Not very useful?

It's true, the docker does nothing more than what we already do locally. But it bring with it sharing + reproducibility + deployment that are impossible things with a local solution.

Docker will probably be the solution in the future!

### Where are we in terms of reproducibility?

![Practical Computational Reproducibility in the Life Sciences - Bj&#xF6;rn Gr&#xFC;ning et al \(2018\)](.gitbook/assets/image%20%28122%29.png)

From the schema of [Björn Grüning _et al._](https://doi.org/10.1016/j.cels.2018.03.014), we have reach the last stack of reproducibility, with the same reproducibility tools than they: git, conda, docker, and a VM.

## It's up to you!

Realize the complete analysis workflow on a VM and using the FAIR\_Bioinfo docker.

## Resources

Learning more with docker : [Reproducible computational environments using containers](https://carpentries-incubator.github.io/docker-introduction/index.html), a Carpentries lesson in progress

