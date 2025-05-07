---
title: "Block 2 - COOIII: size of a gene family"
start: false
teaching: 0:00
exercises: 45
questions: 
- Can we quickly establish the size of a gene family in a predicted proteome?    
objectives: 
- Practice with using HMMER
- Practice with intepreting homology searches in light of the literature
keypoints:
- Expanded gene families reflect taxon/species adaptation 
---

Size of gene family
In the “genoombiologie” course that some of you followed, we discussed that elephants have undergone duplications of their p53 gene and that this has been hypothesize to help these large animals that need many cell divisions to avoid cancer. See https://elifesciences.org/articles/11994

An easy way to get the size of a gene family is to take a profile of a gene family and search against all predicted proteins of the genome/species of interest and count the hits. This is also a common strategy to collect sequences for an informative gene tree. In this short exercise we are going to use the P53 profile to look at how many members this gene family has in a few mammals.

-	A profile for The p53 gene family is available at EBI INTERPRO resource here:
-
-	https://www.ebi.ac.uk/interpro/entry/pfam/PF00870/curation/
use wget to get
https://www.ebi.ac.uk/interpro/wwwapi//entry/pfam/PF00870?annotation=hmm
or download to your laptip and scp to gemini


-	. Download the raw HMM file from this web page. Upload the file to cocalc. Use “gunzip” on the command line to unzip the file  and look at its contents. How many sequences were used to make this profile?
NSEQ  38
So 38

-	We are going to use HMMSEARCH to find all members of this families. First let us do human “hmmsearch [hmm profile name] ~/Data/ToData/Block2/COOIII/Homo_sapiens.GRCh38.pep.all.longest.fa > [output file]”. Look at the output, how many hits do you see?

3

-	Do the same for Heterocephalus_glaber_female.Naked_mole-rat_maternal.pep.all.longest.fa,  Loxodonta_africana.loxAfr3.pep.all.longest.fa (the elephant), and Mus_musculus.GRCm38.pep.all.longest.fa. Can you reproduce the result from the literature?

Yes elephant has more 

-	Why have we included also the naked mole rat?
Because it is also famously cancer resistant
