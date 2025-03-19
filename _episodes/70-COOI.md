---
title: "Block 3 - COOI: Recent ploidy changes in the Saccharomycotina (part 1)"
start: true
teaching: 0
exercises: 120

questions:
- How can you determine changes in ploidy using next-generation sequencing data?

objectives:
- Generate k-mer profiles from short-read data
- Analyze and interprete k-mer profiles to estimate genome size, heterozygocity, repeat content
- Analyze and interprete k-mer profiles to estimate ploidies
- Generate allele frequency plots from short-read data
- Analyze and interprete allele frequencies to estimate ploidies

keypoints:
- You can work with next-generation sequencing data
- You can detect (recent) ploidy changes in fungi using next-generation sequencing data 
---

# Saccharomyces cerevisiae as a model system to study eukaryotic genome evolution
The focus of the three COOs in Block 3 is ‘Eukaryotic genomes and genome evolution’. We will apply different bioinformatics approaches to analyze genome sequencing data to obtain insights into different processes or phenomena that generate genetic variations such as ploidy shifts (COOI and COOII) and large-scale structural variation (COOII and COOIII).

The bakers' yeast Saccharomyces cerevisiae is one of the most well-known eukaryotic model organisms. The *S. cerevisiae* genome was the first completely sequenced eukaryotic genome. The bakers’ yeast genome is only ~12 Mb in size and only encodes ~6,000 protein-coding genes, which is rather small compared with most other fungi or other eukaryotes. *S. cerevisiae* played an important role in advancing our understanding mechanisms contributing eukaryotic genome evolution, and its experimental and genetic tractability greatly facilitated research into gene functions. Consequently, *S. cerevisiae* serves as a model for more complex eukaryotes, including humans.

Throughout these COOs, we’ll use the bakers’ yeast and allied species as models for other more complex eukaryotes. Due to the small size and the rich available data, it provides an ideal starting point and test case to explore genome evolution in eukaryotes.

# Recent ploidy changes in the Saccharomycotina
The bakers' yeast belongs to the Saccharomycotina subphylum, a taxonomic group that contains many species colloquially referred to as budding yeasts. Budding yeasts are mainly thriving as saprophytes, but some are important pathogens of plants and animals, including human. Furthermore, many members of this sub-phylum are commonly exploited in agriculture, food production, or biotechnology. Interestingly, many of these important species are hybrids that are formed by the fusion of two genetically distinct parents. Next to the doubling of the number of chromosomes by hybridization (also referred to as allopolyploidy), genomes of a single species can also double (autopolyploid). Changes in ploidy, either due to allo- or autoployploidy can generate important variation and is thought to play an important role to fuel adaptive genome evolution.

## 1. Estimate genome size, heterozygosity, and repeat content using k-mer frequencies (~120 min)
The genome size, heterozygosity rate and repeat content are important genome characteristics that provide a framework to study genome evolution, but also to assess for instance the quality (completness) of genome assemblies.

Some of these characteristics can be directly derived from unprocessed short reads using k-mer profiles. The k-mer profile (sometimes called k-mer spectrum) measures how often k-mers, substrings of length k, occur in the sequencing data. The profiles reflect the complexity of the genome: homozygous genomes have a simple Poisson profile with a single peak approximately at the overall genome-wide coverage of the data, i.e. if a single region in the genome is sequenced x times, we expect to identify the same k-mer to also occur x times in our data. Furthermore, this distribution provides an indication of the sequencing errors present in the data, i.e. k-mers that are are unique or very rare in the data. Heterozygous genomes typically show a characteristic bimodal profile where one of the peaks represents the homozygous fraction and another peak the heterozygous fraction. Repetetive regions such as transposons and/or duplications can add additional peaks at even higher k-mer frequencies. Thus, we can directly obtain information based on the genome size, the estimated heterozygosity and the repeat content directly from the shape of the k-mer frequency distribution and from the abundances of k-mers in the data.

We will use this approach to first estimate the haploid genome size of a single yeast strain.

