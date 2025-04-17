---
title: "Block 1 - COOI: Recovering a microbial community"
start: true
teaching: 0
exercises: 20
questions:
- What interesting microbes can we find around us?
objectives:
- Practice the usage of command line to handle and analyse sequence files 
- Perform a metagenome assembly, and interpret metagenomic data
keypoints:
- You can obtain genome sequences from environmental sequencing
---

#  A CURIOUS BIOFILM

TEST2

You are biking on your way to the Utrecht Science Park, and your bike’s chain comes out. You stop, and atart to fix it. Your hands get greasy, it’s raining and you feel miserable. Once you are done, you look up. You had just stopped by a beautiful canal, and the falling of droplets on the water is mesmerising. Between the grass and the water, sheltered below a rock formation, you notice an odd slime. It is of a brownish, orange-y color, and you recognise it is a biofilm. You have learnt that microorganisms can form their own environments, where they interact, compete and thrive. You wonder what could be in there…

Since the year 2000, the emergence of Next Generation Sequencing (NGS) has facilitated the development of metagenomics. A metagenome is the combined genomic information of all organisms in a given sample. The metagenome contains information about the composition (taxonomy) and functions (encoded genes) of a microbial community. There are different ways to process and analyze metagenomes. First, targeted amplification and sequencing of the 16S ribosomal RNA gene, also known as 16S rRNA amplicon sequencing, is used to determine the relative abundances of taxa in the sample (taxonomic profiling). Second, shotgun metagenomic sequencing reads random parts of the total community DNA (or RNA in the case of meta-transcriptomics), allowing genomes (or transcriptomes) of all organisms to be sampled. Thus, genome-resolved metagenomics is an approach that enables extracting, sequencing, and analysing DNA without the need for PCR amplification and primers. Using metagenomics, we can now study the genomic content of uncultivated microorganisms from a natural environment, which helps us to identify them, determine their potential function within the environment, and guide us to their successful cultivation. Microbial communities have been investigated to contribute to various global biogeochemical cycles (carbon, nitrogen, phosphorus cycling), the cycling of greenhouse gases through, e.g., respiration of organic matter (methane, CO2, N2O), and the metabolization of toxic compounds (key word: bioremediation). Furthermore, mining of the genomic potential has also led to the discovery of many new bioactive substances, including novel antibiotics.

You decide to take action. You sample the biofilm, and take it to your Bioinformatics teachers. Since there is little biomass, and we want to avoid contaminants, you all agree to first try and enrich some of the microorganisms, focusing on those who can survive (or even thrive) without oxygen. To do that, you include your biofilm in an anaerobic bioreactor, and, after a few days, you have a stable community. Then, you take a sample from this community to the sequencing facility, where they disrupt cells using sonication, and purify DNA using beat-based methods. Then, they prepare DNA libraries to input the DNA to a PacBio sequencer. Once the long-read sequences are ready, they send them your way. Now, you focus on genome-resolved metagenomics of this prokaryotic community.

------


In our exercises, we will examine the quality of assembled *Z. tritici* genomes that are publicly available. We will use genome assemblies from NCBI. Unfortunately, generating eukaryote *de novo* genome assembly is beyond the scope of the exercises; in contrast to bacterial genomes, assembly of eukaryotic genomes requires significantly more computational power and time.

The genome assemblies of some _Z. tritici_ isolates downloaded from NCBI GenBank can be found **data/fungalgenomics_seidl/assemblies/** (state: 2018). There are continuously more and more genome assemblies being generated and deposited at NCBI. 

To get an overview of the number of genome assemblies currently at NCBI. We can obtain an overview file from NCBI with information of all genomes belonging to *fungi*.

~~~
$ wget https://ftp.ncbi.nlm.nih.gov/genomes/genbank/fungi/assembly_summary.txt
~~~
{: .bash}

> ## Exercise
> 
> How many fungal genomes are deposited in the database today?
>
>> ## Solution
>> 
>> `cat assembly_summary.txt | grep -v "^#" | wc -l`
>> 
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
