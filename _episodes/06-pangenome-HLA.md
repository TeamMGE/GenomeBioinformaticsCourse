---
title: "Introduction to pan-genome graphs"
teaching: 0
exercises: 90
questions:
- How can we build a pan-genome graph of multiple sequences?
- How can we interpret the basic features of a pan-genome graph? 
objectives:
- Create a pan-genome graph using pggb.
- Interpret the basic features of a pan-genome graph.
- Visualize a pan-genome graph and interpret the shape and basic features of the graph. 
keypoints:
- You can generate a pan-genome graph using pggb.
- You can critically evaluate technical and biological factors that can shape a pan-genome graph.
---

#  Pan-genome graphs
## Introduction to pan-genome graphs
Standard approaches to genome analysis typically relate sequences to a single linear reference genome. This is efficient but has a fundamental problem: Differences from this reference are hard to observe and describe. When we compared different  *Zymoseptoria tritici* isolates, we have seen that these differ for example in the number and type of dispensable chromosomes. Depending on the reference, these might be difficult to identify and compare across multiple strains. Pan-genomic methods allow us to relate all genomes or sequences in an analysis directly to each other. 

The variation graph data model describes the all-to-all alignment of many sequences as walks through a graph whose nodes are labeled with DNA sequences.

In this exercise, we will use the PanGenome Graph Builder [(pggb)](https://github.com/pangenome/pggb). The tutorial is inspired by a [pggb workship](https://github.com/pangenome/pggb-workshop). We will first focus on a single example of sequences to build pan-genome graphs and inspect them to understand how the method works and the effects of some of its key parameters.

`pggb` work by first performing an all-vs-all alignment to produce base-level alignments. Subsequently, these alignments are converted into a graph that is then filter to remove small matches. 

Before moving into more complex pan-genome graphs of *Zymoseptoria*, we will first focus on a more simple genome graph. The human leukocyte antigen (HLA) system is a complex of genes on chromosome 6 in human which encode cell-surface proteins responsible for the regulation of the immune system.

The DRB1 beta chain is a protein that in humans is encoded by the HLA-DRB1 gene. We have a fasta file of DRB1 genes (DRB1-3123.fa; the file is in **~/data/fungalgenomics_seidl/pangenome/**). 

Before we can generate the pan-genome graph, we will need to change the python version. We can do this by

~~~
$ python3.11
~~~
{: .bash}

We will now build a pan-genome graph from a collection of sequences of the DRB1-3123 gene. We first need to index the fasta file before we can run `pggb`. We will use `samtools faidx` to do that

~~~
$ samtools faidx DRB1-3123.fa
$ pggb -i DRB1-3123.fa -n 12 -t 2 -o DRB1_3123.1
~~~
{: .bash}

This command will invoke `pggb` with two threads (`-t 2`) and redirect the output to the DRB1\_3123.1 folder. The most important parameter at this stage is `-n 12` as it indicates the number of haplotypes in the file, and it is a parameter to indicate the number of mappings per segment. There are twelve sequences/haplotypes in the fasta file and therefore we set this parameter to 12. 

Take a look at the files in the DRB1\_3123.1 folder. We get a graph in GFA (*.gfa) and odgi (*.og) formats. These can be used downstream in many methods, for instance when mapping short-read data to a graph. You can visualize the GFA format graph with Bandage (for Mac or Linux), and use `odgi` directly on the *.gfa or *.og output.

We obtain a series of images that represent the pan-genome alignment. These are created with `odgi viz` (1D matrix) and `odgi layout` with `odgi draw` (2D graph drawings). You can transfer the *png files from the server to your local machine. Please note that the node order in the visualisations has been modified to enhance readability.

First, the 2D layout gives us an overview of the total alignment. For small graphs, we can look at the version that shows where specific paths go (*.draw\_multiqc.png). For larger ones, the *.draw.png result is usually more legible, but it lacks path information. Each coloured rectangle represents a node of a path. The node???s x-coordinates are on the x-axis and the y-coordinates are on the y-axis, respectively. A bubble indicates that here some paths have a diverging sequence or it can represent a repeat region.

We also get some 1D visualisations. These present the graph as a kind of matrix, where information is aggregated into bins. By default, the optimal bin size for visualisation is determined automatically for optimal visualisation but can be adjusted as a parameter in `odgi`. Across the x-axis we have nodes of the graph (scaled by length) and across the y-axis we have paths, or sequences, which have been embedded in the graph. This layout is capable of representing several kinds of information using color. The default associates a color with each path. There are also images that display the orientation of paths using two views. One shows the "position" of each path relative to the graph. It runs light to dark from 0 to path length. A similar view shows inverted regions of paths relative to the graph in red, while the forward orientation in black. Finally, a compressed view shows coverage across the pan-genome coordinate space of all paths. It's a kind of heatmap. This helps when we have a lot of paths to consider.

> ## Exercise
> 
> Based on this pan-genome graph (i.e., using `pggb` default setting) and visualisation, what is your interpretation of the DRB1 pan-genome? You can use `odgi stats` to obtain some basic statistics about the graph.
>
> ~~~
> $ odgi stats -i *.og -S
> ~~~
> {: .bash}
> How does the size of the graphs relate to the length of the individual genes used to invoke the graph?
>
>> ## Solution
>> 
>> There are at least three larger regions of rearrangements in the pan-genome graph. There are no large duplications in the graph, visualize by the self-depth plot. Moroever, one of the sequences is inverted, as visual from the plots. Interestingly, the total graph length is nearly double the length of most of the input sequences, suggesting that there is quite a high level of diversity in the graph between the sequences.
>>
> {: .solution}
{: .challenge}

We can look into a simple representation of the alignments that underly the graph generation. These are stored in a *paf file. We can visualize these with

> ~~~
> $ paf2dot png small DRB1-3123.fa.bf3285f.alignments.wfmash.paf 
> ~~~
 {: .bash}

You can transfer the output file to your local machine. As you can see from the alignments, and also visible from the graph, one of the sequences is inverted (tip: sequences inverted to increase readability are indicated with a \*).

## The effect of parameters on pan-genome graph reconstruction
Changes in the parameters have a significant impact on the generation of the pan-genome graph. We will take a look at three main parameters of `pggb`, `-p`, `-k`, and `-s`. 

The `-p` setting affects the level of pairwise divergence that's accepted in the mapping step. We will now make the alignment more stringent by changing `-p` to 95%. 

 ~~~
 $ pggb -i DRB1-3123.fa -p 95 -n 12 -t 2 -o DRB1_3123.1_p95
 ~~~
 {: .bash}
 
It is clearly visible in the diagnostic plots, but also in the alignment file, that the graph has been broken into isolated components formed by sets of sequences that have >95% pairwise identity. You can also check this by obtaining the length of the graph with `odgi statss`.

Another key parameter is `-k`. This filter removes exact matches from alignments that are shorter than `-k`. Short matches can also occur in regions of higher diversity. In practice, these short matches contribute little to the overall structure of the graph, and we can remove them to further simplify the base graph structure. 

> ## Exercise
> 
> What happens if we increase the length of the matches to 47?.
>
>> ## Solution
>> 
>> The graph starts to become "braided", where regions previously aligned due to short matches are not longer joined. We might say that it is under-aligned.
>>
> {: .solution}
{: .challenge}

Pangenome variation graphs built by pggb are based on homology mappings. The homology maps are built using segments of a fixed size, rather than short k-mers, which makes them suitable for quickly finding high-level patterns of homology.

You can think of `-s` as a seed length for the mappings. It defaults to 5kb, which testing has shown to provide a good tradeoff for computational efficiency, graph collinearity, and SV breakpoint detection. Setting it much higher can start to reduce sensitivity to small homologies.

Increasing `-s` to >10 kb results in a touch of "underalignment". On of the sequences is not aligned into the graph, and two more are only partially aligned, resulting in the appearance of a new graph tip. 

This highlights that pan-genome graph genome graph generation is sensitive to the parameters used. It is important to that parameter settings for whole genomes and chromosomes often are more stringent than those we have tested here. 

{% include links.md %}
