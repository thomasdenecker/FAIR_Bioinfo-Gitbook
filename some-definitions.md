# The context of Reproducibility

## Some definitions

Recently, [an American report](https://www.nap.edu/catalog/25303/reproducibility-and-replicability-in-science) was published to clearly define what reproducibility is in Science in 2019 .

> **Reproducibility** means computational reproducibility—obtaining consistent computational results using the same input data, computational steps, methods, code, and conditions of analysis.

The report distinguishes between Replicability

> **Replicability** means obtaining consistent results across studies aimed at answering the same scientific question, each of which has obtained its own data.

​We can go further by opposing them to a 3rd term Repeatability  \(adapted from de [Hans E. Plesser, Front. Neuroinf. , 2017](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5778115/)\)

> **Repeatability**  means obtaining the closest possible results by performing an identical experiment \(methods, equipment, experimenters, laboratory and conditions\)

## Recommendations for Reproducibility in 2019

The American report recommends 4 main lines of action to be reproducible in 2019

* **Description of the experimental part**   \(Methods, instruments, procedures, measurements, experimental conditions  \)
* **Description of the computational part**   \(Steps in data analysis and technical choices  \)
* **Description of the statistical part**   \(Analytical decisions: when, how, why\)
* **Discussion of the choices and results obtained**

In short, everything must be described and justified so that another researcher can conduct the same scientific process.

## In reality ?

### In Biology

There is a real problem of reproducibility in Biology. **70 %** analyses in Experimental Biology are not reproducible \([Monya Baker, Nature, 2016](https://www.nature.com/news/1-500-scientists-lift-the-lid-on-reproducibility-1.19970)\). 

![Monya Baker, Nature, 2016](.gitbook/assets/image%20%28156%29.png)

### In computer science

The same problem is described in computer science \([Collberg et al, University of Arizona TR 14-04, 2015](http://reproducibility.cs.arizona.edu/)\). Here, there is 65% failure to reuse the software.



![Collberg et al, University of Arizona TR 14-04, 2015](.gitbook/assets/image%20%28137%29.png)

### In Bioinformatics

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

## Are there any solutions?

Fortunately, there are solutions and FAIR\_Bioinfo is here to offer you one! To try to solve its reproducibility problems, we will use development solutions.

![Some tools discussed in the FAIR\_Bioinfo](.gitbook/assets/image%20%2835%29.png)



