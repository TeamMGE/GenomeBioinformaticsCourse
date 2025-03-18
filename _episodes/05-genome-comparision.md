---
title: "Genome comparisons"
teaching: 0
exercises: 135
questions:
- How can we compare two genome assemblies?
- How can we interpret whole-genome alignments to identify large-scale genomic differences? 
objectives:
- Compare two genome assemblies using MUMMER.
- Visually inspect the results of whole-genome comparisons with the focus to describe differences in chromosome numbers and large-scale structural variations.
- Make use of whole-genome alignments to identify isolate-/species-specific genomic regions.
keypoints:
- You can compare two eukaryotic genomes with MUMMER
- You can critically reflect on the results of MUMMER and provide a technical and biological interpretation of the visual representations.
- You can combine the learnt methodologies to conceptualise a computational strategy to identify isolate-/species-specific genomic regions.
---

#  Genome comparisons
## Genome alignments
In the previous exercises, we have focussed on assessing the quality and completness of eukaryotic genome assemblies. 

We now want to move a step further by trying to identify meaningful biological differences between the genomes of two isolates and/or species.

We will do this by generating, analysing, and visualising nucleotide-level whole-genome alignments (WGA). WGA are typically used to study the evolution of genomes. They can be used to detect evolutionary conserved elements but also chromosomal rearrangements. They can also be used to study the progress in genome sequence assembly by comparing newer assemblies to existing ones.

