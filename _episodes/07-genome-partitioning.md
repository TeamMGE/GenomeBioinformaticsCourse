---
title: "Genome partitioning"
teaching: 0
exercises: 60
questions:
- How can we identify homologous chromosomes between individuals/strains?
- How can we interpret the similarity between chromosomes and related these to biology? 
objectives:
- Determine all-vs-all chromosome-similarities between individuals/strains.
- Interpret the similarities between chromosome-networks.
- Visualize similarities between chromosomes.
keypoints:
- You can determine the similarities between chromosomes.
- You can critically evaluate a chromosome-network.
---

#  Genome partitioning
## Background
Pan-genome graphs can represent all possible alignments between a collections of sequences, both within a single strain or individual as well as between a collection of strains/individuals. However, we cannot expect to pairwise map all sequences together and obtain well separated connected components that might represent similarities between the homologous chromosomes of different strains/individuals. It is much more likely to get a giant connected component, likely due to shared sequences that occur on all chromosomes, e.g., telomeric repeats, centromers or other transposable elements. Moreover, there will be likely a few smaller ones, due to incorrect mappings or false homologies. This might unnecessarily increase the computational burden, especially for larger genomes as well as complicate the downstream analyses. Therefore, it is recommended to split the input sequences into homologous 'communities' in order to find the structure of their mutual relationship. For example, these homologous communities can represent the different chromosomes of the input genomes. However, if you know in advance that your sequences contain particular rearrangements, which are common in many fungi, you might consider skipping this step or tuning it accordingly to your biological questions.

