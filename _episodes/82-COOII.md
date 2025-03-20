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
> Take a look the parameters nucmer and take a consult the manual to find the explanation of the prarameters `nucmer -h`.
> - What is the difference between --mum, --mumreference, and --maxmatch options?
> - What could be the impact of the different options when interpreting the genome alignments?
>
>> ## Solution
>> `--mum` will only find maximum unique matches that are unique in the reference and in the query, this is the original definition of mummer from 1999. `--mumreference` will return matches unqiue in the reference sequence only (that is the default) and `--maxmatch` will return matches irrespective of their uniqueness in both reference and query. Thus, `--mum` and `--mumreference` will only return matches if they are unique, i.e., if duplications or other repetetive elements (such as transposons) occur, the alignments between these regions are not reported.
>>
> {: .solution}
{: .challenge}

We can now execute a `nucmer` command to calculate a whole-genome alignment using the S288C original reference genome (S288C.genome.fa) as the reference and the S288C long-read assembly (Yue2017_S288C.genome.fa) as a query. We retained all possible matches (`--maxmatch`) between the query and the reference and provided a prefix to give your results a clear name `-p`.

~~~
$ nucmer --maxmatch -p S288CvS288Cpb S288C.genome.fa Yue2017_S288C.genome.fa
~~~
{: .bash}

In a second step, we can now visually inspect the whole-genome alignment using `mummerplot`. `mummerplot` generates a dotplot to visualize the similarity between genomic regions; genomic regions that are similar when comparing the two genomes are shown by a dot. This dotplot allows to rapidly visualize large-scale differences between the two genomes (e.g. deletions or insertions) as well as chromosomal rearrangements (e.g. translocations or inversions).

First, note the parameters of `mummerplot`, and check what input file `mummerplot` expects and how you can modify the output visualization, e.g., via `--color`. 

~~~
$ mummerplot --color S288CvS288Cpb.delta --png
~~~
{: .bash}

> ## Exercise
>
> Transfer a copy of the file to your own machine. What do you oberserve (in general) when you look at the figure. Is this according to your expectations (remember what genome sequences did you align)?
>
>> ## Solution
>>
>> The alignment displays overall very high co-linearity (order and content is conserved) and no major differences can be observed. This is expected as two assemblies of the same yeast strains are compared. There are some obvious differences at the chromosomal ends, likely telomeres, which are typically complex and very challanging to assemble (Do you know why?)
>> 
> {: .solution}
{: .challenge}

We can also generate a zoom-in of the alignments of chromosome III of the two S288C assemblies by running mummerplot with the option `-r` and `-q` to only display the alignment chromosome III of the reference and query, respectively. First, think how can you can easily find out how the chromosomes are named?

