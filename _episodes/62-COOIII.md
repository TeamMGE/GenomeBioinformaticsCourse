---
title: "Block 2 - COOIII: size of a gene family"
start: false
teaching: 0:00
exercises: 30
questions: 
- Can we quickly establish the size of a gene family in a predicted proteome?    
objectives: 
- Practice with using HMMER
- Practice with intepreting homology searches in light of the literature
keypoints:
- Expanded gene families reflect taxon/species adaptation 
---

## background on gene family sizes and adaptations 

In the “genoombiologie” course that some of you followed, we discussed that elephants have undergone duplications of their p53 gene and that this has been hypothesize to help these large animals that need many cell divisions to avoid cancer. See [https://elifesciences.org/articles/11994](https://elifesciences.org/articles/11994)


An easy way to get the size of a gene family is to take a profile of a gene family and search against all predicted proteins of the genome/species of interest and count the hits. Note that this is also a common strategy to collect sequences for an informative gene tree and for example how we got the RasGEF sequences for our exercises on this gene family. In this exercise we are going to use the P53 profile to look at how many members this gene family has in a few mammals.

## getting the p53 profile and 


A profile for The p53 gene family is in principle available at EBI INTERPRO. However it is a bit tricky to get the p53 gene family profile on gemini as I could not locate a ftp/http site that could be approached via wget. So we have to go the long way around. Go to [https://www.ebi.ac.uk/interpro/entry/pfam/PF00870/logo/](https://www.ebi.ac.uk/interpro/entry/pfam/PF00870/logo/). Press download to download the fileto your laptop (notize it is compressed using the gzip protocol). Use scp to copy it to gemini. (see earlier exercises)


On gemini use `gunzip` on the command line to unzip the file  and look at its contents. 
> ## Exercise: How many sequences were used to make this profile?
>
>> ## Solution
>> NSEQ  38
>>
>> So 38
> {: .solution}
{: .challenge}


We are going to use HMMSEARCH to find all members of this families. First let us do human. `hmmsearch your_hmm_profile_name ~/data_bb3bcg20/Block2/COOIII/Homo_sapiens.GRCh38.pep.all.longest.fa > your_output_file`. Look at the output.
> ## Exercise: How many hits (and thus gene family members) do you see in human?
>
>> ## Solution
>> 3
> {: .solution}
{: .challenge}

Do the same for Heterocephalus_glaber_female.Naked_mole-rat_maternal.pep.all.longest.fa,  Loxodonta_africana.loxAfr3.pep.all.longest.fa (the elephant), and Mus_musculus.GRCm38.pep.all.longest.fa. 

> ## Exercise: Can you reproduce the result from the literature?
>
>> ## Solution
>> Yes elephant has more than all. 
> {: .solution}
{: .challenge}

> ## Exercise:Why have we included also the naked mole rat?
>
>> ## Solution
>> Because it is also semi-famously cancer resistant. But this is not apparently achieved via p53. 
>> 
> {: .solution}
{: .challenge}
