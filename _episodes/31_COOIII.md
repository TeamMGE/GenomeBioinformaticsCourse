---
title: "COO-III-1. Growth parameter prediction"
start: false
teaching: 0
exercises: 40
questions:
- How to use genome features to predict growth parameters?
objectives:
- Learn to use a novel tool to predict growth parameters from genome data
- Learn to parse results to obtain general statistics that allow you to make inferences
---

## Generate your working environment

First of all, let's get our data sorted.

~~~
cd ~/GenomeBioinformatics/Block1/
mkdir COO-III
cd COO-III
mkdir 01_genomeData
ln -s ~/data_bb3bcg20/Block1/COOIII/Data/bin.3 .
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



## Inferring growth parameters

Given that your Asgard archaeal genome and the Panguiarchaeal genomes were obtained from very different environments (a Dutch canal, 
and hot springs, respectively), we may suspect they have adapted to different niches. Certain tools can help you predict optimal
growth parameters based on genome features. Let's use one of them, called GenomeSPOT.

~~~
cd ~/GenomeBioinformatics/Block1/COO-III
mkdir 02_growth
cd 02_growth
mkdir genomes
cd genomes
ln -s ../../01_genomeData/bin.3/*faa .
ln -s ../../01_genomeData/bin.3/*fna .
ln -s ../../01_genomeData/fromCollaborator/*fna .
~~~
{: .bash}

We already have protein sequences for our bin, but we need protein sequences for our partner's bins. Let's generate them
with a quick tool called Prodigal:
~~~
for f in Col*.fna; do prodigal -i $f -a ${f/.fna/.faa} -o /dev/null ; done
~~~
{: .bash}

In this command, we store the names of the files in the variable '$f', and we then use this variable as input for prodigal. 
As output name, we use a modified version of this variable (signaled '${f/.fna/.faa}'), where we replace the suffix '.fna'
with the suffix '.faa'. We also use /dev/null as the place where to print useless standard output. This is a 'virtual black hole'
that is often used when we want to avoid printing something -- by using it, the printed output disappears.

Now that we have both contig sequences and protein sequences, we can use GenomeSpot:
~~~
python3.11
for f in *fna; do python -m genome_spot.genome_spot --models ~/data_bb3bcg20/bin/GenomeSPOT/models/ \
  --contigs $f \
  --proteins ${f/.fna/.faa} \
  --output $f.genome_spot \
; done
~~~
{: .bash}

> ## Exercise: Optimal growth parameters
>
> Read the obtained results using 'less'. What kind of conditions do these genomes require to grow, based on this inference?
> 
>> ## Solution
>>
>> Use 'less' with the different files.
>>
>> To summarise, you can run:
>>
>> `grep "ph_optimum" *genome_spot.predictions.tsv`
>>
>> `grep "salinity_optimum" *genome_spot.predictions.tsv`
>>
>> `grep "temperature_optimum" *genome_spot.predictions.tsv`
>>
>> The main notable difference is indeed that these organisms seem to grow optimally at very different temperatures. While
>> the organisms found by your collaborator grow best at high temperatures, the organism you found is still a mesophile  
>> 
> {: .solution}
{: .challenge}

Let's also calculate the growth parameters of the two other Asgard archaeal groups against which you wanted to compare:
~~~
cd ..
ln -s ../01_genomeData/pangui/ .
ln -s ../01_genomeData/heim .
mkdir heim_genomespot pangui_genomespot

for f in heim/prokka_*/*fna; do
  NAME=$(basename $f)
  python -m genome_spot.genome_spot --models ~/data_bb3bcg20/bin/GenomeSPOT/models/ \
  --contigs $f \
  --proteins ${f/.fna/.faa} \
  --output heim_genomespot/$NAME.genome_spot \
; done

for f in pangui/prokka_*/*fna; do
  NAME=$(basename $f)
  python -m genome_spot.genome_spot --models ~/data_bb3bcg20/bin/GenomeSPOT/models/ \
  --contigs $f \
  --proteins ${f/.fna/.faa} \
  --output pangui_genomespot/$NAME.genome_spot \
; done

grep "temperature_optimum" heim_genomespot/*tsv | cut -f2 > heim_genomespot/OGT.list
grep "temperature_optimum" pangui_genomespot/*tsv | cut -f2 > pangui_genomespot/OGT.list

python2.7
~~~
{: .bash}

> ## Exercise: How do these two groups compare to our genomes?
>
> Use the script '~/data_bb3bcg20/bin/scripts/stats.pl' to obtain general statistics about these values
> 
>> ## Solution
>>
>> `perl ~/data_bb3bcg20/bin/scripts/stats.pl heim_genomespot/OGT.list`
>>
>> `perl ~/data_bb3bcg20/bin/scripts/stats.pl pangui_genomespot/OGT.list`
>>
>> Based on these values, Heimdallarchaea seem to require lower temperatures to grow (ca. 30-37 °C), while Panguiarchaea
>> require high temperatures to grow (72-74 °C). The latter are quite similar to the temperatures preferred by the organisms
>> our collaborators are studying. Interestingly, our Asgard archaeon is somewhere in between.
> {: .solution}
{: .challenge}


