---
title: "Block 2 - COOIII: Profile searches"
start: false
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

## Background divergent homologs 

We are going to do some profile searches to get a feeling for what a profile search achieves and how it achieves it. What this means is that by looking not just at the outcome of a profile search, but doing the search iteratively we should sequences that first were not here appear; and also see searches that have a borderline e-value disappear again, as the profile sharpens and it should become apparent that these similarities are superflous. 

For this we will be looking at the med11 protein, which is a subunit of the Mediator complex that serves as a coactivator for DNA-binding factors in activating transcription via RNA polymerase II (according to Wikipedia). In principle most eukaryotes contain at least part of the mediator complex. However for a bunch of reasons the sequences of the this complex sometimes evade easy homology detection. 

## normal pairwise sequence (in this case BLAST) searching with the human med11

To get the sequence of of med11 go to uniprot, search for it and downlod it from uniprot and then scp it to gemini, or perhaps easier use wget to get it: i.e. `wget https://rest.uniprot.org/uniprotkb/Q9P086.fasta`

> ## Exercise:   Look at the sequence, is there anything that stands out to you?
>
>> ## solution
>> It is short
>{: .solution}
{: .challenge}

Run blast, via ` blastp -query name_of_your_query_file  -db ~/data_bb3bcg20/Block2/COOIII/eukarya.v3.renamed.prot.longest.fa.new.headers > name_of_your_output_file`. Inspect the output for the presence of a homolog in S. pombe. You can do this with `less name_of_your_output_file` and then once in `less` typing / followed by typng SPOM to search. You can also use e.g. `grep SPOM name_of_your_output_file`. In searching also look at the e-value of the hit and also consider insignificant hits. 

> ## Exercise:   Can you find a sequence whose identifier starts with SPOM in the output? ( including in the insignificant hits)?
> 
>> ## solution
>> No sequence whose identifier starts with SPOM (you can check using e.g. grep)
>{: .solution}
{: .challenge}


> ## Exercise:	Does this lack of hits mean that the fungus SPOM (Schizosaccharomyces pombe, fission yeast ) lacks the med11 gene?
> 
>> ## solution
>> No, the search could just be not sensitive enough especially given that many things turn out to be older than we thought (null model/zig-zag), I mention in the introduction that most eukaryotes should have med11, and also given the shortness of med11 
>{: .solution}
{: .challenge}



## iterative profile sarches of human med11 

Now we are going to do profile searches. For this we will be using JACKHMMER. Do a JACKHMMER search with 3 iterations as follows: `jackhmmer --noali -N3 name_of_your_query_fasta_file ~/data_bb3bcg20/Block2/COOIII/eukarya.v3.renamed.prot.longest.fa.new.headers > name_of_your_output_file`. Note that we use the –noali option to keep the output somewhat readable. 


Inspect the outputfile using `more` or `less` or `cat`. 
> ## Exercise:	How can you see in the output that there are iterations?
> 
>> ## solution
>> there is round, and each round includes new targets (e.g. sequences that it hits) for example see this section of the file 
>>
>>        @@ Round:                  2
>>        @@ Included in MSA:        20 subsequences (query + 19 subseqs from 19 targets)
>>        @@ Model size:             117 positions
>>        @@ New targets included:   19
>>        @@ New alignment includes: 20 subseqs (was 1), including original query
>>        @@ Continuing to next round.
>> 
>> You should see that you get a list of hits that keeps growing together with a changing grey-zone
>{: .solution}
{: .challenge}


So given that this is a "profiles work" exercises SPOM shoud appear. Try to locate in the output how/when this is happening. 