**a.** Use commands including ls and pwd to localize the short-read sequencing data from *S. cerevisiae* strain CBS7837 in the data storage folder (**~/data/**). Then create a symbolic link to your own folder with the ln -s command. 

~~~
$ ln -s ~/data/Block3/genomes/XXXX .
~~~
{: .bash}

To determine the genome size, we first need to calculate the k-mer profile using the short-read data for CBS7837. We will use jellyfish to summarize the k-mers that are present in the short-read data.

Take a look at the command line parameters

~~~
$ jellyfish count --help
~~~
{: .bash}
> ## Exercise
>
> Prepare but do not execute a simple jellyfish count command that will count k-mers length 21 and k-mers occuring on both strands (canonical). You have to define the memory (-s option) and set it to 1000000000 (1 Gb). Store the output in a new file (-o option). Important Running jellyfish will take quite some time (~30 min). To be able to continue within the timeframe of this COO, obtain the jellyfish count file (ends with .jf) from the intermediate results folder.
>> ## Solution
>>
>> `jellyfish count -C -m 21 -s 1000000000 -o ScereCBS7837.jf <(zcat ScereCBS7837_R1.fastq.gz) <(zcat ScereCBS7837_R2.fastq.gz)`
>> This will execute jellyfish count. It is important to use canonical counts (-C, both strands), and provide the kmer length (m, 21). As read files are often zipped (due to their size), these need to first be unzipped. This can also be done 'dynamically' during the jellyfish call using `<(zcat file1.fq.gz) <(zcat file2.fq.gz)` (see the example above.
> {: .solution}
{: .challenge}






Inspect the assembly_summary file further. You can look at [README_assembly_summary.txt](https://ftp.ncbi.nlm.nih.gov/genomes/README_assembly_summary.txt) for a description of the columns.

> ## Exercise
> 
> What does the term ???representative genome??? refer to?
>
>> ## Solution
>> 
>> Representative genomes: Additional high-quality genomes are identified by clustering genomes and applying weighting metrics that include consideration of species-level taxonomic classification (e.g., a preference for type strain) and assembly quality (e.g. a preference for complete genomes but WGS is allowed). Additional quality assurance analysis is being added to add consideration of annotation quality metrics such as assessing the number of frame-shifted proteins (compared to close neighbours), presence of the set of expected rRNA and tRNAs, and gene density. We also take into consideration taxonomic diversity and will include some genomes that are taxonomic outliers for which little functional information is available in the representative genome collection.
>> 
> {: .solution}
{: .challenge}


We can now try to find out for how many different species there is at least one genome sequenced. We can do this using the command line. 

~~~
$ cat assembly_summary.txt | grep -v "^#" | cut -f8 | cut -f1,2 -d" " | sort -u | wc -l
~~~
{: .bash}

With `grep` you can obtain the lines matching a specific expression. The `-v` inverts the matches, i.e., it will return all lines that do not match the expression. In our case, we are excluding linkes starting with a `#` as these are comment lines. We then obtain the eights column - why? (Tip: Take a look at the information stored in each column)  with `cut -f 8` and the only retain the first two entries separated by a space `-d " "`. Lastly, we make these results unique and then count the number of lines.

> ## Exercise
> 
> Can you modify the code to obtain the code to obtain the fungal genus for which most genome assemblies have been produced so far? Explain the logic of the code.
>
>> ## Solution
>> 
>> `cat assembly_summary.txt | grep -v "^#" | cut -f8 | cut -f1,1 -d " " | sort | uniq -c | sort -k1,1nr | head`. The logic is similar to above but we only keep the genus information (first entry in the second cut), and then sort the output numerically to get the highest number on top of the list.
>> 
> {: .solution}
{: .challenge}

We can now use a very similar code to obtain the number of *Zymoseptoria* genome assemblies currently available.
 ~~~
$ cat assembly_summary.txt | grep -v "^#" | cut -f8 | cut -f1,2 -d" " | sort | uniq -c | sort -k1,1nr | grep "Zymoseptoria"
~~~
{: .bash}

> ## Exercise
> 
> Can you think about a code that would give you the number of complete (chromosome-level) genome assemblies?
>
{: .challenge}

{% include links.md %}
