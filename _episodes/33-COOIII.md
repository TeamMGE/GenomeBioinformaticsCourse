---
title: "COO-III-3. Gene content associated with niche adaptation"
start: false
teaching: 0
exercises: 70
questions:
- How do genomes evolve under different environmental adaptations?
objectives:
- Search for genes associated with life under different temperatures
- Search for additional features such as CRISPR elements
---



## Gene families related to heat shock, DNA repair and stress

Thermophiles need to survive harsh conditions that can deteriorate their DNA and proteins, or affect the activity of key biological
processes. Therefore, they often acquire key genes via horizontal gene transfer.

As usually, let's first get our files ready:

~~~
cd ~/GenomeBioinformatics/Block1/COO-III/
mkdir 06_geneContent
cd 06_geneContent
ln -s ../01_genomeData/bin.3/bin.3.gbk bin.3.gbk
ln -s ../04_genomeDensity/col_prokka/prokka_Col_02*/*gbk .
ln -s ../01_genomeData/heim/prokka_GCA_0*/*gbk .
rename GCA_ heim_GCA_ GCA_*
ln -s ../01_genomeData/pangui/prokka_GCA_0*/*gbk .
rename GCA_ pangui_GCA_ GCA_*
~~~
{: .bash}


> ## Exercise: How many genes involved in repair mechanisms?
>
> Let's first do a general search for genes involved in repair mechanisms. What do you see?
>
> `grep -c repair *faa`
>
> Run this command without '-c' to see the actual functions
>
> Look also for other protein functions sometimes linked to adaptation to thermophily, such as repair proteins like `RadA` and `Nre`,
> protein stabilisation systems like the `Thermosome`, DNA stabilisation proteins like those involved in `polyamine` synthesis, or those
> involved in oxidative stress reduction like `superoxide` reductases.
>
{: .challenge}


## Reverse gyrase

One particular protein is often a smoking gun for thermophily: reverse gyrase. While its exact mechanism is still not well understood, 
this protein performs a certain DNA supercoiling activity that plays a role in life at high temperatures. Here, we will try to find this protein, and evaluate its origins.

> ## Exercise: Which genomes contain reverse gyrase?
>
>> ## Solution
>>
>> `$ grep "Reverse gyrase" *faa`
>>
>> All thermophilic genomes seem to contain Reverse gyrase genes. Surprisingly, also our Asgard archaeon has one copy of it.
>> 
> {: .solution}
{: .challenge}

Now, let's extract the reverse gyrase protein sequences:
~~~
grep -h "Reverse gyrase" *faa | sed 's/ .*//' | sed 's/>//' > revGyr.list
cat *faa > all_genomes.faa
seqtk subseq all_genomes revGyr.list > revGyr.faa
~~~
{: .bash}

And reconstruct a phylogenetic tree using these, and a reference set of archaeal reverse gyrases. We will do this by using some quick tools that will give us approximate but accurate enough answers.

~~~
cd ~/GenomeBioinformatics/Block1/COO-III/
mkdir 07_reverseGyrase
cd 07_reverseGyrase
ln -s ../06_geneContent/revGyr.faa .
ln -s ~/data_bb3bcg20/Block1/COOIII/Data/revGyr_refs.faa .

cat revGyr.faa revGyr_refs.faa > revGyr_OursAndRefs.faa
famsa revGyr_OursAndRefs.faa revGyr_OursAndRefs.faa.famsa
trimal -in revGyr_OursAndRefs.faa.famsa -out revGyr_OursAndRefs.faa.famsa.tr05 -gt 0.5
perl ~/data_bb3bcg20/bin/scripts/remove_gappy_seqs.pl 0.5 revGyr_OursAndRefs.faa.famsa.tr05 > revGyr_OursAndRefs.faa.famsa.tr05.over50p

OMP_NUM_THREADS=12
export OMP_NUM_THREADS
FastTreeMP -lg revGyr_OursAndRefs.faa.famsa.tr05.over50p > revGyr_OursAndRefs.faa.famsa.tr05.over50p.fasttree
~~~
{: .bash}

In the previous commands, you are (1) retrieving the sequences from your genomes of interest and the reference sequences, (2) 
placing them all in the same file, (3) aligning using the very fast aligner FAMSA, (4) removing columns with over 50% gaps, (5)
removing sequences formed by over 50% gaps. Finally, you are reconstructing a quick tree using FastTreeMP. By default, this tool
uses all available CPUs. To avoid that, we are modifying an environment variable from which this tool reads how many CPUs you
allow it to use -- in this case, 12.

> ## Exercise: Analyse the tree
>
>  Use the command-line package newick utils to visualise the tree:
>
>  `$ nw_display -w 150 revGyr_OursAndRefs.faa.famsa.tr05.over50p.fasttree | less`
>
>  Use 'q' to quit the visualisation.
>
>  
> 
>> ## Solution
>>
>> While the tree is not very robustly reconstructed, it indicates that the copies of the reverse gyrase were obtained via
>> horizontal gene transfer from other archaea (i.e. Asgard archaea are not the sister group). It may also appear that the
>> sequences may have originated through different events of horizontal transfer. 
>> 
> {: .solution}
{: .challenge}


## CRISPR arrays

One final element we can analyse is CRISPR arrays. We don't yet completely understand why, but thermophilic tend to have a higher
prevalence of CRISPR sequences in their genomes, both in terms of the fraction of genomes containing them, and in terms of the 
number of CRISPR repeats+spacers included in the genome. Let's analyse the presence of CRISPR sequences in these genomes, too.

~~~
cd ~/GenomeBioinformatics/Block1/COO-III/
mkdir 08_CRISPR
cd 08_CRISPR
ln -s ../01_genomeData/*/*fna .
~~~
{: .bash}

To do this analysis, we will use the tool MINCED, specialised in detecting CRISPR arrays:

~~~
for f in *.fna; do minced $f > $f.crispr; done
~~~
{: .bash}

> ## Exercise: Which genomes contain CRISPR elements?
>
> 
>> ## Solution
>>
>> `grep Repeats *crispr`
>>
>> It seems that, once again, it is only thermophilic genomes, as well as our Asgard archaeal genome, which contain CRISPR elements.
>> Looking at the number of repeats, it also appears that genomes with higher numbers of repeats are found in genomes that grow at
>> very high temperatures.   
>> 
> {: .solution}
{: .challenge}
