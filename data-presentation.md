# Data presentation

In this training, we will use data available in the literature to illustrate in a concrete case the implementation of a reproducible analytical solution. The objective is to carry out a differential gene expression analysis using RNAseq data in _O. tauri_.

## Origin of the data 

The organism studied is _Ostreococcus tauri_  and the available data are data from RNAseq single end. Data are available available in the following paper : [_Ostreococcus tauri_ is a new model green alga for studying iron metabolism in eukaryotic phytoplankton, Lelandais _et al_, BMC Genomics, 2016](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-016-2666-6).

## Experimental design

3 biological replicas were made for each condition. 16 different experimental conditions were performed. In this project, we will only use the data of the difference +Fe / -Fe at 9 hours in light condition 1

![Experimental design](.gitbook/assets/image%20%28128%29.png)

## Data recovery

47 experience files are available. You can recover all this data from :

* SRA \(Sequence Read Archive\) : [here](https://www.ncbi.nlm.nih.gov/sra?linkname=bioproject_sra_all&from_uid=304086)
* EMBL-EBI : [here](https://www.ebi.ac.uk/ena/data/view/PRJNA304086)

![Screenshot from SRA](.gitbook/assets/image%20%2817%29.png)

![Screenshot from EMBL-EBI](.gitbook/assets/image%20%2847%29.png)

### Data type

Data stand in "fastq" formated file where one sequence is described by 4 lines: 

* sequence identifier 
* sequence in base
* "+" 
* base calling score, one letter code for each base

```text
@SRR3099585.1 HWI-ST365:427:H8K2HADXX:1:1101:1497:2215/1
CTCCCTTGACATCTCGATGTCCTTGGTGAGCTCGTCAACCGCGTCCGCACGATACATTTGGTATGTCTTGAATACCTTCGGGAATCGTTGACGATCTGGA
+
@@@DADDD8:CFH9E3:?AC?BF>AC<+C+<E?:8C?FEE0?D0@:'--;A?B;>>3(6;@B>5;5B@@A@:3>A#########################
@SRR3099585.2 HWI-ST365:427:H8K2HADXX:1:1101:1653:2087/1
GTCGACGAATATAAAGTTATTGGGAGAGACGCTGAAGGTCGCGTTGGAGATGGACTCAATTGCGCTTCGCGTTCGCCTCGTGGGTGTTCGCCCGGCTCAC
+
@CBFFFFFHHGHHJJJHHJJJJJJJJJIJJJJJJIJJJGHJJJJJJHHHHHFFFFFEEEEEEDDDDDDDDDDDDDDDDDDDDDDBDDDDDDDDDDDDDDD
@SRR3099585.3 HWI-ST365:427:H8K2HADXX:1:1101:1554:2142/1
CGACTCAATAACCTTGTCTTCGATCGGCGGCACGGAGCCAGTGATATCGCATTCGCCGGGGAACCACTCGAGCTCAGTCATCCGAGAGCGCAAGGGCGCT
```

