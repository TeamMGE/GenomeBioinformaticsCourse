---
title: "COO-II-3. Phylogenomic reconstruction"
start: false
teaching: 0
exercises: 75
questions:
- How to use whole-genome information to place an organism in the tree of life?
objectives:
- Learn how to run a phylogenomic pipeline 
---

## Obtain genome data to reconstruct a phylogenomic tree

Your 16S rRNA tree may give some clues, but clearly the overall topology is not very well resolved, and the classification of 
at least one of your archaeal bins is not quite clear. Additionally, if one of your bins is distantly related to everything in 
the database, a nucleotide phylogeny may not be the best choice. And of course: using multiiple genes should give a better result
than using a single gene. These are sufficient reasons to aim to do a more thorough job, and try to reconstruct a phylogenomic tree 
using multiple marker genes.

Here, we are going to use GToTree, a phylogenomic software that performs the entire pipeline for you. You just need to provide a set 
of genomes. We already have ours, and all we need to retrieve is a set of reference genomes from public databases. To do that, we need 
genomes from all across the Archaea.

GToTree includes an easy-to-use toolkit to interact with the Genome Taxonomy Database (GTDB), so we can choose which public genomes we 
want to use. Let's start by obtaining genomes for *all* available archaeal species in the GTDB. This will need to be run using the
python3.11 environment.

~~~
$ cd ~/GenomeBioinformatics/Block1/COO-II/
$ mkdir 03_phylogenomicTree
$ cd 03_phylogenomicTree
$ python3.11
$ gtt-get-accessions-from-GTDB --GTDB-representatives-only -t Archaea
~~~
{: .bash}

> ## Exercise: Explore the available data
>
> - How many genomes were found?
> - What kind of information was retrieved?
> - What variation of assembly quality, environmental sources and taxonomy exists?
>
>   Note: use the script '~/data_bb3bcg20/bin/scripts/coltab.sh' to visualise the table
>  
>> ## Solution
>>
>> If GToTree takes too long to download the required data, obtain the file from the provided Intermediate files.
>>
>> `$ cp ~/data_bb3bcg20/Block1/COOII/Intermediate_files/GTDB_genome_data/GTDB-Archaea-domain-GTDB-rep-metadata.tsv .`
>> 
>> `$ wc -l GTDB-Archaea-domain-GTDB-rep-metadata.tsv`
>>
>> `$ ~/data_bb3bcg20/bin/scripts/coltab.sh GTDB-Archaea-domain-GTDB-rep-metadata.tsv`
>> 
> {: .solution}
{: .challenge}

### Select high quality genomes

It is possible to parse this table to select only a few genomes of interest. Let's select only genomes with a completeness 
score higher than 90% and a contamination score lower than 5%. These values are in columns 10 and 11, and can be processed using 'awk':
~~~
$ awk -F '\t' '$10>90 && $11<5' GTDB-Archaea-domain-GTDB-rep-metadata.tsv > GTDB-Archaea-domain-GTDB-rep-metadata.tsv.comp90_cont5
~~~
{: .bash}

How many genomes are left? To use only about a hundred for phylogenomic analyses, we can just select one genome per taxonomic order (column 5):
~~~
$ cut -f5 GTDB-Archaea-domain-GTDB-rep-metadata.tsv.comp90_cont5 | sort -u > orders.list
$ cat orders.list | while read order; do grep $order GTDB-Archaea-domain-GTDB-rep-metadata.tsv.comp90_cont5 | head -n 1 ; done > GTDB-Archaea-domain-GTDB-rep-metadata.tsv.comp90_cont5.1perorder
~~~
{: .bash}

Now we only need to extract the accession numbers and we will be able to run the pipeline:
~~~
$ cut -f1 GTDB-Archaea-domain-GTDB-rep-metadata.tsv.comp90_cont5.1perorder | sed 's/^[A-Z][A-Z]_//' | sort -u > GTDB-Archaea-domain-GTDB-rep-metadata.tsv.comp90_cont5.1perorder.accessions
$ ln -s GTDB-Archaea-domain-GTDB-rep-metadata.tsv.comp90_cont5.1perorder.accessions GTDB_genomes.list
~~~
{: .bash}


## Reconstruct a phylogenomic tree

Now, we can run GToTree with your original genomes and with the list of genomes from the GTDB database. 
Run the pipeline by using these commands:
~~~
$ ls ../01_annotation/prokka_*/*.faa > bins_faa.list
$ GToTree -A bins_faa.list -a GTDB_genomes.list -H Archaea -D -j 5
~~~

The first command should simply create a file containing a list of the fasta amino acid files from your bins. 
The second will run the entire pipeline. GToTree will download the GTDB genomes, filter them for quality, and identify 
Coding DNA sequences. Next, it will use all the inferred amino acid sequences (from the published genomes and your own bins) 
to find a curated set of 122 vertically-evolving archaeal genes, filter out genomes with too few marker genes, align and trim 
each gene, concatenate the resulting alignments, and use the obtained concatenated alignment (or supermatrix) to reconstruct 
a phylogenomic tree. The whole thing! After about 10 minutes, your run will be ready. Check out the results! 


> ## Exercise: Analyse the run
> Use the “coltab” script to look at the genomes that were removed because they contained too few SCG (“single-copy genes”): is this 
list surprising?
>
> Find more information about this result by looking at the “SCG_hit_counts.tsv” file. How many genomes are flagged 
as having relatively high redundancy values? What may that mean, in the context of metagenomics?
> 
>> ## Solution
>>
>> 
> {: .solution}
{: .challenge}


Finally, you can visualise the obtained tree as you did for the 16S rRNA tree above. 
 

> ## Exercise: Visualise the tree
> - Does this tree look better than the 16S rRNA tree?
> - Can you say something more about Bin3 compared to the 16S rRNA tree? This time, the tree contains branch support values. You can show 
them in Figtree by clicking on “branch labels” and then displaying the value you imported when opening the tree. In iTOL, you can click 
on “Advanced”, then “Display” next to “Bootstraps/metadata”, and finally in “Text”. Change font sizes if necessary.
> - Are the positions of Bin 3 and Bin 5 well supported?
> - Is the identity of Bin 5 consistent with the previous results?
> - What can you say about the identity of Bin 3?
>   
>> ## Solution
>> 
> {: .solution}
{: .challenge}

## Conclusions

You have identified several organisms from a microbial community you found in the Utrecht canals. Some of these may be novel species, or 
even representatives of more diverse taxa. Understanding what these organisms are, and what they can do, can give us more information about
the diversity of life, and perhaps about the ecosystems that surround us!

What would you do to better explore the identiy of these organisms? Can you think of ways to attempt more precise phylogenomic placements 
(particularly for Bin 3)? If you have time, energies and interest, do go ahead and perform a new phylogenomic run with a better genome 
dataset! 

And what could you do to gain information about their lifestyle, or their ecological interactions? There is so much that we can infer 
by simply finding clever ways of looking at these genomes!


