---
title: "Block 3 - COOI: Recent ploidy changes in the Saccharomycotina (part 1)"
start: true
teaching: 0
exercises: 120

questions:
- How can you determine changes in ploidy using next-generation sequencing data?

objectives:
- Generate k-mer profiles from short-read data
- Analyze and interprete k-mer profiles to estimate genome size, heterozygocity, repeat content
- Analyze and interprete k-mer profiles to estimate ploidies
- Generate allele frequency plots from short-read data
- Analyze and interprete allele frequencies to estimate ploidies

keypoints:
- You can work with next-generation sequencing data
- You can detect (recent) ploidy changes in fungi using next-generation sequencing data 
---

# Saccharomyces cerevisiae as a model system to study eukaryotic genome evolution
The focus of the three COOs in Block 3 is ‘Eukaryotic genomes and genome evolution’. We will apply different bioinformatics approaches to analyze genome sequencing data to obtain insights into different processes or phenomena that generate genetic variations such as ploidy shifts (COOI and COOII) and large-scale structural variation (COOII and COOIII).

The bakers' yeast Saccharomyces cerevisiae is one of the most well-known eukaryotic model organisms. The *S. cerevisiae* genome was the first completely sequenced eukaryotic genome. The bakers’ yeast genome is only ~12 Mb in size and only encodes ~6,000 protein-coding genes, which is rather small compared with most other fungi or other eukaryotes. *S. cerevisiae* played an important role in advancing our understanding mechanisms contributing eukaryotic genome evolution, and its experimental and genetic tractability greatly facilitated research into gene functions. Consequently, *S. cerevisiae* serves as a model for more complex eukaryotes, including humans.

Throughout these COOs, we’ll use the bakers’ yeast and allied species as models for other more complex eukaryotes. Due to the small size and the rich available data, it provides an ideal starting point and test case to explore genome evolution in eukaryotes.

# Recent ploidy changes in the Saccharomycotina
The bakers' yeast belongs to the Saccharomycotina subphylum, a taxonomic group that contains many species colloquially referred to as budding yeasts. Budding yeasts are mainly thriving as saprophytes, but some are important pathogens of plants and animals, including human. Furthermore, many members of this sub-phylum are commonly exploited in agriculture, food production, or biotechnology. Interestingly, many of these important species are hybrids that are formed by the fusion of two genetically distinct parents. Next to the doubling of the number of chromosomes by hybridization (also referred to as allopolyploidy), genomes of a single species can also double (autopolyploid). Changes in ploidy, either due to allo- or autoployploidy can generate important variation and is thought to play an important role to fuel adaptive genome evolution.

## 1. Estimate genome size, heterozygosity, and repeat content using k-mer frequencies
The genome size, heterozygosity rate and repeat content are important genome characteristics that provide a framework to study genome evolution, but also to assess for instance the quality (completness) of genome assemblies.

Some of these characteristics can be directly derived from unprocessed short reads using k-mer profiles. The k-mer profile (sometimes called k-mer spectrum) measures how often k-mers, substrings of length k, occur in the sequencing data. The profiles reflect the complexity of the genome: homozygous genomes have a simple Poisson profile with a single peak approximately at the overall genome-wide coverage of the data, i.e. if a single region in the genome is sequenced x times, we expect to identify the same k-mer to also occur x times in our data. Furthermore, this distribution provides an indication of the sequencing errors present in the data, i.e. k-mers that are are unique or very rare in the data. Heterozygous genomes typically show a characteristic bimodal profile where one of the peaks represents the homozygous fraction and another peak the heterozygous fraction. Repetetive regions such as transposons and/or duplications can add additional peaks at even higher k-mer frequencies. Thus, we can directly obtain information based on the genome size, the estimated heterozygosity and the repeat content directly from the shape of the k-mer frequency distribution and from the abundances of k-mers in the data.

We will use this approach to first estimate the haploid genome size of a single yeast strain.

