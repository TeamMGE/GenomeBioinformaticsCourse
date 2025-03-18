---
title: "Genome assembly completness"
teaching: 0
exercises: 30
questions:
- How can we assess completness of fungal genome assemblies?
objectives:
- Compare genome assemblies in light of their completness.
keypoints:
- You can compare genome completness in eukaryotic genomes with BUSCO.
- You can critically reflect on the results of BUSCO and provide technical and biological explanations.
---

#  Genome assembly completness
We can examine the quality and completeness of genome assemblies by searching for specific set of genes that are known to be present in single copy in all/the majority of species of a selected group of eukaryotes. Think of example genes that are shared by all animals or by all plants or fungi. BUSCO is a tool that exactly does this type of analysis. It is very similar to `checkm` that you used in the first week of the course that enabled you to find marker genes in bacterial genomes. The main differences is that BUSCO is able to specifically address challenges in gene annotation that are specific to eukaryotes, e.g., the presence of exons and intron. You will learn more about gene annotation in the lectures and computer assignments on fungal gene annotation and gene expression.

Due to time constrains in the course, we have performed a BUSCO analyses for some selected genomes. The analysis was performed by searching for genes that are conserved and single copy in all ascomycete fungi, the fungal group to which *Z. tritici* belongs to. 

~~~
$ BUSCO.py -i ${genome} -c 24 -o ${genome} -m geno -l ascomycota_odb9
~~~
{: .bash}

The output data for BUSCO that can be found at **data/fungalgenomics_seidl/busco/**. We will initially focus on a set of *Zymoseptoria* genomes only (zymo/ directory). 

We will first take a look at the results for the reference genome IPO323.

~~~
$ less short_summary_IPO323.txt  
~~~
{: .bash}

We can see that the completness of the assembly is high, with 98.6% of the 1,315 single-copy BUSCO genes being identified as complete or duplicated. Only five genes are fragmented and 13 are missing, which supports the overall good quality of the reference assembly. 


> ## Exercise
> 
> Determine which genomes are most and which are least complete. Use a combination of UNIX/BASH commands to obtain these numbers for all genomes. 
>
>> ## Solution
>> 
>> ~~~
>> $ grep "Complete and sin" short* | sort -k2,2nr 
>> ~~~
>> {: .bash}
>> 
>> In the Zymoseptoria set, ASM22368v2 (1299/1315) is most complete and ASM22382v2 (1259/1315) is least complete.
>>
> {: .solution}
{: .challenge}

The file `missing_busco_list*` contain a list of genes missing in each genome assembly. Take for example a look at the file `missing_busco_list_IPO323.txt` to see the 13 genes that are missing from the reference genome assembly.

> ## Exercise
> 
> Determine the numer and the names of genes that always missing (absent). Do this for both the Zymoseptoria set (zymo/ fold) and the Mycosphaerellaceae set (myco/ folder) Use a combination of UNIX/BASH commands to obtain these numbers for all genomes.
>
>> ## Solution
>> First, we need to get number of genomes in each set. We can do this by counting the number of files.
>> ~~~
>> $ ls -l missing_busco* | wc -l 
>> ~~~
>> {: .bash}
>> 
>> This gives the number of genomes assessed: Zymo = 21 and Myco = 6. We can then determine the number of times a specific gene was missing from the BUSCO analyses. 
>> 
>> ~~~
>> $ cat missing_busco* | grep -v "^#" | sort | uniq -c | sort -k1,1nr | head -20
>> ~~~
>> {: .bash}
>>  
>> This shows that eight genes are missing in all the 21 Zymoseptoria genomes assessed. Seven genes are missing in all Mycosphaerellaceae.
>>
> {: .solution}
{: .challenge}

It is interesting to think about the reasons for the absence of genes in genome assemblies. If some genes are absent in few or only a single assembly, this likely suggests that the genome is not perfectly assembled. For example, we expect more genes to be absent in genome assemblies performed with short-read data. However, the absence of a subset of genes in all genomes of closely related strains and species more likely suggests that these genes might be lost in the most common ancestor. BUSCO genes are identified if they occur in >90% of the assessed species, and it is therefore not uncommon that few are not conserved. 

{% include links.md %}
