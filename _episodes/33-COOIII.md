---
title: "COO-III-3. Explore genome architectural features related to niche adaptation"
start: false
teaching: 0
exercises: 90
questions:
- How do genomes evolve under different environemntal adaptations?
objectives:
- Get familiar with some general genome characteristics related to ecological adaptation
---

## Genome size

A common feature of thermophiles is smaller, streamlined genomes. Let's ask how genome size has evolved in these organisms.

~~~
cd ~/GenomeBioinformatics/Block1/COO-III/
mkdir 03_genomeSize
cd 03_genomeSize
ln -s ../01_genomeData/bin.3/*fna .
ln -s ../01_genomeData/fromCollaborator/*fna .
ln -s ../01_genomeData/heim/*fna .
rename GCA_ heim_GCA_ GCA*fna
ln -s ../01_genomeData/pangui/*fna .
rename GCA_ pangui_GCA_ GCA*fna
~~~
{: .bash}

This first code just generates links to the fna files. Additionally, we are renaming the files from Heimdallarchaea and 
Panguiarchaea to be able to distinguish them. It is important to run the code in this particular sequence to avoid renaming the
genomes incorrectly. Now we can calculate genome sizes.

~~~
grep -v ">" bin.3.fna | tr -d "\n" | wc -m > bin.3.genomeSize
for f in Col_*.fna; do grep -v ">" $f | tr -d "\n" | wc -m ; done > Col.genomeSize
for f in heim_*.fna; do grep -v ">" $f | tr -d "\n" | wc -m ; done > heim.genomeSize
for f in pangui_*.fna; do grep -v ">" $f | tr -d "\n" | wc -m ; done > pangui.genomeSize
~~~
{: .bash}

> ## Exercise: How has genome size evolved?
>
> Use again the stats.pl file to calculate general statistics
>
>> ## Solution
>>
>> `for f in *.genomeSize; do echo $f; perl ~/data_bb3bcg20/bin/scripts/stats.pl $f ; echo; done`
>>
>> The Panguiarchaea genomes have the smallest genomes in this set (1.2-1.4 Mb), closely followed by our collaborator's genomes
>> (ca. 1.5-2.0 Mb). Our bin is much larger than the genomes of these thermophiles (3 Mb), but still generally smaller than
>> the genome of the mesophilic Heimdallarchaea (ca. 3.1-4.0 Mb).
>> 
> {: .solution}
{: .challenge}


## Genome density comparison

Often, genome density also varies between mesophiles and thermophiles, the latter being more gene-dense than the former. Let's
calculate gneome density in our genome set:
~~~
cd ~/GenomeBioinformatics/Block1/COO-III/
mkdir 04_genomeDensity
ln -s ../01_genomeData/heim/prokka_GCA_0*/*gff .
rename GCA_ heim_GCA_ GCA*
ln -s ../01_genomeData/pangui/prokka_GCA_0*/*gff .
rename GCA_ pangui_GCA_ GCA*
ln -s ../01_genomeData/bin.3/bin.3.gff .
~~~
{: .bash}

In the case of the genomes of our collaborators, we still don't have GFF files that we can easily use for this purpose. Let's use
prokka to annotate them first
~~~
mkdir col_prokka
cd col_prokka
ln -s ../../01_genomeData/fromCollaborator/Col_02*fna .
for f in *fna; do prokka_ed --kingdom Archaea --metagenome --outdir prokka_${f%.fna} --locustag ${f%.fna} --prefix ${f%.fna} --cpus 4 --quiet  $f; done
cd ..
ln -s col_prokka/prokka_Col_02*/*gff .
~~~
{: .bash}


> ## Exercise: How has genome density evolved?
>
> Use the quick tool '~/data_bb3bcg20/bin/scripts/calculateGenomeDensity.sh' to obtain the values we want.
>
>> ## Solution
>>
>> `~/data_bb3bcg20/bin/scripts/calculateGenomeDensity.sh bin.3.gff`
>>
>> To calculate for all genomes:
>>
>> `for group in bin.3 Col heim pangui; do for f in $group*gff; do ~/data_bb3bcg20/bin/scripts/calculateGenomeDensity.sh $f; done | grep fraction | sed 's/.* //' > $group.codingDensity; done`
>>
>> Now we can summarise these values:
>>
>> `for f in *codingDensity; do echo $f; perl ~/data_bb3bcg20/bin/scripts/stats.pl $f ; echo; done`
>> 
>> Here, the values are much less distinct than in our previous genome size analysis, but we can still observe a trend by which
>> organisms with higher optimal growth temperatures have slightly higher genome densities.
>> 
> {: .solution}
{: .challenge}

## Gene families related to heat shock, DNA repair and stress

Thermophiles need to survive harsh conditions that can deteriorate their DNA and proteins, or affect the activity of key biological
processes. Therefore, they often acquire key genes via horizontal gene transfer.


## CRISPRs


## Composition
