---
title: "COO-III-4. Genome composition and optimal growth temperature"
start: false
teaching: 0
exercises: 30
questions:
- How do genomes evolve under different environmental adaptations?
objectives:
- Explore protein composition adaptation to high temperatures 
---



## Protein compositions

Organisms that live at high temperatures require different genome and protein composition to withstand these conditions. Here, we will
analyse the inferred proteome composition of these genomes and characterise their differences.

~~~
cd ~/GenomeBioinformatics/Block1/COO-III
mkdir 08_composition
cd 08_composition
ln -s ../../01_genomeData/bin.3/*faa .
ln -s ../../01_genomeData/heim/prokka*/*faa .
rename GCA heim_GCA GCA*
ln -s ../01_genomeData/pangui/prokka_GCA_0*/*faa .
rename GCA pangui_GCA GCA*
ln -s ../04_genomeDensity/col_prokka/prokka_Col_02*/*faa .
~~~
{: .bash}

Now explore two different tools to visualise amino acid frequences:
~~~
~/data_bb3bcg20/bin/scripts/aa_freq_plot.sh bin.3.faa
~~~
{: .bash}

> ## Exercise: Visualise changes in proteome composition
>
> Can you see any general trends in amino acid frequency changes?
>
> `for f in Col*faa; do ~/data_bb3bcg20/bin/scripts/aa_freq_plot.sh $f; done`
>
> `for f in heim*faa; do ~/data_bb3bcg20/bin/scripts/aa_freq_plot.sh $f; done`
>
> `for f in pangui*faa; do ~/data_bb3bcg20/bin/scripts/aa_freq_plot.sh $f; done`
>
> `mkdir aa_freqs; mv *txt aa_freqs/`
{: .challenge}

It may be very difficult to visualise when confronting all amino acids at the same time, but some of the amino acid categories 
that are most affected by adaptations to higher temperatures are Charged amino acids (both positive and negative) and Polar
amino acids. Let's focus our analyses on these.

> ## Exercise: How do charged and polar amino acid compositions change under different temperatures?
>
> `for f in *faa; do ~/data_bb3bcg20/bin/scripts/aa_chargedpolar_plot4.sh $f; done`
>
> `mkdir Charged_polar`
>
> `mv *polar Charged_polar/; mv *charged Charged_polar/; mv *ratio.txt Charged_polar`
> 
>> ## Solution
>>
>> `for group in bin.3 Col heim pangui; do echo $group; perl ~/data_bb3bcg20/bin/scripts/stats.pl <(cat Charged_polar/$group*_ratio.txt); echo; done`
>>
>> Generally, charged amino acids are more common in thermophile genomes, while polar amino acids are less used.
>> 
> {: .solution}
{: .challenge}


