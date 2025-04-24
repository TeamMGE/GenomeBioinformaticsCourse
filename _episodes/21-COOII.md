---
title: "COO-II-1. Genome annotation"
start: false
teaching: 0
exercises: 30
questions:
- How to obtain metagenomic sequencing data?
objectives:
- Become familiar with sequencing technology and sequencing reads format
---

Your genomes are, at the moment, simple Fasta nucleotide sequences. 
A first step now is to infer the genes they encode. Let’s roll up our sleeves and get going.

Now, set up your new working environment and generate a folder named “annotation” for this next step. Link the bin fasta files here:
~~~
cd ~/GenomeBioinformatics/Block1/
mkdir COO-II
cd COO-II
mkdir 01_annotation
cd 01_annotation
ln -s ~/GenomeBioinformatics/Block1/COO-II/Data/Metawrap_BinRefinement_c50_x5 .
~~~
{: .bash}

You need to annotate all your bins. We will start by running Prokka, a fast, all-purpose genome annotation tool 
that identifies protein-coding genes, as well as genes for rRNA, tRNA and other interesting sequences such as CRISPR arrays. 
More specifically, we will use a slightly modified version of prokka with a couple of fixed bugs.
Get a feel about what prokka can do by running it with the option “-h”. 
~~~
prokka_ed -h
~~~
{: .bash}

We will run this software on each genome, generating file names and output folders that allow us to trace the bin that was 
used for annotation. Moreover, prokka uses different databases depending on whether the genomes are bacterial or archaeal, 
so make sure you know which domain each bin belongs to by looking at the 'stats' file. 
You can tell Prokka to which domain the specific bin belongs by using 
the flag “--kingdom Archaea”, or “--kingdom Bacteria” when you are running prokka on an archaeal or bacterial bin, respectively. 
Moreover, we can tell prokka to consider that these are MAGs (fragmented assemblies with possibly complicated assembly paths). 
You can put it all together with the following command (remember to switch Bacteria to Archaea for archaeal bins):
~~~
f=Metawrap_BinRefinement_c50_x5/bin.1.fa
filename=$(basename $f);
name=${filename%.fa};
prokka_ed -o prokka_$name --prefix $name --locustag $name --kingdom Bacteria --metagenome --cpus 2 $f
~~~
{: .bash}


Run this on each one of the five bins (ca. 2-3 min each), and take a look at the log that is printed to screen. 
It will show you the different features that are being annotated for each bin (tRNAs, rRNAs, CRISPR repeats, 
Coding DNA sequences, plus BLAST analysis for each protein-coding gene). Start by exploring the results. 

A number of files are generated. Among others, these include:
-	A fasta file containing the entire nucleotide sequence of the genome (“.fna” for “fasta nucleic acid”). 
-	A fasta file containing the nucleotide sequences for all genes (“.ffn”, somehow standing for “fasta nucleotide”)
-	A protein fasta file (“.faa” for “fasta amino acid”) containing all translated protein-coding genes inferred form the genome.
-	A “genbank file” (“.gbk”), including stardardised annotation information, with metadata at the top, all detected features,
-	and ending in the entire nucleotide sequence of the genome.


Take a peek at each one of these files, noticing their formats. 

> ## Exercise: Take a peek at each one of these files
>
> Get familiar with the formats and content of these files.
> 
> Are the protein-coding sequences annotated with inferences on their possible functions?
> 
> How many protein-coding sequences were found per bin? How many RNA-coding genes?
>
> Did prokka identify CRISPR arrays in any of the bins?
> 
>> ## Solution
>>
>> `grep -c ">" prokka_*/*faa`
>>
>> `grep -c ">" prokka_*/*ffn`
>>
>> To check on which features fasta nucleotide and fasta amino acid files differe (i.e. to detect non-coding genes), you can do:
>>
>> `grep ">" prokka_bin.1/bin.1.faa | sed 's/ .*//'  > bin.1.faa.names`
>>
>> `grep ">" prokka_bin.1/bin.1.ffn | sed 's/ .*//' > bin.1.ffn.names`
>>
>> `grep -vf bin.1.faa.names bin.1.ffn.names > bin.1.difference`
>>
>> `grep -f bin.1.difference prokka_bin.1/bin.1.ffn`
>>
>> These last lines work as follows: (1) detecting all headers in the fasta file containing the amino acid sequences of all
>> protein-coding genes, removing everything after the first space (to keep only the locus tag) and storing the result
>> in a file called 'bin.1.faa.names'; (2) repeating the same process with the fasta file containing the nucleotide sequences
>> of all genes; (3) those gene names present in the ffn file but not in the faa file (i.e. 'grep -f' uses all the search strings
>> included in the given file, and 'grep -v' provides the negative result: the lines not matching those strings); and finally
>> (4) finding the full names of those genes again. The obtained results should all correspond to non-protein-coding genes.
>> 
> {: .solution}
{: .challenge}