We will generate WGAs using the [MUMMER](http://mummer.sourceforge.net/)package. MUMMER comes with several useful tools to generate and visualize WGAs.

Depending on the expected similarity between species and/or strains, you can either perform a nucleotide-based alignment (with `nucmer`) or a translated nucleotide-based alignment (`promer`). In the latter, the nucleotide sequence is first translated in all six frames and then amino acids sequences are aligned. Consequently, `nucmer` works best for closely related species and/or strains, whereas `promer` works best for more distantly related species/strains as the amino acid sequence is better conserved.

We can first run `nucmer` to obtain an overview of the potential parameter and to help us to understand its usage. Take a look at the parameters and also consult the [manual](https://mummer4.github.io/manual/manual.html) to find the explanation about the parameters used.

~~~
$ nucmer -h
~~~
{: .bash}

> ## Exercise
> 
> What is the differences between the `--mum`, `--mumreference`, and `--maxmatch` options?
>
>> ## Solution
>> 
>> `mum` will only find maximum unique matches that are unique in the reference and in the query, this is the original definition of mummer from 1999. `mumreference` will return matches unique in the reference sequence only (that is the **default** setting) and `maxmatch` will return matches irrespective of their uniqueness in both reference and query.
>>
> {: .solution}
{: .challenge}

We now want to perform a WGA between the *Z. tritici* IPO323 reference genome assembly and one other *Zymoseptoria* genome. Take one of the genome assemblies you previously identified to be of high quality, ideally chromosomal-scale. The genome assemblies can be found in **~/data/fungalgenomics_seidl/assemblies/**. 

~~~
$ nucmer -p IPO323v3D7 --maxmatch -t 2 IPO323.fna ST99CH_3D7.fna 
~~~
{: .bash}

Here we use `nucmer` with the `--maxmatch` setting. The `-p` option provides a prefix to the output files and the `-t 2` option indicates that we will use two threads. If you do not specify this option, all outputfiles will have the prefix `out`. If you run `nucmer` multiple times, you will therefore overwrite the output files. Depending on the settings, `nucmer` might take few minutes to run successfully, so please be patient. 

The alignment will be stored in a file called `.delta` which contains the information on all identified alignments between and within the two sequences. This format is not easily human readable, but we can make use of one of the support tools of MUMMER to visualize the alignments.

~~~
$ show-coords IPO323v3D7.delta | head
~~~
{: .bash}

The output is normally written to the stdout (command line) but we only now look into the first 10 lines (`head`). The output is slightly different depending on the type of alignment, i.e. nucleotide or amino acid; for example, some of the described columns, such as percent similarity, will not appear for nucleotide comparisons. 

For now, we will only perform nucleotide alignments. For nucleotide alignments, the columns describe the start and the end of the alignment region in the reference sequence, the start and the end of the alignment region in the query sequence, length of the alignment region in the reference and query sequence, the percent identity of the alignment, and lastly, the names of the reference and query sequence (e.g., chromosome or contig name/id) are shown. All output coordinates and lengths are relative to the forward strand of the reference DNA sequence. Every line in the output represents one alignment region that has been identified by `nucmer`.

As you can see, the alignments are short and there are a lot of alignments in the output; you can count the number of lines (with `wc`) to get an idea about the number of alignments found. What would you expect would happen if you would have run the alignment with the default setting or with `--mum`? Would you expect more or less alignments?

We can filter the alignment to only retain longer alignments and those of high identity. MUMMER provides a tool called `delta-filter` that enables you to perform filtering on the delta file.

~~~
$ delta-filter -i 95 -l 10000 IPO323v3D7.delta > IPO323v3D7.l10ki95.delta
~~~
{: .bash}

This will filter your delta file to only retain alignments longer than 10kb and with more than 95% identity. 

Depending on the expected similarity or differences between the two genomes you align, filtering will have a dramatic impact on the results. For example, different yet still related fungal species often have <80% identity, and consequently filtering any alignments might remove most of them. 

Next to looking into the alignments via `show-coords`, we can also visually inspect the WGA using `mummerplot`. `mummerplot` generates a dotplot to visualize the similarity between genomic regions; regions that are similar when comparing the two genomes are shown by a dot or line. This dotplot allows to rapidly visualize differences between the genome content (e.g. deletions or insertions) as well as chromosomal rearrangements (e.g. translocations or insertions).

~~~
$ mummerplot --color --png IPO323v3D7.l10ki95.delta
~~~
{: .bash}

The `--png` option is important to tell `mummerplot` to generate a png file. The `--color` option colors the alignment based on sequence identity rather than based on forward and reverse alignment (default), and is generally better to interpret.

To display the alignment, copy the file from the remote server to your own computer. Then open the `.png` file on your own computer. 

> ## Exercise
> 
> Provide a general description of the alignment that you generated. What would you conclude from it (and what not?). Again, carefully read the manual page! Think about co-linearity, possible rearrangements, percentage identity, etc.
>
>> ## Solution
>> 
>> In the case of the IPO323 vs. 3D7 alignment, we see overall very high levels of co-linearity without major rearrangements. The sequence identity is overall very high, as one would expect of different strains of the same species. It is interesting to note that some chromosomes of IPO323 reference strain are uncovered by alignments from 3D7. If you remember, this is however, not too surprising as Z. tritici is known to contain multiple dispensable regions, obviously 3D7 misses some of these chromosomes
>>
> {: .solution}
{: .challenge}

`mummerplot` has options to further impact the visualisation. For example `--fat` re-orients the sequences (re-order the contigs are changes the orientation) to maximize the overall diagonal in the plot, which can make the visualisation more appealing. If you have time, redo the `mummerplot` using the `--fat` option.

To visually inspect specific regions in the alignment, you can generate a zoom-in of the alignments of specific chromosomes/contigs by running `mummerplot` with the option `-r` and `-q` to only display the alignment of specific regions in the reference and query, respectively. 

~~~
$ mummerplot --color --png IPO323v3D7.l10ki95.delta -r "IPO323#1#chr1"
~~~
{: .bash}

Focus on the alignment of one core chromosome and one dispensable chromosome. What are the differences? 

> ## Exercise
> 
> A recent manuscript by [Baudet and colleagues](https://bmcbiol.biomedcentral.com/articles/10.1186/s12915-020-0744-3) displays an insertion/deletion on chromosome 3 between IPO323 and 3D7. Based on the whole-genome alignments, are you able to recover this structural variation?
>
>> ## Solution
>> 
>> The insertion/deletion is very visible in the whole-genome alignment. It seems the region is very complex region that is characterised by many transposable elements, see figure 3e from the paper.
>>
> {: .solution}
{: .challenge}

## Identification of shared and unique genomic regions in genome assemblies
Next to the visual inspection using `mummerplot`, whole\-genome alignments from `nucmer` can also be systematically analysed to identify regions that are present or absent in the query or reference. These would be regions present in either of the sequences not covered by an alignment.

We will make use of `show-coords` as it provides us with access to the genomic coordinates of alignments in both the query and the reference. 

To be able to efficiently use the alignment coordinates, we need to convert these into a [bed file](https://genome.ucsc.edu/FAQ/FAQformat.html). Bed files are often used in bioinformatics to represent genomic features relative to their genomic regions, e.g. location of genes, promoter sequences, SNPs. However, this feature could also be the localisation of the genome alignment. 

In contrast to many other genome-based representations, bed files are in a 0-based coordinate system while most others are in a 1-based coordinate system. The difference between 1-based starts and 0-based system is well explained [here](https://tidyomics.com/blog/2018/12/09/2018-12-09-the-devil-0-and-1-coordinate-system-in-genomics/).

We now need to execute a command that uses `show-coords` to retrieve a list or references sorted alignments (reference chromosome, reference start, and reference end; this are columns 1, 2 and eight from the show-coords output), and subsequently use a combination of command line commands (cut, awk, etc.) to generate a bed file that contains the information of the alignment positions in the reference sequence. It is important to note that the start position of a feature in a bed file needs to be always smaller than the end position, while MUMMER reports the coordinates in relation to the reference DNA. We therefore use `awk` to swap the start and the end position in the case that the start coordinate is larger than the end coordinate.

~~~
$ show-coords -r IPO323v3D7.l10ki95.delta -T -H | cut -f1,2,8 | awk '{if($1 < $2){print $3,$1-1,$2}else{print $3,$2-1,$1}}' | tr " " "\t" > IPO323.ref10k.bed
~~~
{: .bash}

The resulting bed files of the alignment coordinates can be used to rapidly identify regions that are either present or absent. Since we obtained a reference-sorted bed file, we can determine if the entire reference sequence (here IPO323) was covered with alignments to the query genome sequence.

We will make use of `bedtools`, which is a suite of popular tools to analyse bedfiles. We will use `bedtools genomecov` to determine which regions are not covered by the alignment and consequently are present in the reference but absent in the other strain.

> ## Exercise
> 
> What type of input does `bedtools genomecov` need next to the bed-file?
>
>> ## Solution
>> 
>> It needs information on the size of the individual chromosomes.
>>
> {: .solution}
{: .challenge}

We can generate such an overview using `faSize` but there are also alternatives such as using the command line or other tools.

~~~
$ faSize -detailed IPO323.fna > IPO323.size
~~~
{: .bash}

Using `bedtools genomecov`, we can now easily identify for each individual base in the reference genome if it was covered by an alignment to the query. Conversely, we can also identify if a base in the reference genome were not covered and turn these information into a bed file that contains coordinates for uncovered regions.

~~~
$ bedtools genomecov -g IPO323.size -i IPO323.ref10k.bed > IPO323.ref10k.cov
~~~
{: .bash}

> ## Exercise
> 
> Take a look at the output file. The output file shows for each chromosome (first column) how many nucleotides (third column) are covered by the feature in the bed file. In our case, these are the alignments between the query and the reference genome assemblies. The file contains multiple rows for each chromosome to indicate how many nucleotides are covered by 0, 1, 2, or more alignments (2nd column). The fourth and fifth column tell you what percentage (5th column) of the total length of the chromosome (4th column) are covered. The last lines in the file summarise these measurements over the entire genome
> - How many bases in the reference genome assembly are (not) covered?
> - What could be the biological or technical reason that some regions being absent in the query genome but present in the reference genome?
> - What could be the biological reason that some regions in the genome are covered more than once?
>
>> ## Solution
>> 
>> A big proportion of the genome is present ~60% but ~40% is absent. This is likely due to both biological and technical reasons. First, we filtered shorter alignments (< 10 kb) and thus will overestimate strains-specific regions. Second, we know that Zymoseptoria has dispensable chromosomes. We can for instance see that chromosome 21 is absent from the other strain. These chromosomes and other dispensable regions contribute to the high number of nucleotides being absent.
>>
> {: .solution}
{: .challenge}

We now have identified the amount of genomic material that is unique to the reference genome. We can now think about additional computational experiments to identify i) identify the location of these regions, ii) detect protein-coding genes, and iii) functionally characterise these genes. For i), we can for example use `bedtools genomecov -d` to obtain the coordinates of nucleotides that are not covered. How to identify protein-coding genes and how to functionally characterise those will the subject of future exercises. 

{% include links.md %}
