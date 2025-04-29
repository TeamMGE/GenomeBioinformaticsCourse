---
title: "Block 2 - COOI: Gene tree intepretation & orthology: a metabolic enzyme"
start: true
teaching: 0:00
exercises: 30
questions:
- How is my human gene related to genes in other model organisms and what does this tell me about orthologs 
objectives:
- Practice the usage of common command line tools to find homologs and build trees.
- Practice with interepting gene trees. 
keypoints:
- Orthology can be a many-to-many relation 
---

Exercises from a human gene to a simple gene tree

In this exercise, our goal is to infer the evolutionary history of a human protein starting from its sequence. This evolutionary history should reveal the orthologs in other species and the timing of the duplicates of our protein. We are going to use the human 6-phosphofructo-2-kinase / fructose-2,6-bisphosphatase as a starting point

    1. 
    
    
    
Go to  [http://www.uniprot.org/uniprot/Q16877](http://www.uniprot.org/uniprot/Q16877) . Quickly scan the page. And see what kind of information uniprot has available and to what kind of databases it cross references.

At the top of the page click on format and select “fasta (canonical)”. Copy all the text, i.e. the sequence and the fasta header. (see https://en.wikipedia.org/wiki/FASTA_format if you want to know what fasta header is / means)

    3. Open a text editor on your local PC (e.g. textedit, notepad++) and copy and paste the protein sequence of Q16877 in your text file. Save the protein sequence as a text file named “query.txt”. scp the text file to your cocalc folder where we are doing these exercises.

Check how your file looks on cocalc by typing e.g. `more query.txt`, or `less query.txt`.

Now we are going to run blast with the human 6-phosphofructo-2-kinase / fructose-2,6-bisphosphatase  as a starting point. We are going to do this via `blastp -query query.txt -db ./proteomes1.fa > tmp.out`
Look at the output, using (for example e.g. more tmp.out or less tmp.out or via the cocalc explorer). Proteins starting with HSAP are human. ATHA is a plant, CELE is worm, DMEL is fly, SCER is baker’s yeast, SPOM is fission yeast. How much hits E-value < 1e-10 do you see in human, plant, worm and fly?
> ## Exercise: inspecting the blast output 
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

Still looking at the file, look at the pairwise alignment between your query and its best hit in DMEL. What is the percent identity with the best hit in fly?

>> ## solution
>>`> DMEL018546`
>> `Length=716`

 Score = 529 bits (1363),  Expect = 0.0, Method: Compositional matrix adjust.
 Identities = 258/456 (57%), Positives = 329/456 (72%), Gaps = 5/456 (1%)
57%


We want to create a fasta file in order to make a tree of the hits in plants, in animals (fly, worm, human) with E-value < 1e-10; and of the best hit in fission yeast (SPOM) and the best hit in baker’s yeast SCER. To do so:

    1. Copy the identifiers of the sequences you want to a text file on your laptop or download the blast output file (e.g. tmp.out) to your laptop.
    2. For each of these sequences grep them from the proteomes files (i.e. grep -A10 “sequence identifier” ~/Data/ToData/Block2/COOI/proteomes1.fa ). Copy and paste the sequence of each protein that you need to include into a new local file on your laptop. Note that this is some work. If you can (python/awk/bash) script and if you need to do this for more than 10 sequences, you should really write a small script for tasks such as this that obtains all sequences for a list of identifiers.
    3. Upload this file to the cocalc server using the upload button on cocalc. 
    4. Run mafft on your fasta file. i.e. mafft [yourfile] > [name of alignment file, e.g. homs.msa]
    5. Then run iq tree e.g. iqtree2 -s  homs.msa – m LG+G4
    6. Download the output tree (i.e. homs.msa.treefile) or open it and copy past to view the tree in iToL https://itol.embl.de/upload.cgi



    8.  Okay now we get to the interpretation. Look at the tree and reroot it to make some kind of biological sense. You can reroot trees by clicking on a branch -> Tree structure -> re-root the tree here. Sketch the resulting tree on paper or copy a picture of the resulting tree into a program which allows to draw on top of it (e.g. powerpoint, paint). Annotate the tree in terms of duplications and speciations. How many duplications does this tree imply? 

Rooted on plants because officially fungi and animals are closer to eachother than either is to plants: So I get this
![answerX](../fig/answer_pfkfp.jpg)

    9. Check the function of the different human genes, and the reconstruction according to literature from the following article https://bmcbiol.biomedcentral.com/articles/10.1186/1741-7007-4-16 (our proteins are in the left most panel of figure 2). What type of functional differentiation have the genes undergone.
Functional differentiation is change in tissue where these paralogous enzymes are predominantly expressed i.e. platelet, liver , muscle, brain. So no change in enzymatic/molecular function. Common with inparalogs. 


    10. According to your tree, which human gene(s) are orthologs of which genes in D. melanogaster and to which genes in C. elegans? 
HSAP037297, HSAP082045, HSAP043809, HSAP095035 are orthologous to DMEL018546 . 1-to-many.
HSAP037297, HSAP082045, HSAP043809, HSAP095035 are orthologous to CELE028867, CELE024628 Many-to-many