> ## Exercise:	In which iteration does the SPOM med11 appear and with what e-value? And (how) does this e-value improve over the iterations?
> 
>> ## solution 
>>        grep SPOM your_jackhmmer_output_file | grep med11
>>        	
>>        0.54   16.5   0.3       0.67   16.2   0.3    1.1  1  SPOM002872_med11              SPAC644.10.1:pep SPAC644.10 116  SPOM002872_med11  SPAC644.10.1:pep SPAC644.10 116 med11 mediator complex subunit Med11 (predicted) (original header:S
>>
>>        9.9e-05   28.7   0.3    0.00013   28.4   0.3    1.1  1  SPOM002872_med11              SPAC644.10.1:pep SPAC644.10 116 SPOM002872_med11  SPAC644.10.1:pep SPAC644.10 116 med11 mediator complex subunit Med11 (predicted) (original header:S
>>
>>First iteration no SPOM med11, second iteration not significant (0.54), third iteration significant (9.9e-05)
>{: .solution}
{: .challenge}

There are also other SPOM hits than the med11 hit. Find them and describe what happens to them in the iterations and what happens to their e-value and try to consider why this is happening to them. Relevant is for example to see if the the e-values ever become significant.

> ## Exercise:	what happens to the other SPOM hits
> 
>> ## solution
>> They appear and disappear and do not become significant
>> 
>> we for example we have lines like
>>          1.1   15.8   0.0        1.5   15.4   0.0    1.2  1  SPOM002124_ptf2              SPBC16G5.13.1:pep SPBC16G5.13 12
>>
>> present in the first iteration/search, but they disappears from subsequent searches because they are not true and the better profile throws them out
>{: .solution}
{: .challenge}



## Limits of iterative profile searches and how the reverse -being hit by a profile- solves this

Next we will use a query the sequence from the med11 annotated protein from the protist Trichomonas available here: https://rest.uniprot.org/uniprotkb/A2E1L7.fasta. Use wget to get it to gemini as described above, or download this sequence from UNIPROT to your laptop and then scp it to gemini. 

Then do a JACKHMMER search as described above for this query. Store the output in a different file than the search from human med11. Look at the output.

> ## Exercise:	What do you notice in the output? 
> 
>> ## solution
>> There is only a single iteration, meaning the profile does not get going  
>{: .solution}
{: .challenge}


> ## Exercise: Why is jackhammer not succeeding at making a profile/alignment to start the search?
> 
>> ## solution
>> If you hit nothing, you cannot start iterating 
>{: .solution}
{: .challenge}


> ## Exercise: What does “CONVERGED” mean in the output? 
> 
>> ## solution
>> That the search does not find anything new and so the profile does not change and so the search is converged on a solution. Further iterations will not change anything about the outcome. 
>{: .solution}
{: .challenge}


> ## Exercise: What is the identifier of the Trichomonas med11 in eukarya.v3.renamed.prot.longest.fa.new.headers based on it being an identical hit to your downloaded Trichomonas med11 query?
>
>> ## solution
>> TVAG035526
>{: .solution}
{: .challenge}

Go back to your jackhammer search with human med11 as the query and search for this this Trichomonas sequence in the JACKHMMER output? (using e.g. grep or less)

> ## Exercise: is this trichomonas sequence there? 
>
>> ## solution
>> Yes
>>          1.9e-06   34.2   1.1    2.1e-06   34.1   1.1    1.2  1  TVAG035526                    TVAG_206320 TVAG_206320 102  con
>{: .solution}
{: .challenge}

So we have now searched for difficult to find homologs. Take a step back and consider  based on what you have seen in your output if all the “med11” homologs you have found are likely to be part of the same orthogroup or different orthogroups?
> ## Exercise: are all of your hits also orthologs?
>
>> ## solution
>> We do not know. We have not made a tree. But given that I see no duplicates in any species, I think there is a higgh likelihood that all homologs are also orthologs
>>
>> i.e. if there are no ancient duplications everything is likely orthologous
>{: .solution}
{: .challenge}

> ## Exercise: If we would have run orthofinder. Would orthofinder have made a correct orthogroup?
> 
>> ## solution
>> Likely orthofinder would fail, because the blast output cannot connect all orthologs. Consequently you get disconnected clusters or singleton sequences in your blast BBH network/graph.
>>
>> If you would want a network that connects these sequences you need profile searches network to start from. Although even then, how would Trichomonas ever make a bidirectional/reciporcal best hit? 
>{: .solution}
{: .challenge}
