---
title: "Block 3 - COOI: Recent ploidy changes in the Saccharomycotina (part 2)"
start: false
teaching: 0
exercises: 60

questions:
- How can you determine changes in ploidy using next-generation sequencing data?

objectives:
- Generate k-mer profiles from short-read data
- Analyze and interprete k-mer profiles to estimate genome size, heterozygocity, repeat content
- Analyze and interprete k-mer profiles to estimate ploidies
- Generate allele frequency plots from short-read data
- Analyze and interprete allele frequencies to estimate ploidies

keypoints:
- You can work with next-generation sequencing data
- You can detect (recent) ploidy changes in fungi using next-generation sequencing data 
---

## 2. Identify ploidy variation using k-mer frequencies

Signatures of recent ploidy change can be inferred from next-generation sequencing data by examining k-mer distributions. When interpreting k-mer profiles directly, one needs to have a intuition about the ploidy. For example, a third peak in a k-mer profile could point to repeats as well to a triploid species. Computational approaches such as [smudgeplot](https://pubmed.ncbi.nlm.nih.gov/32188846/) (see lecture) can estimate the ploidy and genome structure of a genome by analyzing heterozygous k-mer pairs that can be directly found in the sequencing data.

We will first analyse the smudgeplot based on the k-mer data generared for *S. cerevisiae* strains CBS7837. To be able to analyze heterozygous k-mers, we first need to extract these heterozygous k-mers from the `jellyfish count` data we have generated in the previous episode.

We can extract k-mers using `jellyfish dump` and we need to provide both upper and lower count cutoff to excude low and high frequency kmers. The resulting k-mers can be directly provided to smudgeplot.py hetkmers to compute the set of heterozygous k-mer pairs.

However, we first need to change to a smudgeplot environment since it runs in a different python version. This is done with the `smudgeplot` command. You can at any moment return to your default environment using `python2.7`

~~~
$ smudgeplot
$ jellyfish dump -c -L 10 -U 1000 ScereCBS7837.jf | smudgeplot.py hetkmers -o ScereCBS7837_kmer_pairs
~~~
{: .bash}

> ## Exercise
>
> Now that we generated the heterozygous k-mers pairs (*_coverages.tsv), we can now use s`mudgeplot plot` to visualize the data.
> - What does the x- and y-axis display? What is shown by the color gradient?
> - What is the suggested ploidy based on the smudgeplot results?
>
>> ## Solution
>> `smudgeplot.py plot ScereCBS7837_kmer_pairs_coverages.tsv -o ScereCBS7837`
>> 
>> The x-axis shows the minor k-mer coverage (ratio of minor allele frequency over the total allele count; for diplaid one would expect 0.5 : 0.5). The y-axis is the k-mer coverage (based on 1n (monoploid) coverage, the coverage is estimated based on the data). The color gradient shows the number of k-mer pairs with this particular minor k-mer coverage (x-axis) and k-coverage (y-axis).
>>
>> The suggested ploidy is diploid with a normalized minor kmer coverage of 0.5 and peak of data around a 2n coverage based on a 1n of 41.
>> 
> {: .solution}
{: .challenge}

We can now generate similar smudgeplots for another *S. cerevisiae* strain and analyze its ploidy. Localize the short-read sequencing data from *S. cerevisiae strains CBS2919* at the data storeage folder.
As explained in the previous episodes, use `jellyfish` to generate the k-mer counts. Then use **a.** `genomescope` to determine the genome size and visualize the k-mer distribution and **b.** `jellyfish dump` together with `smudgeplot hetkmers` to extract k-mer pairs, and visualize these with `smudgeplot plot`. 

> ## Exercise
>
> What is the suggested ploidy based on the smudgeplot result?
>
>> ## Solution
>>
>> The suggested ploidy based on smudgeplot is triploid where the minor allele frequency is occuring at a 1/3 ratio and the total coverage of the k-mer pair is at 3n based on a 1n coverage of 27.
>> In the GenomeScope analysis, the first peak of the k-mer distribution is mistaken for an error peak and thus the peak detection and genome size estimate is likely off. Instead of suggesting a 3n it seems like a diploid strain without any heterozygous peak. This example highlights shows that knowledge of ploidy is important prior analysing k-mer distributions.
>>
> {: .solution}
{: .challenge}

