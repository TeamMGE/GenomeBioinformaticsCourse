---
title: "COO-II-2. 16S rRNA phylogenetics"
start: false
teaching: 0
exercises: 75
questions:
- How to identify the taxonomy of an organism based on a common phylogenetic marker?
objectives:
- Extract genes based on annotation
- Perform a single-gene phylogeny
- Interpret results at every step
---

## Extracting 16S rRNA sequences

In order to obtain more information about the identity of the organisms we have found, a first analysis we can perform 
is a 16S rRNA phylogeny. The small ribosomal RNA subunit (16S rRNA in bacteria and archaea) is a usual go-to in phylogenetics
due to its slow evolutionary rate, and to the existence of large databases that have already gathered sequences for this molecule
across the diversity of life. To be able to obtain such a tree, first we need to identify the 16S rRNA sequences in our bins. 
Let’s get to it.

First, generate your environment:
~~~
$ cd ~/GenomeBioinformatics/Block1/COO-II/
$ mkdir 02_16SrRNA
$ cd 02_16SrRNA
~~~
{: .bash}

Now, you can find if 16S sequences were found at all by using grep:
~~~
$ grep “16S ribosomal RNA” */bin*.ffn
~~~
{: .bash}

This command should output all lines that match in all the bin files. The first part of the output indicates the file where the
string was identified, followed by a colon (':') and the line that matches.
You can see that some bins contain 16S rRNA sequences, but not all. To extract them, you can first generate a list of their 
locus tags (the tag at the beginning fo the Fasta header). One way to do this would be the following:
~~~
$ cd prokka_bin.2
$ grep “16S ribosomal RNA” bin.2.ffn | sed ‘s/ .*//’ | sed ‘s/>//’ > bin.2.ffn.rRNA.list
~~~
{: .bash}

This ‘grep’ command first outputs the Fasta headers of 16S rRNA sequences, and then uses that output as input (though the pipe, “|”) 
for ‘sed’, which is a powerful text parsing tool. Now, sed will substitute (“s”) the pattern that is provided between the first two slashes 
("s/PATTERN/") with what is provided between the last two slashes ("s/PATTERN/REPLACEMENT/"). This means that it will find everything 
that comes after a space (" .\*", i.e. a space " " followed by any character “.” any number of times “\*”), and it will replace it with 
nothing – essentially, a quick way to remove matching text. Finally, this output is parsed one more time, to remove the “>” symbol, 
leaving only the locus tags. The final part of the command stores the output to the new “bin.2.ffn.rRNA.list” file. Check that 
file to make sure you have correctly extracted the locus tags for ribosomal RNA sequences.

Next, we can use this list of locus tags to extract their corresponding sequences using the software “seqtk”, a versatile fasta-handling 
tool. Here, we save the result in a fasta file with suffix “.frn” (usual for non-coding RNA sequences).
~~~
$ seqtk subseq bin.2.ffn bin.2.ffn.rRNA.list > bin.2.ffn.rRNA.frn
~~~
{: .bash}

Now, repeat this process with all bins that contain a 16S rRNA sequence. If you feel comfortable with the command line, you can also 
try to run a loop to iterate over all ffn files and save a few keystrokes. Make sure that the number of sequences you manage to extract
for each bin matches the number of lines you could identify using grep.


## Reconstruct a 16S rRNA phylogeny

In the Data folder you will find a fasta file containing archaeal 16S rRNA sequences, downloaded from the SILVA database, one of the 
largest and best curated 16S rRNA databases. These sequences have been selected to include one representative for every set of over 90% 
identical sequences. Copy it to your folder:

~~~
$ cp ~/data_bb3bcg20/Block1/COOII/Intermediate_files/16S_phylogeny/silva.frn .
~~~
{: .bash}

