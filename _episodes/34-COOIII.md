---
title: "COO-III-4. Gene content associated with niche adaptation"
start: false
teaching: 0
exercises: 30
questions:
- How do genomes evolve under different environmental adaptations?
objectives:
- Search for genes that are associated with life under different temperatures 
---



## Gene families related to heat shock, DNA repair and stress

Thermophiles need to survive harsh conditions that can deteriorate their DNA and proteins, or affect the activity of key biological
processes. Therefore, they often acquire key genes via horizontal gene transfer.

As usually, let's first get our files ready:

~~~
cd ~/GenomeBioinformatics/Block1/COO-III/
mkdir 05_geneContent
cd 05_geneContent
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





## CRISPRs


## Composition
