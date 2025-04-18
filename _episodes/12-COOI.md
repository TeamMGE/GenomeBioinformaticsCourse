---
title: "COO-I-2. Data processing"
start: false
teaching: 0
exercises: 30
questions:
- How to assess the quality of the sequencing reads?
objectives:
- Interpret sequencing read quality and filter read files with this information
---


# Data processing

## Quality report using FastQC 

Start by analysing these reads using FastQC. Run the software with the option “-h” to see how it needs to be run.
This is a good step to perform before running any software, as getting acquainted with the main requisites and options may be essential.
~~~
$ fastqc -h
~~~
{: .bash}

> ## Exercise: Generate a quality report
>
>> ## Solution
>>
>> `$ fastqc -o 01_fastqc -f fastq ../Data/Course_sample_metagenome.fastq.gz`
>>
>> This will generate an output folder called "01_fastqc". We can enumerate results folders this way, which will help keeping our files tidy.
> {: .solution}
{: .challenge}

### Examine results

In order to read the results you will need to download the file to your laptop. 
MobaXTerm may allow you to do this easily by navigating the filesystem. Other terminal users can copy the file into their local folders
by using the secure copy protocol. But first, let's unzip our results and then make sure we have the full path to these files:
~~~
$ unzip Course_sample_metagenome_fastqc.zip
$ pwd
~~~
{: .bash}

From a new terminal in your laptop, run the following command. Note: replace "username" by your username, 
the first path to your "01_fastqc" folder, and the second path to a place in your local system (e.g. "~/Downloads/").
~~~
$ scp -r username@gemini.science.uu.nl:/path/to/file /path/to/local/folder
~~~
{: .bash}

Now, open the report by clicking on the HTML file and explore the statistics!

> ## Exercise: Interpret the results
>
> **How variable is the read length? What do you think the compositional variation in your data may mean?**
>
>> ## Solution
>> We can see that our fastq file contains 60099 sequences, and a total of almost 500 million bases. We also see that there is a
>> sequence length range between 50 and 34595 -- quite wide. The first warning page includes read composition. We can see that read
>> composition is quite stable across read length, with G and C being slightly lower than A and T. This indicates a slight bias in
>> GC content in our metagenome (not unusual). However, variation becomes more unstable at very high read lengths (above 20,000 bp).
>> This is probably a result of averaging. Many reads can be used to average composition at low read lengths. However, fewer and fewer
>> very long reads exist, thus generating more variation at high length averages.
>> Another warning is shown at the GC content variation page, where we see multiple peaks instead of the single peak we could expect
>> from a theoretical homogeneous sample. These peaks may represent different GC contents characteristic of the genomes of different
>> organisms, perhaps indicating that only a few highly abundant lineages are represented in our data.
>> Finally, the last warning is indicated in the sequence length distribution page, where we can see a clear peak with a long tail (fewer
>> and fewer very long reads). We also see that we still have a considerable number of short reads.    
>> 
> {: .solution}
{: .challenge}

## Filtering out short reads 

One noteworthy aspect is that there is a wide distribution of read lengths in the data. 
To avoid problems with assemblers focused on long reads, let’s remove short reads. We can do this using the software ‘seqkit’. 
As you did before, run the software using the parameter “-h”. You can see that seqkit offers many different options. We will make 
use of the 'stats' option to quickly gather statistics about the reads, and also the 'seq' option, which allows for multiple
types of sequence filtering options. Here, let's remove all reads with lengths below 1,000 bp.

~~~
$ cd ~/GenomeBioinformatics/Block1/COO-I/Results
$ mkdir 02_readFiltering
$ cd 02_readFiltering
$ seqkit stats ../../Data/Course_sample_metagenome.fastq.gz
$ seqkit seq -m 1000 ../Data/../Course_sample_metagenome.fastq.gz -o filtered_reads.fastq.gz
$ seqkit stats filtered_reads.fastq.gz
~~~
{: .bash}

> ## Exercise: Interpret the results
>
> **How many reads were filtered out? How can you be sure the filtering worked as expected?**
>
>> ## Solution
>> The minimum length moves from 50 bp to 1000 bp, as expected from removing all reads with lenghts below 1000 bp. We can also
>> see that about 400 reads were removed from our dataset. 
>> 
> {: .solution}
{: .challenge}
