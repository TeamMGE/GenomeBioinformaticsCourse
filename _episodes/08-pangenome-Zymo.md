---
title: "Pan-genome graphs of Zymoseptoria tritici"
teaching: 0
exercises: 150
questions:
- How can we build a pan-genome graph for individual communities?
- How can we interpret the more complex pan-genome graphs?
- How can we relate features of the pan-genome graph to pair-wise genome comparisons? 
objectives:
- Build pan-genome graphs for core and dispensable chromosomes using pggb.
- Interpret pan-genome graphs in relation to known biology.
- Compare pan-genome graphs with pair-wise whole-genome alignments.
keypoints:
- You can build pan-genome graphs for homologous chromosomes.
- You can critically evaluate the shape of a pan-genome graph and relate it to known biology.
---

#  Pan-genome graph of *Zymoseptoria tritici*
We will analyse a set of six *Zymoseptoria tritici* strains that have been sequenced as part of a pan-genome [paper](https://bmcbiol.biomedcentral.com/articles/10.1186/s12915-020-0744-3). We assume that these genome assemblies are (near)chromosomal-level, and therefore represent an interesting dataset to build a pan-genome graph.

In the previous step, we identified different communities that represent homologous chromosomes of six *Z. tritici* strains. We will now move a step further by generating pan-genome graphs for different communities. Please be aware that determining the pan-genome graph for larger chromosomes will take some time. Therefore, please be patient, especially when computing the pan-genome graph of the larger core chromosomes.

## Pan-genome graph of core community
We will first generate a pan-genome graph for the community that represents chromosome 3 of IPO323. Please check in which community chromosome 3 is localised and adjust the command below accordingly, e.g., chromosome 3 might not be in community 18 in your particular partition. 

~~~
$ pggb -i combined.community.18.fna -s 10000 -n 6 -t 4 -p 90 -o combined.community.18 
~~~
{: .bash}

To create the pan-genome graph, we first need to create the all-vs-all mappings. Given the size of the chromosomes (~3.5 Mb), this step will take some time (~10-15 min). Please us this time to take  look at the options we used to invoke the graph. What are your expectations if you would have changed `-p` and `-s` to other values than in the command? How would you expect the graph of a core chromosome to look like, and why?

Once `pggb` is finished, copy the *png files from the server to your local machine. We will first examine the 2D representation of the pan-genome graph. 

> ## Exercise
> 
> Take a look at the 2D representation of the pan-genome graph of chromosome 3 but also the statistics you can get from `odgi`. How would you describe this graph in terms of i) co-linearity and ii) conservation?
>
>> ## Solution
>> 
>> Overall, the pan-genome graph seems largely co-linear. In the first 1/3 of the graph, there is a large loop and there are smaller loops/bubbles throughout the graph. It is also clear that the overall conservation is not very high and that each strain has genomic regions absent from the other strains. This can also be seen from the statistics that indicate that the pan-genome graph is distributed over many nodes and is ~7Mb in size.
>>
> {: .solution}
{: .challenge}

Now we will take a look at the 1D visualisation of the pan-genome graph, especially the plots that display the node orientation and strandness.

> ## Exercise
> 
> What do you observe w.r.t. to the overall orientation and strandness of the individual assemblies? What is your interpretation?
>
>> ## Solution
>> 
>> It seems that one of the chromosomes has a larger section inverted compared with the other five genomes. Another way to visualise this inversion is to display the .paf file as a dotplot using `paf2dot`. Note: you will need to adjust the filename to your corresponding name.
>> ~~~
>> $ paf2dot png large combined.community.18.fna.c325321.alignments.wfmash.paf
>> ~~~
>> {: .bash}
>> This occurence of this inversion is corroborated by the alignment visualisation. This inversion in one of the strains could either point to a true inversion on this chromosome or potentially to an assembly artefact. 
>>
> {: .solution}
{: .challenge}

