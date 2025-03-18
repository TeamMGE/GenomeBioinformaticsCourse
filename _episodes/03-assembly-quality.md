---
title: "Genome assembly quality"
teaching: 0
exercises: 45
questions:
- How can we interpret the genome assembly quality of fungal genome assemblies?
objectives:
- Compare genome assemblies in light of their quality.
- Assess the implications of different genome sequencing technologies and assembly approaches on genome assemblies.
keypoints:
- You can compare genome assemblies with quast.
- You can examine and critically reflect on the impact of genome sequencing technologies on fungal genome assemblies.
---

#  Genome assembly quality
We will now analyse *Zymoseptoria* genomes assemblies in more detail. Take a look at the `QUAST` [manual](http://quast.sourceforge.net/quast) to understand how to prepare a suitable command to execute `QUAST` on the command line.

The genome assemblies of some _Z. tritici_ isolates downloaded from NCBI GenBank can be found **data/fungalgenomics_seidl/assemblies/** (state: 2020). It will be useful to make a symbolic link to the data folder in your own home directory (or any other folder your own). You can do this by using `ln -s [source folder] [target folder]` where *source folder* is the name/location of the data folder and *target folder* is the name of the link you wish to create.

QUAST can be run using `quast`. First run the command without arguments (i.e. options nor files) to get a list of basic options (run with `--help` for the expanded help).

~~~
$ quast --help
~~~
{: .bash}

We will now prepare and execute a QUAST analysis for one *Zymoseptoria* genome assembly. We will use the genome assembly of strain ASM22370. We will compare this strain to reference IPO323 genome and annotation as a comparison. Please make sure the output goes to your local folder and specify the number of threads (max=2).

~~~
$ quast -o quast_output -g gene:data/IPO323.gff -r data/IPO323.fna data/ASM22370v2.fna -t 2 
~~~
{: .bash}

We can now navigate the output of QUAST folder, e.g., by using `cd quast_output`. 

Take a look at the report files (report.html and report.txt). You can look into the text file (report.txt) using `less`.

~~~
$ less report.txt
~~~
{: .bash}

To look into the html file, for example in your browswer. You will need to first copy the file from the server to your local machine.

What type of measurements are reported for the genome assembly? In bacterial genome assembly exercise in week 1, you have already heard about N50. What do the other terms mean and how are they calculated? 

It is particularly relevant to think about the number of contigs of the genome assembly. How many would you expect to find in a perfectly assembled eukaryotic genome? How many contigs would you expect in a typical bacterial genome?

> ## Exercise
> 
> Quest provides useful information about each genome assembly. Based on the data provided, would you consider the genome assembly of strain ASM22370 of high quality? Explain why. What could be a reason? 
>
>> ## Solution
>> 
>> The assembly is not of high quality. The assembly has many contigs and the N50 is generally low. Moreover, we can see many misalignment and genes (genomic features) only being partially present. The reason could be that this genome is sequenced with short-read data, which significantly hampers the assembly process.
>> 
> {: .solution}
{: .challenge}

The report files \(html and txt\) for all the _Zymoseptoria_ genomes \(of those available in 2018\) can be found in the QUAST folder \(**~/data/fungalgenomics_seidl/quast/**\). You can open the files to further assess and compare the quality of more genomes.

> ## Exercise
> 
> Which genome has the highest (excluding the IPO323 reference), and which the lowest NG50 and LG50 values?
>
>> ## Solution
>> 
>> Highest NG50 and LG50: ASM293741v1. Lowest NG50 and LG50: ASM96659v1
>> 
> {: .solution}
{: .challenge}

Take another look on the QUAST quality assessments and evaluate the different key criteria (N50, contig number, etc.). If you would need to decide, which genomes were likely assembled using short-read data and long read data and what guides your decision?

We can now specifically focus on genome assemblies that have high quality (i.e., low number of contigs and high N50). If we compare the number of contigs between the reference genome assembly IPO323 and ST99CH\_3D7, we can observe that ST99CH\_3D7 has less contigs than the reference genome. Do you think contigs here correspond to chromosomes?

What is your biological interpretation of this observation? Does it fit data from the IPO323 genome assembly paper published in [PLoS Genetics](https://journals.plos.org/plosgenetics/article?id=10.1371/journal.pgen.1002070).

{% include links.md %}