> ## Exercise
>
> -  What type of structural variation can you you observe on chromosome III?
> -  How does this relate to the observation in the paper by [Yue and colleagues](https://www.nature.com/articles/ng.3847)?
> -  What are *Ty* elements, and what could be an reason for their absence in the S288C reference genome assembly?
>
>> ## Solution
>>
>> `mummerplot --color --png S288CvS288Cpb.delta -r "chrIII" -q "chrIII"`
>> On chromosome 3, there seem to to be insertions in the long-read assembly that are absent in the reference genome assembly. The paper by Yue and colleagues report on the absence of *Ty* elements in the reference assembly. *Ty* elements are retrotransposons, a type of transposons, that occur in high abnudance in the yeast genomes (~2% of the genome are formed by *Ty* elements). Repetetive elements such as transposons are very challanging to assemble due to their length and repetetive nature. Thus, these were likely not assembled in the inital genome assembly that was based on shorter sequencing reads, but can now be assembled using long-read sequencing data.
>>
> {: .solution}
{: .challenge}

Next to the visual inspection using `mummerplot`, whole-genome alignments from `nucmer` can also be systematically analysed to identify regions that are present or absent in the query or reference. To obtain the genomic coordinates of the alignments, we can use the `show-coords` command. First, take a look at the command line options and identify what input `show-coords` expects. The output of `show-coords` is a tiny bit more complex but the individual columns inform on the start and end positions, the length, sequence identify, and chromosome names of each aligned segments between the query and the reference genome. `show-coords` also allows the user to sort alingmnets by query/reference positions or filter them by length and/or identity. This can be useful to only only obtain highly similar regions, for instance in the case of the alignment between two divergent genomes, or to only keep alignments that have a minimum length, thereby removing multiple repetetive matches that might be present due to `nucmer`'s `--maxmatch` option.

To be able to efficentily use the alignment coordinates, we need to convert the coordinates into a bed file. [Bed files](https://en.wikipedia.org/wiki/BED_(file_format)) are often used in bioinformatics to represent genomic features relative to their genomic regions, e.g. location of genes but also the localization of the genome alignment. In contrast to many other genome-based representations, bed files are in a 0-based coordinate system while most others are in a 1-based coordinate system. The 1-based format is similar to our normal counting (1-based fully closed), e.g., counting the number of characters in a string directly. The bed format is 0-based (half open), and thus counts between the characters. Thus, the first element in a 0-based sequence has the start position 0 and the end position 1. This allows to more easily calculate the length of objects (0-based: end - start) rather than (1-based: end - start + 1). 

We now need to prepare and execute a command that uses `show-coords` to retrieve a list or query sorted alignments (query chromosome, query start, and query end), and subsequently use a combination of command line commands (`cut`, `awk`, etc.) to generate a bed file that contains the information of the alignment positions in the query sequence. Moreover, the start position of a feature in a bed file needs to be always smaller than the end position. Specifically, we remove the header from the coordinate file and seperate it via tabs, we then obtain the 3rd, 4th, and 9th column to get the query start, end, and chromosome name and the use `awk` to reformat these information into the correct order (query chromosome, query start, and query end), enrure that the start coordinate is always smaller than the end coordinate and substract 1 to change from a 1-based to a 0-based coordinate system. 

~~~
$ show-coords -q S288CvS288Cp.delta -T -H | cut -f3,4,9 | awk '{if($1 < $2){print $3,$1-1,$2}else{print $3,$2-1,$1}}' | tr " " "\t" > Yue2017_S288C.bed
~~~
{: .bash}

The resulting bed files of the alignment coordinates can be used to rapidly identify regions that are present or absent in the alignment. Since we obtained a query-sorted bed file containing the coordinates in the query sequence, we can thus determine if the entire query sequence was covered with an alignment to the reference genome sequence, i.e., were these regions also present in the reference or are there regions in the query that are absent from the reference. 

We will make use of [`bedtools`](https://bedtools.readthedocs.io/en/latest/), which is a suite of popular tools to analyse bedfiles. We will use `bedtools coverage` to determine which regions are not covered by the alignment and consequently are absent in the reference and present in the long-read genome assembly. `bedtools genomecov` does need the bed file as well as information about the chromosome length. We can generate such afile based on the genome assembly of the query using `faSize -detailed`, which will report the length of every single chromosome/contig in the fasta file.

~~~
$ faSize -detailed Yue2017_S288C.genome.fa > Yue2017_S288C.genome.size
~~~
{: .bash}

We can now finally prepare and execute an `bedtools genomecov` command to determine the coverage in the query genome; note: redirect the output into a new file to ease subsequent analyses.

> ## Exercise
>
> - Take a look at the output file. How many bases in the query genome assembly are (not) covered?
> - What could be the biological or technical reason that some regions absent in the genome?
> - What could be the biological reason that some regions in the genome are covered more than once?
>
>> ## Solution
>>
>> - In total, ~17kb of the query genome assembly are not covered. This can be easily seen by looking at the amount bases that are covered 0 times.
>> - The reasons can be both technical and biological: Lineage-specific genes or regions could be absent from this assembly version, even thought the same strain was sequenced. Prolonged cultivation or independent cultivation in different labs can lead to divergence between strains that 'should' be identical. Moreover, transposons or other repeats are challanging to assemble and thus might be absent from the assembly
>> - Multiple alignments indicate regions in the genone that occur several times. That could be duplications or repetetive regions such as transposons.
>>
> {: .solution}
{: .challenge}

Using `bedtools genomecov`, we can also easily identify for each individual base in the query genome if it was covered by an alignment to the reference. Conversely, we can also identify if a base in the query genome was not covered and turn these information into a bed file that contains coordinates for uncovered regions.

~~~
$ bedtools genomecov -g Yue2017_S288C.genome.size -i Yue2017_S288C.bed -d | awk '{if ($3 == 0){print $1,$2,$2}}' | tr " " "\t" | bedtools merge -i - > Yue2017_S288C.uncovered.bed
~~~
{: .bash}

The command above uses `bedtools genomecov -d` to report the depth for each position in the genome rather than the default option that summarizes how much of the genome/chromosome is covered x times. This informatioed is then piped (`|`) into `awk` to only keep lines where the coverage is 0, i.e., these regions are present in the query genome but are not covered by an alignment to a region in the reference genome and thus are absent. We then print the chromosome name, the start, and the end. We then need to substitute spaces with tabs to generate a valid bed output. This output is directed to bedtools merge which merges overlapping and successive regions into bigger regions. The -i - indicates that the data is read directly from the pipe rather than from an input file.

> ## Excercise
>
> Do you find the regions in chromosome III (see above) that are absent? Can you provide a possible explanation for this observation?
>
>> ## Solution
>>
>> The regions on chromosome III are actually covered. Repetetive elements overlapping these regions are localized at different regions in the genome. Since we used `--maxmatch` to compute the alignments, also matches other than those between chrIII and chrIII are reported. This can also nicely be visualized using mummerplot and zooming into chrIII of the query while showing all alignments of the reference genome assembly.
>>
> {: .solution}
{: .challenge}

We now specifically obtain the coordinates of regions on chromosome III of the query genome that are not covered by alignments anchored on chromosome III of the reference genome. We will use a modification of the previous code (see above) but adding a grep command that will specifically take out alignments between chrIII of the query and the reference. This then allows us to determine how many regions are specific on chromosome III, and to see their sizes?

~~~
$ show-coords -q S288CvS288Cp.delta -T -H | grep -P "chrIII\tchrIII" | cut -f3,4,9 | awk '{if($1 < $2){print $3,$1-1,$2}else{print $3,$2-1,$1}}' | tr " " "\t" >  Yue2017_S288C.chrIII.bed

$ bedtools genomecov -g Yue2017_S288C.genome.size -i Yue2017_S288C.chrIII.bed -d | awk '{if ($3 == 0){print $1,$2,$2}}' | tr " " "\t" | bedtools merge -i - | grep "chrIII"
~~~
{: .bash}

The genome annotation of S288C from Yue and colleagues (long-read) is available as a [gff file](https://en.wikipedia.org/wiki/General_feature_format), which is a common file format typically used to store genome annoation. A gff file contains much information that is currently not useful for us. However, a simple command line approach allows us to convert the gff file (1-based starts) to a (0-based) bedfile that only contains the information that is essential to inform us on the localization and the type of feature.


~~~
$ cut -f1,4,5,9 Yue2017_S288C.features.gff3  | awk '{print $1,$2-1,$3,$4}' | tr " " "\t" > Yue2017_S288C.features.bed
~~~
{: .bash}

We can then use `bedtools intersect` to identify which features are localized in the regions on chromosome III that are present in the long-read assemby but absent in the S288C reference genome. Note the different parameters of bedtools intersect command and prepare and execute a command.

~~~
#get the features in bed format (See above)
$ cut -f1,4,5,9 Yue2017_S288C.features.gff3  | awk '{print $1,$2-1,$3,$4}' | tr " " "\t" > Yue2017_S288C.features.bed
#get the coverage (as above) and store in a file
$ bedtools genomecov -g Yue2017_S288C.genome.size -i Yue2017_S288C.chrIII.bed -d | awk '{if ($3 == 0){print $1,$2,$2}}' | tr " " "\t" | bedtools merge -i - | grep "chrIII" > Yue2017_S288C.chrIII.uncovered.bed 
#get the intersections between features and missing regions
$ bedtools intersect -a Yue2017_S288C.features.bed -b  Yue2017_S288C.chrIII.uncovered.bed 
~~~
{: .bash}

> ## Excercise
>
> How many features overlap and what type of feature overlap? Does your finding confirm the previous observations by [Yue and colleagues](https://www.nature.com/articles/ng.3847)?
>
>> ## Solution
>>
>> In total, five genomic features overlap with the regions lacking the int reference genome. These are all *Ty* elements as reported by Yue and colleagues in their paper.
>>
> {: .solution}
{: .challenge}