We now would like to focus on an additional region on chromosome 3 that has been previously [reported](https://bmcbiol.biomedcentral.com/articles/10.1186/s12915-020-0744-3) (Figure 3e) to show a complex region with many transposable elements and a duplication of a gene involved in fungicide resistance.

We will first generate a pairwise alignment using `nucmer` with the IPO323 chromosome 3 as a reference as the corresponding chromosome of 3D7 as a query. You can either re-use the alignment file from yesterday, or generate a new alignment only with these chromosomes. To generate an alignment with only the two chromosomes, we can generate a file called `subset.txt` that contains both chromosome names, on identifier in a new line. We can then first use `samtools faidx` to generte a subset file that only contains the sequences of these two chromosomes. In a second step, we generate a `nucmer` alignment (using `--maxmatch`), and then visualise the alignment using `mummerplot`.

~~~
$ samtools faidx combined.community.18.fna `cat subset.txt` > combined.community.18.subset.fna
$ nucmer --maxmatch -p IPO323chr3v3D7chr3 combined.community.18.subset.fna combined.community.18.subset.fna
$ mummerplot --png --color IPO323chr3v3D7chr3.delta -q "ST99CH_3D7#1#LT853694.1" -r "IPO323#1#chr3" -y [3400000:3700000]
~~~
 {: .bash}
 
> ## Exercise
> 
> What is your interpretation of the alignment? Does it match your expectations based on the data shown in the [paper](https://bmcbiol.biomedcentral.com/articles/10.1186/s12915-020-0744-3) (Figure 3e)?.
>
>> ## Solution
>> 
>> Yes, the alignment of the regions indicates that IPO323 lacks a large region (~100 kb) that is present in 3D7. The region seems to contain repetitive elements, at least partially visible due to the other off-diagonal matches. Based on the paper, the region also seems to be absent in other strains but we cannot easily see this from the pair-wise alignment. To address this question, we would need to compute multiple pairwise alignments between all of the strains in our analyses.
>>
> {: .solution}
{: .challenge}

`odgi` allows us to generate a figure that displays only a small section of the entire graph to get a better understanding how this region is represented in the pan-genome graph. Based on your `nucmer` analyses above and the information, we can select an appropriate window to focus on.

~~~
$ odgi viz -i combined.community.18.fna.c325321.11fba48.9fb5d42.smooth.final.og -o test.png -m -w 50 -r ST99CH_3D7#1#LT853694.1:3450000-3650000
~~~ 	
{: .bash}	

When you compare this visualisation with the `nucmer` figure from above, it seems clear that this insertion/duplication only occurs in 3D7 and in no other strain we have analysed. This reference-free, multiple-strain comparisons make pan-genome graphs very powerful compared with pair-wise analyses. 

## Pan-genome graph of dispensable community
In the previous exercise, we have seen that two communities with dispensable chromosomes were joint during the community detect. We will now try to analyse one of these in more detail to understand if the merger is due to spurious matches or of biological significance. 

We will focus on the community that contains both the dispensable chromosome 14 and chromosome 21. We will create a pan-genome graph using the same settings as above but adjusting `-n` to reflect the number of haplotypes in the input file and reducing `-s` to 5k to also identify smaller segments.

~~~
$ pggb -i combined.community.8.fna -s 5000 -n 10 -t 4 -p 90 -o combined.community.8 
~~~
{: .bash}

Transfer the *png plots from the server to your local computer.

> ## Exercise
> 
> Based on the various 1D/2D representations and the alignment plots, how would characterise this pan-genome graph, especially when you compare it to the pan-genome graph of chromosome 3? If you would need to separate the chromosomes into two sub-communities, which chromosomes would you join and why? Can you explain why these two sets of homologous chromosomes where most likely joined? 
>
>> ## Solution
>> 
>> The pan-genome graph is much less co-linear than that of chromosome 3, and generally characterised by presence/absence variation and rearrangements (visible in the alignment but also in the direction of the chromosome through the graph). The graphs seems to contain two largely unconnected graphs that are joined by a single region that is shared between the two sub-communities (centre of the 1D figure), mediated by a single chromosome. This regions is characterised by other inversions in few chromosomes and it is present at the end or the beginning of chromosomes.
>>
> {: .solution}
{: .challenge}

{% include links.md %}
