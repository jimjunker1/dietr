---
title: "tRophic Tutorial"
author: "Samuel R. Borstein"
date: "16 May, 2018"
output:
  html_document:
    keep_md: true
vignette: >
  %\VignetteIndexEntry{tRophic Tutorial}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}
---

# 1: Introduction

This is a tutorial for using the R package `tRophic`. `tRophic` uses diet or food item data to calculate
trophic levels following the procedures described in `TrophLab` (Pauly et al., 2000), which is only
avaialbleas a Microsoft Access database program. Our implementation is very easy to use and extremely
fast asusers can specify all their data as dataframes. It also differs from TrophLab in that users 
can specifya taxonomic hierarchy and measure trophic levels from their data at various levels (e.x.
individual,population, species,genus, etc.). tRophic also works well with FishBase (Froese & Pauly,
2018) dataand can use diet and food item data obtained in R using the rfishbase package (Boettiger 
et al., 2012). 

In this tutorial we will cover the basics of how to use tRophic to measure trophic levels. We will 
discuss both how to read in data from FishBase using rfishbase and how to pass that data into tRophic.
We will also show how one can use their own data and input that 

# 2: Installation
## 2.1: Installation From CRAN
In order to install the stable CRAN version of the AnnotationBustR package:
```
install.packages("tRophic")
```
## 2.2: Installation of Development Version From GitHub
While we recommend use of the stable CRAN version of this package, we recommend using the package `devtools` to temporarily install the development version of the package from GitHub if for any reason you wish to use it :
```
#1. Install 'devtools' if you do not already have it installed:
install.packages("devtools")

#2. Load the 'devtools' package and temporarily install the development version of
#'tRophic' from GitHub:
library(devtools)
dev_mode(on=T)
install_github("sborstein/tRophic")  # install the package from GitHub
library(tRophic)# load the package

#3. Leave developers mode after using the development version of 'AnnotationBustR' so it will not #remain on your system permanently.
dev_mode(on=F)
```
# 3: Using tRophic
To load tRophic and all of its functions/data:
```
library(tRophic)
```
## 3.0: Basic data necessary to run tRophic


## 3.1: Using tRophic to calculate trophic levels from FishBase diet data
Our first example will use FishBase diet data. For this, we will get data for two species: The Goliath
Grouper, *Epinephelus itajara*, and the Schoolmaster snapper, *Lutjanus apodus*.

```
#load fishbase
library(fishbase)
#Use FishBase diet function to read in  
my.longest.seqs<-FindLongestSeq(accessions)#Run the FindLongestSeq function
my.longest.seqs#returns the longest seqs found per species
```
In this case we can see that the function worked as it only returned accession AP011317.1 (16577 bp) for *Barbonymus schwanefeldii* which was longer than accessions KU498040.2, KU233186.2, KJ573467.1  (16570,16478, and 16576 bp respectively) as well as the single accessions for *Barbonymus altus* and *Barbonymus gonionnotus*. The table returns a three-column data frame with the species name, the corresponding accession number, and the length.

## 3.2: Load a Data Frame of Search Terms of Gene Synonyms to Search With:
AnnotationBustR works by searching through the annotation features table for a locus of interest using search terms for it (i.e. possible synonyms it may be listed under). These search terms are formatted to have three columns:

- Locus: The name of the locus and the name of the FASTA of the file for that locus to be written. It is important that you use names that will not confuse R, so don't start these with numbers or include other characters like "." or "-" that R uses for math.
- Type: The type of sequence to search for. Can be one of CDS, tRNA, rRNA, misc_RNA, D-Loop, or misc_feature.
- Name: A possible synonym that the locus could be listed under.

For extracting introns and exons, an additional fourth column is needed (which will be discussed in more detail later in the tutorial):
-IntronExonNumber: The number of the intron or exon to extract

So, if we wanted to use AnnotationBustR to capture these and write them to a FASTA, we could set up a data frame that looks like the following.

```
##   Locus Type    Name
## 1  ATP8  CDS ATPase8
## 2  ATP6  CDS ATPase6
```

While AnnotationBustR will work with any data frame formatted as discussed above, we have included in it pre-made search terms for animal and plant mitochondrial DNA (mtDNA), chloroplast DNA (cpDNA), and ribosomal DNA (rDNA). These can be loaded from AnnotationBustR using:

```
#Load in pre-made data frames of search terms
data(mtDNAterms)#loads the mitochondrial DNA search terms for metazoans
data(mtDNAtermsPlants)#loads the mitochondrial DNA search terms for plants
data(cpDNAterms)#loads the chloroplast DNA search terms
data(rDNAterms)#loads the ribosomal DNA search terms
```
These data frames can also easily be manipulated to select only the loci of interest. For instance, if we were only interested in tRNAs from mitochondrial genomes, we could easily subset out the tRNAs from the premade `mtDNAterms` object by:

```
data(mtDNAterms)#load the data frame of mitochondrial DNA terms
tRNA.terms<-mtDNAterms[mtDNAterms$Type=="tRNA",]#subset out the tRNAs into a new data frame
```

## 3.3:(Optional Step) Merge Search Terms If Neccessary

While we have tried to cover as many synonyms for genes in our pre-made data frames, it is likely that some synonyms may not be represented due to the vast array of synonyms a single gene may be listed under in the features table. To solve this we have included the function `MergeSearchTerms`.

For example, let's imagine that we found a completely new annotation for the gene cytochrome oxidase subunit 1 (COI) listed as CX1. The `MergeSearchTerms` function only has two arguments, `...`, which takes two or more objects of class `data.frame` and  the logical `Sort.Genes`, which We could easily add this to other mitochondrial gene terms by:

```
add.name<-data.frame("COI","CDS", "CX1", stringsAsFactors = FALSE)#Add imaginary gene synonym for cytochrome oxidase subunit 1, CX1
colnames(add.name)<-colnames(mtDNAterms)#make the columnames for this synonym those needed for AnnotationBustR

#Run the merge search term function without sorting based on gene name.
new.terms<-MergeSearchTerms(add.name, mtDNAterms, SortGenes=FALSE)

#Run the merge search term function with sorting based on gene name.
new.terms<-MergeSearchTerms(add.name, mtDNAterms, SortGenes=TRUE)
```
We will use this function again in a more realistic example later in this vignette.

## 3.4 Extract sequences with AnnotationBust



## 3.5 Troubleshooting



## 4: Final Comments
Further information on the functions and their usage can be found in the helpfiles `help(package=tRophic)`.
For any further issues and questions send an email with subject 'tRophic support' to sborstei@vols.utk.edu or post to the issues section on GitHub(https://github.com/sborstein/tRophic/issues).