> ## Exercise: Analyse the SILVA sequence data
>>
>> - How many sequences are included?
>> - Do they belong to a diverse group of Archaea?
>>  
>> ## Solution
>>
>> `$ grep -c ">" silva.frn`
>> 
>> 235
>>
>> You can get a general feeling about the diversity of these sequences by reading the names in the headers:
>>
>> `$ grep ">" silva.frn | less`
>>
>> You can be more precise about their diversity by examining their taxonomy:
>>
>> `$ grep ">" silva.frn | sed 's/.*_Archaea;//' | sed 's/;.*//' | sort | uniq -c | sort -nr`
>>     68 Halobacterota
>>     45 Crenarchaeota
>>     39 Nanoarchaeota
>>     32 Thermoplasmatota
>>     16 Euryarchaeota
>>      9 Micrarchaeota
>>      6 Altiarchaeota
>>      5 Aenigmarchaeota
>>      4 Asgardarchaeota
>>      3 Nanohaloarchaeota
>>      3 Iainarchaeota
>>      3 Hydrothermarchaeota
>>      2 Hadarchaeota
>>
>> This command extracts fata headers, then removes everything in this line except for the term that appears after
>> 'Archaea' (corresponding to the archaeal phylum from which this sequence was extracted), and then counts the number of
>> occurrences of each phylum.
> {: .solution}
{: .challenge}

### Align the sequences

The next step in our pipeline is to align your newly found 16S rRNA sequences to this group of curated, database sequences. 
First, you will need to put them all in the same Fasta file. To do that, you can use the command “cat” (for “concatenate”):
~~~
$ cat file_1 file_2 file_3 … file_n > output_file
~~~
{: .bash}

Once you replace the name of the files above, this command will concatenate (i.e. simply print sequentially in the same file) 
all your 16S rRNA fasta files, together with the database 16S rRNA file. Write the output to a file called “16S_rrna_bins_silva.frn”.

> ## Exercise: Concatenate all 16S rRNA sequence files
>>
>> ## Solution
>>
>> `$ cat silva.frn ../01_annotation/prokka_bin.*/*.ffn.rRNA.frn > 16S_rrna_bins_silva.frn`
> {: .solution}
{: .challenge}

Next, we can go ahead an align these sequences. To do this, we will use “MAFFT”, a common aligner software. MAFFT includes multiple
algorithms, and it adjusts its algorithm automaticaly based on the size of the input alignment. Run mafft with no arguments 
(just `$ mafft`) to learn how to provide input and output file names, and then run it on your sequence file. The process may take
about a minute.

Let’s take a look at the resulting alignment. In the 'scripts' folder, you will find a tool called 'alan_dt', which is a version of 
'less' that allows you to visualise alignments. Simply use it by running:
~~~
~/data_bb3bcg20/bin/scripts/alan_dt your_alignment
~~~
{: .bash}

Visualise the alignment by using the keyboard arrows (move right-left to see more sites of the alignment, and up-down to see more 
sequences).

> ## Exercise: Interpret the alignment
>>
>> - Do the sequences look well aligned?
>> - Are there large sections “missing” for different sequences? Put another way: are there many gaps?
>> 
>> ## Solution
>>
>> `$ mafft 16S_rrna_bins_silva.frn > 16S_rrna_bins_silva.frn.mafft`
>>
>> `$ ~/data_bb3bcg20/bin/scripts/alan_dt 16S_rrna_bins_silva.frn.mafft`
>>
>> Many sites seem clearly well aligned (stretches of characters are highly conserved across the alignment, as expected). However,
>> there are many gaps in this alignment, both in extremely gappy columns (i.e. uncommon insertions in those sites) and rows
>> (i.e. sequences missing most of the 16S rRNA). This is normally OK. Indels are relatively common across large evolutionary
>> distances, and sequencing/assembly artefacts may also be at play. 
>>
>> Some of the sequences coming from our bins look quite different, as expected from bacterial sequences aligned together with
>> archaeal sequences.
> {: .solution}
{: .challenge}