## Community detection
We will analyse a set of six *Zymoseptoria tritici* strains that have been sequenced as part of a pan-genome [paper](https://bmcbiol.biomedcentral.com/articles/10.1186/s12915-020-0744-3). We assume that these genome assemblies are (near)chromosomal-level, and therefore represent an interesting dataset to build a pan-genome graph.

The genome assemblies are in **~/data/fungalgenomics_seidl/pangenome**. 

Before we can form these communities, it is useful to get an idea about the overall similarity between the chromosomes within and between these strains. First, we can determine the number of chromosomes/contigs in each of the assemblies.

~~~
$ grep -c ">" *fna
~~~
{: .bash}

As we can see, the IPO323 reference genome assembly has 21 chromosomes while few of the other strains deviate from this number (e.g., 3D7 has only 17 chromosomes/contigs). If you inspect the names of the individual contigs/chromosomes, we used the Pangenome Sequence Naming (PanSN-spec) schema. The names are build up by sample\_name#haplotype\_id#contig\_name, and therefore provide a unique and clear naming schema to differentiate sequences in a pan-genome.

We now need to obtain an estimate mutual relationship between the input assemblies in order to detect the underlying communities. We will therefore first combine the assemblies into a single fasta file and subsequently index the fasta file for further analyses.

~~~
$ cat *fna  > combined.fna
$ samtools faidx combined.fna 
~~~
{: .bash}

To estimate the distance of each input sequence to every other sequence in the set, we use [mash](https://mash.readthedocs.io/en/latest/). The `mash triangle` command outputs a lower-triangular distance matrix if all-vs-all comparisons between all chromosomes.

~~~
$ mash triangle combined.fna -s 10000 -i > combined.triangle.tsv
~~~
{: .bash}

> ## Exercise
> 
> Inspect combined.triangle.tsv, e.g., using `less`. What information does this file contain? Give a rough estimation (range) of the distances between chromosomes. If you focus on the first column, what is the most likely homologous sequence to ERS3651594#1#CACTHN010000001.1 in the IPO323 reference genome assembly.
>
>> ## Solution
>> 
>> The file contains the distance matrix of all sequences compared to all sequenced; note that only the lower half of the matrix is returned. The pair-wise distances are in the range of 0.16 - 0.25 between most chromosomes/contigs, indicating that these are rather different to each other. To obtain the maximum divergence between all pairwise comparisons, you can use
>> ~~~
>> $ sed 1,1d combined.triangle.tsv | tr '\t' '\n' | grep chr -v | LC_ALL=C sort -g -k 1nr | uniq | head -n 1
>> ~~~
>> {: .bash}
>>
>> The divergence between ERS3651594#1#CACTHN010000001.1 and most IPO323 chromosomes is in the range of 0.17 - 0.25, but chromosome 1 has a divergence of only 0.013, which indicates that these are much more similar that the other chromosomes. Thus, ERS3651594#1#CACTHN010000001.1 is most likely homologous to chromosome 1 of IPO323.
>>
> {: .solution}
{: .challenge}

Since we now established that most non-homologous chromosomes diverged for >10% and homologous chromosomes are much more similar, we can now automatically detect communities in our dataset. 

~~~
$ wfmash combined.fna -p 90 -t 4 -n 5 -m > combined.mapping.paf
~~~
{: .bash}

We set -p 90 as we expect a sequence divergence of between 10-20% between these assemblies. `-n 5` indicates the number of mappings to keep for each homologous region identified, set as the number of haplotypes (number of haploid samples in this example) minus 1.

We now project the PAF mappings (all-vs-all alignments) into a network format (an edge list). We will do this by using `paf2net.py`.

~~~
$ paf2net.py -p combined.mapping.paf
~~~
{: .bash}

This will generate three output files that contain the list of edges representing the pairs of sequences mapped in the PAF, is a list of edge weights (long and high estimated identity mappings have greater weight), and a file that describes he mapping of the id to the sequence name.

To finally identity the communities, you can execute
~~~
$ net2communities.py -e combined.mapping.paf.edges.list.txt -w combined.mapping.paf.edges.weights.txt -n combined.mapping.paf.vertices.id2name.txt --plot
~~~
{: .bash}

The `net2communities.py` script creates a set of *.community.*.txt files one for each of the communities detected. Each txt file lists the sequences that belong to the same community.

> ## Exercise
> 
> How many communities are generated? Is this the number of communities you would have expected based on your knowledge about *Z. tritici*? Tip: Consult the [paper](https://bmcbiol.biomedcentral.com/articles/10.1186/s12915-020-0744-3) and Figure 1 to identify the expected chromosomes per strain. Which communities are representative of a single chromosome, and which communities are containing more than one chromosome? Which communities lack chromosomes?
>
>> ## Solution
>> 
>> We generated in total 19 communities. The reference isolate IPO323 has 21 chromosomes, and thus we might have expected to find 21 communities. Since we identified less, this suggests that some chromosomes have been joint in a single community. In total, 16 communities have six sequences and thus seem to be conserved in this six strains. One community, with IPO323 chromosome 17, is absent in strain 1E4. Two additional communities consists of two sets of homologous chromosomes, chromosome 15 and 18 and chromosome 14 and 21, respectively. These communities do not have chromosomes in strain 3D7, and thus 3D7 lacks four dispensable chromosomes. 
>>
> {: .solution}
{: .challenge}

We observe two communities that have more than one chromosome, which could suggest chromosomal rearrangements or other reasons that some of these chromosomes are more similar to each other. To further investigate this, `net2communities.py` also generates a *.pdf file that visualises the chromosome-network. Copy this file to your local computer to examine the network. The nodes in the network are the chromosomes/contigs, and are coloured by community. The edges represent the mappings between contigs: the black ones indicate mappings between contig of the same community, while gray indicates links between different communities.

> ## Exercise
> 
> Based on these mappings, which chromosomes/contigs might be responsible for the two communities that have more than one homologous chromosome? How strong is the support that links these chromosomes?
>
>> ## Solution
>> 
>> For each community, there is only a single chromosome/contig that links the the two sets of otherwise highly similar (many links) homologous chromosomes together. The support is therefore not very strong that these communities are joined by a chromosomal rearrangement. Most likely, these links are due to repetitive elements (transposons) that create these mappings.
>>
> {: .solution}
{: .challenge}

Each community can now be analysed independently by `pggb` to build a pan-genome graph. First, we will need to generate a fasta file for each of the communities. We can execute:

~~~
$ seq 0 18 | while read i; do echo "community $i"; samtools faidx combined.fna $(cat combined.mapping.paf.edges.weights.txt.community.$i.txt) > combined.community.$i.fna; samtools faidx combined.community.$i.fna; done;
~~~
{: .bash}

This will generate a single fasta file for each community, which we can later use as input for `pggb`.

{% include links.md %}
