---
title: "Block 2 - COOI"
start: true
teaching: 0
exercises: 20
questions:
- How can you obtain and interpret genome assembly information from NCBI?
objectives:
- Practice the usage of command line to get files from the internet and examine their content
- Interpret genome assembly overview from the internet
keypoints:
- You can obtain assembly information from NCBI
- You can subset this information and determine the number of genome assemblies
---

#  Genome assembly information
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