### Trim the alignment

To avoid including alignment sites that may be uninformative (or even misleading, if erroneously aligned) for a phylogeny, 
let’s remove sites (columns) containing many gaps. You can do so by running the software “trimal”. First run the software with the 
option “-h” to find out how to use it, and then run it with your alignment to remove all columns containing over 50% gaps (hint: 
option “-gt 0.5”). Visualise the result using alan_dt to make sure the alignment looks more complete.

> ## Exercise: Trim sites containing over 50% gaps
>>
>> ## Solution
>>
>> `trimal -in 16S_rrna_bins_silva.frn.mafft -out 16S_rrna_bins_silva.frn.mafft.trimal05 -gt 0.5`
> {: .solution}
{: .challenge}

### Remove incomplete sequences

Another possible issue for phylogenetics could be to include sequences that are highly incomplete, 
i.e. mostly represented by gaps. In such cases, phylogenetic inference software will not have enough information to evaluate 
the relatedness between sequences that do not have many homologous sites. To remove sequences (rows) formed by 50% gaps or more, 
use another provided script:
~~~
perl ~/data_bb3bcg20/bin/scripts/remove_gappy_seqs.pl 0.5 alignment > reduced_alignment
~~~
{: .bash}

Again, make sure to replace “alignment” and “reduced_alignment” with your own file names. 

> ## Exercise: Remove sequences formed mostly by gaps
>>
>> - How many sequences were removed?
>> - Were they correctly removed?
>>
>> ## Solution
>>
>> `perl ~/data_bb3bcg20/bin/scripts/remove_gappy_seqs.pl 0.5  16S_rrna_bins_silva.frn.mafft.trimal05 > 16S_rrna_bins_silva.frn.mafft.trimal05.over50p`
>>
>>
>>  Removing sequences under 0.5 of Alignment length: 1451 (0.5 = 725.5)
>>
>>   719 sites. >AOLX01000033.1079.1801_Archaea;Halobacterota;Halobacteria;Halobacterales;Halomicrobiaceae;Haloarcula;Haloarcula_argentinensis_DSM_12282
>>
>>   540 sites. >DAMT01000008.79347.79893_Archaea;Thermoplasmatota;Thermoplasmata;Marine_Group_II;Euryarchaeota_archaeon_UBA68
>>
>>   347 sites. >LCWM02000049.5124.6170_Archaea;Crenarchaeota;Thermoprotei;Thermoproteales;Thermoproteaceae;Thermoproteus;Thermoproteus_sp._CP80
>>
>>   363 sites. >LQMQ01000003.1.378_Archaea;Hadarchaeota;Hadarchaeia;Hadarchaeales;Hadesarchaea_archaeon_YNP_45
>>
>>   328 sites. >MDVS01000011.41821.42178_Archaea;Crenarchaeota;Bathyarchaeia;Candidatus_Heimdallarchaeota_archaeon_LC_3
>>
>>   316 sites. >MGWD01000040.73779.74105_Archaea;Thermoplasmatota;Thermoplasmata;Methanomassiliicoccales;uncultured;Euryarchaeota_archaeon_RBG_19FT_COMBO_69_17
>>
>>   274 sites. >MNDT01000004.15584.15890_Archaea;Crenarchaeota;Bathyarchaeia;archaeon_13_2_20CM_2_53_6
>>
>>   251 sites. >NJDP01000097.1.769_Archaea;Crenarchaeota;Thermoprotei;Desulfurococcales;Acidilobaceae;Thermodiscus;Desulfurococcales_archaeon_ex4484_58
>>
>>   653 sites. >NJEF01000015.11187.12008_Archaea;Crenarchaeota;Thermoprotei;Desulfurococcales;Ignisphaeraceae;uncultured;Desulfurococcales_archaeon_ex4484_204
>>
>>   503 sites. >NZCB01000001.1.514_Archaea;Thermoplasmatota;Thermoplasmata;uncultured;Candidatus_Pacearchaeota_archaeon
>>
>>   273 sites. >NZER01000159.1.333_Archaea;Nanoarchaeota;Nanoarchaeia;Woesearchaeales;Candidatus_Pacearchaeota_archaeon
>>
>>   465 sites. >NZER01000214.1.485_Archaea;Nanoarchaeota;Nanoarchaeia;Woesearchaeales;SCGC_AAA011-D5;Candidatus_Pacearchaeota_archaeon
>>
>>   620 sites. >PAWD01000005.37468.38108_Archaea;Crenarchaeota;Nitrososphaeria;Nitrosopumilales;Nitrosopumilaceae;Thaumarchaeota_archaeon
>>
>>   334 sites. >PBOE01000046.6371.6706_Archaea;Thermoplasmatota;Thermoplasmata;Marine_Group_II;Euryarchaeota_archaeon
>>
>>   374 sites. >PCCI01000037.1.385_Archaea;Thermoplasmatota;Thermoplasmata;Marine_Group_III;Euryarchaeota_archaeon
>>
>>   372 sites. >PXRK01000025.1.421_Archaea;Halobacterota;Halobacteria;Halobacterales;Haloferacaceae;Halovenus;Halobacteriales_archaeon_QS_4_66_20
>>
>>   266 sites. >PXRX01000041.1.319_Archaea;Halobacterota;Halobacteria;Halobacterales;uncultured;Halobacteriales_archaeon_QS_8_69_26
>>
>>   424 sites. >QEFD01000059.1.459_Archaea;Crenarchaeota;Thermoprotei;Sulfolobales;Sulfolobaceae;Acidianus;Acidianus_hospitalis
>>
>> Removed 18 sequences
>>
>>
>> Based on the tool's output, 18 sequences were removed, which were all formed by less than 725 characters (half of the total alignment length).
>> You visualise the difference with the previous alignment by using alan_dt.
> {: .solution}
{: .challenge}


