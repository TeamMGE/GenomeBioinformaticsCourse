---
title: "Block 2 -Manually interpreting a more complicated gene tree"
start: true
teaching: 0:00
exercises: 45
questions:
- What is the evolutionary history of the RasGEF gene family in animals, fungi and an amoebozoan
objectives:
- Practice interpreting a gene tree that reflects a more complex set of events  
- Practice interpreting a gene tree that contradicts the species tree
keypoints:
- Gene families are dynamic with recurring duplications and losses 
---

RasGEFs are important signalling proteins that diversified predominantly in amoebozoa and opisthokonta (a.k.a. fungimals/funimals, i.e.  fungi & animals; see https://www.cell.com/trends/ecology-evolution/fulltext/S0169-5347(19)30257-5 for a review on the relationship among eukaryotes). We want to reconstruct the gene duplications and losses of this dynamic gene family within amoebozoa and opisthokonta. However, this subtree of the RasGEF’s is a real life tree and real life is not so easy. Look at the following tree of RasGEF(CDC25) containing proteins .

![atree1](../fig/)
The six letter abbreviations are as follows HOMSAP; human, DROMEL: fruitfly; CAEELE: nematode worm; DYCDIS: slime mold; USTMAY: basidomycete corn smut fungus; NEUCRA ascomycete red bread mould fungus; SACCER ascomycete baker's yeast.

The fully resolved species tree for these species is as follows
![atree2](../fig/)


Assume I have correctly rooted this gene tree. We are going to annotate the RasGEF tree. However reconciling the RasGEF protein tree in terms of duplications and speciations, strictly using the fully resolved species tree would yield (too) many ancient duplications and differential gene losses. So instead, annotate internal nodes of the RasGEF protein tree in terms of gene duplications, gene losses and speciations where this designation is not contradictory to the unresolved (consensus) tree. (i.e. perform tree reconciliation using an unresolved species tree An useful “partially unresolved” species tree for these species could be the one below. Another way to say what you need to do, is to use your common sense and avoid spurious “ghost” duplications and losses. As a consequence, this strategy does not really specific exactly where you draw gene losses in the gene tree; so that is up to you)

![atree3](../fig/)

Consider the orthology relations. Which genes in human are orthologous to which genes in fruitfly? And which genes in human are orthologous to which genes in S. cerevisiae?


We annotated the gene tree using the uncresolved species tree, but if we would have used the fully resolved species tree, and applied strict tree reconliation we would have infered duplications that are very unlikely, have a duplication concistency score of 0 and create also inlikley losses. We refer to these as spurious duplications. 

> ## Exercise:  Circle/indicate in the gene tree where strict reconciliation would have given “spurious” duplications and losses. 
>
>> ## Solution
>>
> {: .solution}
{: .challenge}

Exercises from a human gene to a simple gene tree

In this exercise, our goal is to infer the evolutionary history of a human protein starting from its sequence. This evolutionary history should reveal the orthologs in other species and the timing of the duplicates of our protein. We are going to use the human 6-phosphofructo-2-kinase / fructose-2,6-bisphosphatase as a starting point. This bi-functional (hence the long name)  enzyme is an enzyme that catalyzes formation as well as degradation of a significant allosteric regulator of glycolysis and gluconeogenesis:  fructose-2,6-bisphosphate. 
          
Go to  [http://www.uniprot.org/uniprot/Q16877](http://www.uniprot.org/uniprot/Q16877) . Quickly scan the page. And see what kind of information uniprot has available and to what kind of databases uniprot cross references. 

At the top of the page click on format and select “fasta (canonical)”. Copy all the text, i.e. the sequence and the fasta header. (see [https://en.wikipedia.org/wiki/FASTA_format](https://en.wikipedia.org/wiki/FASTA_format) if you want to know what a fasta header is / means)

Open a text editor on your local laptop (e.g. textedit, notepad++) , and copy the protein sequence of Q16877 into a text file. Save the protein sequence as a text file named “query.txt”. Then use `scp` to copy the text file to your gemini folder where we are doing these exercises.

Check how your file looks on gemini by typing e.g. `more query.txt`, or `less query.txt`.

Now we are going to run blast with the human 6-phosphofructo-2-kinase / fructose-2,6-bisphosphatase  as a starting point. We are going to do this via `blastp -query query.txt -db ~/data_bb3bcg20/Block2/COOI/proteomes1.fa > tmp.out`
Look at the output, using (for example e.g. `more tmp.out` or `less tmp.out`). Proteins starting with HSAP are human. ATHA is a plant, CELE is a nematode worm, DMEL is the fruitfly, SCER is baker’s yeast, SPOM is fission yeast. 
> ## Exercise: How many hits with E-value < 1e-10 do you see in human, plant, worm and fly?
>
>> ## Solution
>>      HSAP037297                                                          979     0.0   
>>      HSAP082045                                                          743     0.0   
>>      HSAP043809                                                          671     0.0   
>>      HSAP095035                                                          667     0.0   
>>      DMEL018546                                                          529     0.0   
>>      CELE028867                                                          477     3e-166
>>      CELE024628                                                          437     2e-150
>>      SCER003727                                                          406     2e-138
>>      ATHA005880                                                          345     1e-110
>>      SPOM004734                                                          304     4e-99 
>>      SCER001206                                                          298     3e-92 
>>      SPOM002399                                                          287     3e-89 
>>      SPOM003505                                                          253     5e-77 
>>      SCER000727                                                          202     1e-58 
>>      SPOM001690                                                          166     2e-45
>>
>> So, HSAP 4, DMEL, 1, CELE 2, ATHA 1
> {: .solution}
{: .challenge}

Still looking at the blastoutput file, look at the pairwise alignment between your query and its best hit in DMEL.

> ## Exercise:  What is the percent identity with the best hit in fly?
>> ## solution
>>`> DMEL018546`\
>> `Length=716`\
>>
>>  `Score = 529 bits (1363),  Expect = 0.0, Method: Compositional matrix adjust.`\
>>  `Identities = 258/456 (57%), Positives = 329/456 (72%), Gaps = 5/456 (1%)`\
>> so precent identity 57%
> {: .solution}
{: .challenge}
rasgef manual analysis
