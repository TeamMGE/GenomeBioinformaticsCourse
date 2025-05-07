---
title: "Block 2 - COOIII: Profile searches"
start: true
teaching: 0:00
exercises: 45
questions: 
- What is actually happening during an iterative profile search?     
objectives: 
- Practice with using profile searches 
- Practice with interpreting profile searches output 
keypoints:
- Profile searches are better able to distinguish homologs from non-homologs than pairwise sequence searches 
---

Divergent homologs

We are going to do some profile searches to get a feeling for what a profile search achieves and it achieves it. For this we will be looking at the med11 protein, which is a subunit of the Mediator complex that serves as a coactivator for DNA-binding factors in activating transcription via RNA polymerase II (according to Wikipedia). 
-	Obtain human med11 protein sequence from UNIPROT. i.e. go to UNIPROT, search for med11_human and go to this entry. At the top of the page click on download, then in format select “fasta (canonical)”. Upload this sequence to your cocalc terminal. 


> ## Exercise:   Look at the sequence, is there anything that stands out to you?
>
>> ## solution
>> It is short
>{: .solution}
{: .challenge}

-	Run blast, via ` blastp -query name of your query file  -db ~/data_bb3bcg20/Block2/COOIII/eukarya.v3.renamed.prot.longest.fa.new.headers > [name of your output file] “. Can you see a sequence whose identifier starts with SPOM in the output? ( also look at the insignificant hits)? 

> ## Exercise:   Can you see a sequence whose identifier starts with SPOM in the output? ( also look at the insignificant hits)?
> 
>> ## solution
>> No sequence whose identifier starts with SPOM (you can check using e.g. grep)
>{: .solution}
{: .challenge}


> ## Exercise:	Does this lack of hits mean that the fungus SPOM (Schizosaccharomyces pombe, fission yeast ) lacks the med11 gene?
> 
>> ## solution
>> No, the search could just be not sensitive enough especially given that many things turn out to be older than we thought (null model/zig-zag) and the shortness of med11
>{: .solution}
{: .challenge}

 
Now we are going to do profile searches. For this we will be using JACKHMMER. Do a JACKHMMER search with 3 iterations as follows: `jackhmmer --noali -N3 name_of_your_query_fasta_file ~/data_bb3bcg20/Block2/COOIII/eukarya.v3.renamed.prot.longest.fa.new.headers > name_of_your_output_file`. Note that we use the –noali option to keep the output somewhat readable. 


Look at the outputfile. 
> ## Exercise:	How can you see in the output that there are iterations?
> 
>> ## solution
>> e.g. 
>>@@ New targets included:   19
>>@@ New alignment includes: 20 subseqs (was 1), including original query
>>@@ Continuing to next round.
>>
>>@@
>>@@ Round:                  2
>>@@ Included in MSA:        20 subsequences (query + 19 subseqs from 19 targets)
>>@@ Model size:             117 positions

>>
>> i.e. you get a list of hits that keeps growing and with changing grey-zone
>{: .solution}
{: .challenge}

e.g. 
@@ New targets included:   19
@@ New alignment includes: 20 subseqs (was 1), including original query
@@ Continuing to next round.

@@
@@ Round:                  2
@@ Included in MSA:        20 subsequences (query + 19 subseqs from 19 targets)
@@ Model size:             117 positions


In which iteration does the SPOM med11 appear and with what e-value? And (how) does this e-value improve over the iterations?
  
-	E.g. grep SPOM med11_vs_euk3_jackhmmer | grep med11
-	
-	       0.54   16.5   0.3       0.67   16.2   0.3    1.1  1  SPOM002872_med11              SPAC644.10.1:pep SPAC644.10 116 
-	>> SPOM002872_med11  SPAC644.10.1:pep SPAC644.10 116 med11 mediator complex subunit Med11 (predicted) (original header:S
-	+   9.9e-05   28.7   0.3    0.00013   28.4   0.3    1.1  1  SPOM002872_med11              SPAC644.10.1:pep SPAC644.10 116 
-	>> SPOM002872_med11  SPAC644.10.1:pep SPAC644.10 116 med11 mediator complex subunit Med11 (predicted) (original header:S
First iteration no SPOM med11, second iteration not significant, third iteration significant 



-	There are also other SPOM hits. Find them and describe what happens to them in the iterations and what happens to their e-value? For example are the e-values ever significant?

They appear and disappear and do not become significant 
e.g.         1.1   15.8   0.0        1.5   15.4   0.0    1.2  1  SPOM002124_ptf2              SPBC16G5.13.1:pep SPBC16G5.13 12
in the first iteration/search disappears from subsequent searches 

Next we will use a query the sequence from the med11 annotated protein from the protist Trichomonas available here: https://rest.uniprot.org/uniprotkb/A2E1L7.fasta. Download this sequence from UNIPROT. scp it to gemini or use wget it get it from the web to gemini (e.g. ``). Then do a jackhammer search as described above for this query. Store the output in a different file than the search from human med11. Look at the output.

> ## Exercise:	
What do you notice in the output? 
Only a single iteration 

-	Why is jackhammer not succeeding at making a profile/alignment to start the search?
If you hit nothing, you cannot start iterating 

-	What does “CONVERGED” mean in the output? 
That the search does not find anything new and so the profile does not change and so the search is converged 

-	What is the identifier of the Trichomonas med11 in eukarya.v3.renamed.prot.longest.fa.new.headers based on it being an identical hit to your downloaded Trichomonas med11 query? 
TVAG035526

-	Go back to your jackhammer search from human. Can you find this Trichomonas sequence in the JACKHMMER output?
Yes
1.9e-06   34.2   1.1    2.1e-06   34.1   1.1    1.2  1  TVAG035526                    TVAG_206320 TVAG_206320 102  con

-	Argue, based on what you have seen in your output if all the “med11” homologs you have found are likely to be part of the same orthogroup or different orthogroups?
Given that I see no duplicates in any species, I think all homologs are also orthologs
i.e. if there are no ancient duplications everything is likely orthologous