### Reconstruct a phylogeny

Let’s now run the 16S rRNA tree using IQ-Tree, a sophisticated maximum-likelihood phylogenetics inference software. 
We will run it with an evolutionary model that considers different probabilities for the exchange of any pair of nucleotides. 
Additionally, we will allow four different categories of evolutionary rate, to account for the fact that the 16S rRNA gene contains 
regions that are heavily conserved, and others that evolve faster:
~~~
f=final_alignment
iqtree2 -s $f -m GTR+G4 -pre $f.GTRG4
~~~
{. bash}

Note that this step can again take a few (ca. 5-10) minutes. Finally, we need to visualise this tree (file with suffix “.treefile”). 
To do this, use Figtree, a common phylogeny visualisation software. Download it from https://github.com/rambaut/figtree/releases. 
Download the ”.zip” file for Windows, or the ”.dmg” file for Mac. Decompress and open it. If it does not work, you can use the online 
tool iTOL (https://itol.embl.de/):
-	With FigTree: click ”File”, ”Open” and select the ”.treefile” file you explored above. To facilitate reading the tree,
-	enlarge the window size to cover your screen, and increase the font size (”Tip label”, Font size) as appropriate. Finally,
-	select ”Tree” and then ”Order” (ordering: ”increasing”). Root using the option “Midpoint rooting”
-	With iTOL: upload your tree, and change font size, (“Label options”). Root using “Midpoint rooting” (bottom of the “Advanced” tab).

> ## Exercise: Analyse the tree
>> 
>> - Does your tree make sense, in general?
>> - Are sequences from the same taxonomic group generally clustered?
>> - Is the root in the correct place? Can you identify which place is correct, and reroot the tree there?
>> - Do all sequences from the same genome cluster together?
>> - What species do you think your 16S rRNA sequences represent?
>> - Can you classify both of your archaeal bins?
>>   
>> ## Solution
>>
> {: .solution}
{: .challenge}


