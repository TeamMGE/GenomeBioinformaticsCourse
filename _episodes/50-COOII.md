...



# blast

In this excercise we want to compute orthologs via the BBH method between human and mouse, and then plot the distribution of identities. For this we need the blast output between these two species. 

Instead of running these blast searches yourself, we have already performed for you an blast search of all human proteins to all mouse proteins. And we also did all homology searches of all mouse proteins versus all human proteins. (for a detailed motivation on why we did this for you, fell free to ask me; see e.g. https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs for more detail on BLAST).

The files containing the output of these searches can be found at ../../../Data/ToData/Block2/COOII/ 

Can you find these files. Please look at them but please note that they are too big for coalc so either download them or use the terminal or vim. Relevant for you is that column1 is gene identifier species 1, column2 is gene identifier species 2, column 3 is the percentage identity of the hit, column 11 is
the e\-value and column 12 the bitscore. For the human vs mouse file, species 1 is human and species 2 is mouse. For the mouse vs human file, species 1 is mouse and species 2 is human. 

No need to code yet but this is the file that is going to serve as the basis for your very own orthology  between human and mouse, so I think your should look at it / browse it, before starting scripting.


# bidirectional_best_blast_hits

So now we want to go from blast hits to orthologs between human and mouse. For this we will use the bidirectional best blast hit heuristic, as explained in the lectures. And we are going to brush up our python skills. Given that we are going to do human and mouse, the relevant blast files are: blast_out_human_vs_mouse.txt and blast_out_mouse_vs_human.txt.

As explained we already did the blast. So we just need to parse it in such a way we get our candidate orthologs. However, this is relatively a big task, which we think should be done in one piece of code.

To approach this relatively big task we want to do a couple of things.
1. Write the task first as a pseudo code on a piece of paper or in word document or someting. (we might  also ask for some pseudo code in the exam). Also think about some intermediate points where you can summarize the progress of the script sofar.
2. Lets start coding: start again by reading in the blast file. The file contains 12 columns: 'qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore'. Try to figure out what these columns mean. A note: what consitutes a best hit? We want you to use bitscores rather than sequence identities or e-values. (Although of course we could also test to what extent that makes a difference). Use two distinct dictionaries to keep track of the hits with the best score per query protein: one dictionary for human and an other dictionary for mouse. Every dictionary should have the protein identifier of the query sequence as a key and as a value a list containing protein identifier of the hit/subjec, the bitscore and the sequence identity.
3. Loop over all human proteins with a best hit in mouse and check if that mouse protein has the same human protein as a best hit. If they do, these are the biderectional best hits (BBH) and you can write the pair to a  data structure that you can use later (or perhaps to a file). Also, report the number of pairs to the screen.

# distribution_of_identities_of_orthologs

We want to see how similar the orthologs between human and mouse are. Specially we want you to write a script that gives the distribution of idenities of all the bidirectional best hits that you have just created and report this as a histogram.

After you have finished this script, if you would rather have made a histogram of e\-values, how would you have done that? i.e. to where would you have to go back and change something?

Inspect the resulting distribution of protein sequence identities. What identify is most likely for a human\-mouse orthologous pair? Are there orthologous proteins between human and mouse at less than 60% sequence identity?  


# also_make_bbh_orthologs_and_identity_distribution_for_human_zebrafish 

Use the code above to do the same for human and zebrafish (i.e. <b>identify</b> bbh orthologs and <b>plot</b> their identity). We provide two blast files to do this at ../../../Data/ToData/Block3/COOII, i.e. blast_out_human_vs_zebrafish.txt and blast_out_zebrafish_vs_human.txt

You can do the same analysis for human and zebrafish as you did for human and mouse, in multiple ways, for example you can

1. copy and paste your code below with the zebrafish_human files replacing the human_mouse files
2. rewrite your code into functions and call these functions below
3. replace human and mouse files in the above with human and zebrafish files and execute everything again ... 

NB for option 1 and 3 there is a danger because in python notebooks your global variables exist in all cells. As a consequence the dictionaries you have used to make bbh_orthologs from human and mouse will also exist/still be filled when doing zebrafish. As a solution: either clear the variables (e.g. restart the kernel), or rename the variables, or perhaps best: use a function.

How do the two distributions compare? What is the sequence identity of the most diverged orthologs between human and zebrafish, and what is the highest sequence identity between human and zebrafish? Why are there such large differences between these proteins?  
 

<b>Optional</b>: can you also plot them in the same graph?


