---
title: "Block 3 - COOII: Large-scale chromosomal variation in Saccharomyces cerevisiae (part 2)"
start: false
teaching: 0
exercises: 60

questions:
- How can you we identify large-scale genomic differences by directly comparing genome assemblies?

objectives:
- Perform whole-genome alignments, both between different genome assemblies as well as between different strains of the same species.
- Interpret whole-genome alignments to identify miss-assemblies as well as structural varients;

keypoints:
- You can work with and compare genome assemblies.  
- You can detect large-scale genomic differences by interpreting whole-genome comparisions.
---

# Large-scale chromosomal variation in *Saccharomyces cerevisiae*
Genome sequences of individuals of the same species can differ significantly, e.g. two humans are expected to differ at 0.5% of the nucleotides. Next to these single nucleotide differences, genomes can differ by larger structural variations such as translocations, inversions, duplications, or deletions. Mobile genetic element (transposon) insertions and deletions can further establish genetic diversity. Collectively, these large-scale variations often affect hundreds of nucleotides can significantly impact the phenotype.

Bakers’ yeast has been traditionally used as a model to study how genotypic variations is mechanistically established. The availability of sequencing data of hundreds of different yeast isolates enables us to study the emergence and consequences of structural variation in detail. We here will work with assembled genome sequences (you have learnt how genomes are assembled in Block 1).

## Comparision between two *S. cerevisiae* strains

Thus far, we compared two genome assembly versions of the same *S. cerevisiae*. In this excercise, we will focus on the comparision between genome sequences of two different *S. cerevisiae* strains, the reference strain S288C that we have analyzed before and the strain UWOPS03-461.4, which has been isolated in the rain forest in Bertram palm in Malaysia.

Localize the long-read genome assemblies of *S. cerevisiae* strain UWOPS03-461.4 (Yue2017_UWOPS034614.genome.fa) at the data folder and create a symbolic link to your working folder.

We will study the structural similarity of these two strains by using MUMMER (`nucmer`) to generate whole-genome alignments.

> ## Exercise
>
> Generate a whole-genome alignment with `nucmer` that retains all matches using the S288C genome assembly as the reference and the UWOPS03-461.4 genome assembly as the query. Then use `mummerplot` to visualize the similarities between the two *S. cerevisiae* strains. Visualise the alignment with --color and store the output as a .png file; details how to perform these analyses can by found in the previous episode. Address the following questions:
> - What patterns do you oberserve (in general) when inspecting the alignment?
> - Are these what you would expect when comparing different strains of the same species?
> - What type of structural variation can you observe on chromosome VII, and how does this relate to the observation by [Yue and colleagues](https://www.nature.com/articles/ng.3847)?
> - What could be biological consequences of the large-scale chromosomal rearrangements?
>> ## Solution
>> ~~~
>> $ nucmer --maxmatch -p S288CvUWOPS034614 Yue2017_S288C.genome.fa Yue2017_UWOPS034614.genome.fa
>> $ mummerplot --png --color S288CvUWOPS034614.delta
>> ~~~
>> {: .bash}
>> The genome alignment is overall still largely co-linear with most chromosomes being conserved. This would be expected from strain of the same species. Few chromosomes (e.g chromosome VII) display clear signs of translocations and way more deletions/insertions. Dfferences in karyotypes (chromosomes) can lead to reproductive isolation and slowly into formation of new species (e.g., https://onlinelibrary-wiley-com.proxy.library.uu.nl/doi/full/10.1111/j.1365-294X.2011.05005.x). However, large-scale translocations are commonly observed in fungi.
> {: .solution}
{: .challenge}