Use commands including `cd`, `ls` and `pwd` to localize the short-read sequencing data from *S. cerevisiae* strain CBS7837 in the data storage folder (**~/data_bb3bcg20/**). Then create a symbolic link to your own folder with the ln -s command. 

~~~
$ ln -s ~/data_bb3bcg20/Block3/COOI/raw/reads/ScereCBS7837_* .
~~~
{: .bash}

To determine the genome size, we first need to calculate the k-mer profile using the short-read data for CBS7837. We will use jellyfish to summarize the k-mers that are present in the short-read data.

Take a look at the command line parameters

~~~
$ jellyfish count --help
~~~
{: .bash}
> ## Exercise
>
> Prepare but do not execute a simple jellyfish count command that will count k-mers length 21 and k-mers occuring on both strands (canonical). You have to define the memory (-s option) and set it to 1000000000 (1 Gb). Store the output in a new file (-o option). Important Running jellyfish will take quite some time (~30 min). To be able to continue within the timeframe of this COO, obtain the jellyfish count file (ends with .jf) from the intermediate results folder.
> 
>> ## Solution
>>
>> `jellyfish count -C -m 21 -s 1000000000 -o ScereCBS7837.jf <(zcat ScereCBS7837_R1.fastq.gz) <(zcat ScereCBS7837_R2.fastq.gz)`
>> This will execute jellyfish count. It is important to use canonical counts (-C, both strands), and provide the kmer length (m, 21). As read files are often zipped (due to their size), these need to first be unzipped. This can also be done 'dynamically' during the jellyfish call using `<(zcat file1.fq.gz) <(zcat file2.fq.gz)` (see the example above.
> {: .solution}
{: .challenge}

Now we can convert the output into a human-readable count table using jellyfish histo.

> ## Exercise
>
> Prepare and execute a simple jellyfish histo command to redirect the output of the k-mer counts into a text file.
> 
>> ## Solution
>>
>> `jellyfish histo ScereCBS7837.jf > ScereCBS7837.count.txt`
>>
> {: .solution}
{: .challenge}

> ## Exercise
>
> Take a look at the text file. What is noted in the first and second column of the file?
> 
>> ## Solution
>>
>> `head ScereCBS7837.count.txt`
>> The first column contains the k-mer with coverage x (x-axis of a kmer plot), the second column displays the frequency in which the k-mer with coverage of x occurs in the data (y-axis of a kmer plot)
>>
> {: .solution}
{: .challenge}

We can now generate a graphical representation of the k-mer profile, similar to what you have seen in the lectures. We can generate this representation using Python (pandas and seaborn) and/or or R (e.g., ggplot), depending on what you feel most familiar with.

> ## Exercise
>
> Open a text editor or the python console (`python`) to write to code. The code should load the data generated by `jellyfish histo` and to visualize it as a simple scatterplot where the x-axis shows the k-mer coverage and the y-axis the k-mer frequency.
> 
>> ## Solution
>>
>> ~~~
>> import seaborn as sns #imports seaborn
>> import pandas as pd #imports pandas
>> import matplotlib.pyplot as plt #imports matplotlib
>> kmers = pd.read_csv("ScereCBS7837.count.txt",sep=" ",header=None) #read the kmer-count files
>> kmers.columns = ['Coverage','Frequency'] #name the columns
>> sns.scatterplot(data=kmers, x='Coverage', y='Frequency') #plot the data as a scatterplot
>> ~~~
>> 
> {: .solution}
{: .challenge}

> ## Excercise
>
> Inspect the figure. Based on your visualization, what is your interpretation with regard to the occurence of errors and high-frequency k-mers?
>
>> ## Solution
>>
>> There are many  k-mers that have little coverage, i.e. they occur just once in the data and thus are likely errors. Many k-mers also occur in high numbers, some up to 10,000x coverage. These could represent repeats or part of the mitochondrial genome that occurs in high frequenc.
>>
> {: .solution}
{: .challenge}

To be able to better analyse the k-mer distribution to determine the peak in k-mer coverage, we need to exclude high-abundance and high-frequency k-mers. 

> ## Excercise
>
> Modify the scatterplot to limit the x- and y-axis to focus on the data between 0 and 200 on the x-axis and 0 and 350000 on the y-axis.
>
>> ## Solution
>>
>> ~~~
>> sns.scatterplot(data=kmers, x='Coverage', y='Frequency')
>> plt.ylim(0,350000) #focusses the y-axis to maximum 350000
>> plt.xlim(0,200) #focusses the x-axis to a maximum of 200
>> ~~~
>> 
> {: .solution}
{: .challenge}

Based on your updated visualisation, you can now determine the number of height of the peaks in the k-mer distribution.

> ## Excercise
>
> How many peaks can you observe in the k-mer distribution plit? What does this suggest with respect to the ploidy of the isolate you analyzed? What is the average k-mer coverage for each of the peaks? Based on this observations, do you conclude that the isolate very heterozygous?
>
>> ## Solution
>> 
>> You can clearly observe two peaks, suggesting that this isolate is diploid. We can see a small heterozygous peak (at around 40x) and larger homozygous peak (at around 80x). Based on the height and size of the first peak, the level of heterozygosity is likely small (you can compare this plot to slides from the lecture).
>>
> {: .solution}
{: .challenge}

We can use the information to estimate the overall genome size under the assumption that the total number of k-mers and the k-mer coverage are related to the genome size (see lecture slides for a detailed introduction on this topic). Specifically, we can assume that the genome size can be derived based on the area under the curve and the mean coverage at the homozygous peak. Furthermore, we assume that the k-mers that occur in low coverage but in high frequency are likely sequencing errors that can be safely ignored.

> ## Excercise
>
> Inspect the data in Python and/or R ...
> - ... to identify a reasonable coverage threshold for sequencing errors and generate a subselection of the data that only contains k-mer information we want to keep (i.e., exclude k-mer coverage and frequencies likely associated with errors)
>
> - ... to identify the k-mer coverage of the homozygous peak.
>
>> ## Solution
>>
>> ~~~
>> kmers_sub = kmers.iloc[20:,] #we assume kmers < 20x coverage are errors
>> kmers_cov_hom = kmers_sub.loc[kmers_sub['Frequency'].idxmax(),"Coverage"] #the peak (maximum of data) is at 82. An approximate peak could have also been  determined by inspecting the figure
>> print(kmers_cov_hom)
>> ~~~
>>
> {: .solution}
{: .challenge}

Now that we have the subset and the k-mer coverage of the homozygous peak, we can obtain the area under the curve, i.e. the total number of k-mers, excluding errors, that are part of the k-mer frequency distribution. The total number of k-mers can be obtained by multiplying the k-mer coverage with the k-mer frequency per row summed over the entire dataset, i.e., `sum(k-mer coverage * k-mer frequency).` Then you need to divide the total number of k-mers by the average coverage at the homozygous peak to obtain an estimate of the genome size.

> ## Excercise
>
> What is the estimated genome size of this isolate?
> What size do you expect the yeast genome to be?
> What could be a possible explanation for the discrepancy?
>
>> ## Solution
>>
>> ~~~
>> kmer_count = (kmers_sub['Coverage'] * kmers_sub['Frequency']).sum() #obtain the total k-mers
>> kmer_count / kmers_cov_hom #divide the total number of k-mers by the coverage at the homozygous peak
>> ~~~
>>
>> We obtain an estaimate of around 14.05 Mb, which is slight bigger than the expected 12 Mb of a typical yeast genome. One reason could be that we still considered all k-mers up to 10,000 and that we only ignored some errors on a ad hoc cutoff.
>>
> {: .solution}
{: .challenge}

We now estimated the genome size by hand. However, there is also dedicated bioinformatic software that can be used to analyse k-mer profiles. A commonly used software is [GenomeScope](http://genomescope.org) website that uses the k-mer histogram from `jellyfish` (and other k-mer counting programs) and statistical approaches to estimate the genome size, the repeat content, and the level of heterozygocity.

> ## Excercise
>
>Take the k-mer profile you obtained by jellyfish histo and upload it to GenomeScope. What is the genome size estimated by GenomeScope? Could you explain how it differs compared with your manual calculation?
>
>> ## Solution
>>
>> The genome size is closer to the expected 12 Mb. GenomeScope applies different models to estimate the error profiles and the location of the peaks to estimate genome sizes from the data.
>> 
> {: .solution}
{: .challenge}

