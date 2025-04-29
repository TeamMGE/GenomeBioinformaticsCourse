---
title: "COO-III-1. XXXXXX"
start: false
teaching: 0
exercises: 30
questions:
- 
objectives:
- 
- 
---

## Generate your working environment

First of all, let's get our data sorted.

~~~
cd ~/GenomeBioinformatics/Block1/
mkdir COO-III
cd COO-III
mkdir 01_genomeData
ln -s ~/data_bb3bcg20/Block1/COOIII/Data/bin_3 .
ln -s ~/data_bb3bcg20/Block1/COOIII/Data/fromCollaborator .
~~~
{: .bash}


## Retrieving additional genomes

A quick search in the Genome Taxonomy Database reveals that these sequences are closely related to an archaeal family
called Panguiarchaeaceae. So you used the GToTree tools to obtain all known genomes from this group, and then keep those that
have scores of at least 70% completeness and less than 5% contamination. To compare your genomes to a slightly more distant
group, you reach for a slightly more distant group from the Heimdallarchaea, selecting also genomes with the same high-quality
metrics. 

~~~
ln -s ~/data_bb3bcg20/Block1/COOIII/Data/pangui .
ln -s ~/data_bb3bcg20/Block1/COOIII/Data/heim .
~~~
{: .bash}





