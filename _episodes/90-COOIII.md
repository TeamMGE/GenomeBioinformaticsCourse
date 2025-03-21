---
title: "Block 3 - COOIII: Large-scale chromosomal variation in Saccharomyces cerevisiae (part 3)"
start: true
teaching: 0
exercises: 180

questions:
- How can you we identify large-scale genomic differences by making use of next-generation sequencing data?

objectives:
- Map genomic sequencing reads to a reference genome
- Use read mapping information to identify structural variants
- Interpret the results of structural variant prediction algorithms

keypoints:
- You can work with genome assemblies and next-generation sequencing data.  
- You can detect large-scale genomic differences by interpreting read mappings to a reference genome.
---

# Large-scale chromosomal variation in *Saccharomyces cerevisiae*
Genome sequences of individuals of the same species can differ significantly, e.g. two humans are expected to differ at 0.5% of the nucleotides. Next to these single nucleotide differences, genomes can differ by larger structural variations such as translocations, inversions, duplications, or deletions. Mobile genetic element (transposon) insertions and deletions can further establish genetic diversity. Collectively, these large-scale variations often affect hundreds of nucleotides can significantly impact the phenotype.

Bakers’ yeast has been traditionally used as a model to study how genotypic variations is mechanistically established. The availability of sequencing data of hundreds of different yeast isolates enables us to study the emergence and consequences of structural variation in detail. Heree, we will work directly with sequencing reads.

## Determine structural variants using sequence-read mapping (~120 min)
So far, we made use of genome assemblies and whole-genome alignments to identify large-scale chromosomal rearrangemnents. However, high-quality genome assemblies that allow whole-genome alignments to an sufficient level are very often not available for multiple individuals or strains of the same species. Genomic re-sequencing of a large number of different individuals (e.g. humans), cultivars (e.g. crops), or strains (e.g. fungi or bacteria) has become an important approach to study the genetic diversity between individual within a population. These approaches typically sequence genomes with short-read sequencing technologies (e.g. Illumina). Short-read data is then not assembled into a genome sequence but is mapped onto a single reference genome to identify genetic variation. The mapping of the short-read data provides information about the nucleotide diversity in the population, e.g. the number of single-nucleotide polymorphisms. Furthermore, short-read sequencing data allows to analyze the mapping patterns to identify structural variations in the re-sequenced indidviduals/isolates compared with the reference genome assembly.

We will be using sequencing data from *S. cerevisia*e strain UWOPS03-461.4 to identify structural variations compared to the *S. cerevisiae* S288C long-read genome assembly. To identify structual variant we will **i)** map the reads to the genome assembly, and **ii)** use bioinformatics programms that exploit mapping information to identify structural variants (see Mahmoud and colleagues, [Fig. 1](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-019-1828-7).

Use commands including ls and pwd to localize llumina short-read sequencing data of strain UWOPS03-461.4 in the data storage folder. Then create a symbolic link to your own folder with the ln -s command.

To be able to identify structural variants, we first need to map the sequencing data (the reads) to the referene genome. A commonly used tool to map sequencing reads to a reference genome assembly is `bwa`. 

Before mapping reads to the genome assembly, `bwa` needs to generate an genome index (similar to a suffix tree, see lecture). This index is important to speed-up the search for matching positions and allows `bwa` to map millions of reads in reasonable time.

~~~
$ bwa index Yue2017_S288C.genome.fa
~~~
{: .bash}

Now we can map the short-read data to the reference genome using `bwa mem`. To run `bwa mem` we need the reference as well as the input reads; the input reads are stored in two separated fastq files, one for the forward read and one of the backward read that come from the same DNA fragment.

*Important:* Running `bwa mem` will take quite some time (~30 min). To be able to continue within the timeframe of this excercise, you might want to consider to obtain the already mapped bam file from the intermediate results folder.

If you decide to go ahead and map the reads yourself, you can now run a `bwa mem` command to map the UWOPS03-461.4 short-read data to the S288C long-read genome assembly. It is important that your redirect the output (with the `-o` option) in a file as otherwise the alignment will be 'flushed' into the terminal.

~~~
$ bwa mem Yue2017_S288C.genome.fa Yue2017_UWOPS034614.resequencing.R1.fastq.gz Yue2017_UWOPS034614.resequencing.R2.fastq.gz -o Yue2017_UWOPS034614.resequencing.sam
~~~
{: .bash}

BWA produces a file in the sam format, which is a standard format to store information of read mappings towards a reference genome. We can converted the sam file into a bam file, a binary version of the file that is smaller and more quicker accessbile for computers. 

~~~
$ samtools view -Sb Yue2017_UWOPS034614.resequencing.sam > Yue2017_UWOPS034614.resequencing.bam
~~~
{: .bash}

Lastly, we need to sort the bam file and Index the bam file, either produced by yourself or obtained from the intermediate results folder.

~~~
$ samtools sort Yue2017_UWOPS034614.resequencing.bam -o Yue2017_UWOPS034614.resequencing.sort
$ samtools index Yue2017_UWOPS034614.resequencing.sort.bam
~~~
{: .bash}

Now that we have mapped the reads successfully to the reference genome, we can first determine regions in the reference genome that are not covered by reads, and thus are likely absent in UWOPS03-461.4 and present in the reference genome. We can use a similar strategy as employed in a previous episode, in which you used `bedtools genomecov` to identify regions not covered with sequencing alignments. Note the different parameters of bedtools genomecov that allows you to directly use a bam file as in input source.

> ## Exercise
>
> Prepare and execute  `bedtools genomecov`  to identify regions in the S288C reference genome that are absent in UWOPS03-461.4.
>
>> ## Solution
>>
>> ~~~
>> bedtools genomecov -d -ibam  Yue2017_UWOPS034614vsS288C.sort.bam | awk '{if ($3 == 0){print $1,$2,$2}}' | tr " " "\t" | bedtools sort -i - > Yue2017_UWOPS034614vsS288C
>> ~~~
>> {: .bash}
>> 
> {: .solution}
{: .challenge}

We can further summarize regions that are not covered in close proximity by using `bedtools merge`. For example, to merge regions with <= 5000 bp distance between them, we can use:

~~~
$ bedtools merge -d 5000 -i Yue2017_UWOPS034614vsS288C.sort.bed
~~~
{: .bash}

> ## Exercise
>
> - Which regions seem to be particular prone to absences? Compare the coordinates to the chromosomes (size) for this genome assembly.
> - Could you try to provide a technical or biological reasons to explain your observation?
>   
>> ## Solution
>>
> Many telomeric/subtelomeric regions seem absent. This could be either due to extensive variation in sub-telomeres as suggested by [Yue and colleagues](https://www.nature.com/articles/ng.3847). However, we currently could not rule out a technical problem due to the repetetive nature of these regions which challanges read mapping and alignments in general.
>> 
> {: .solution}
{: .challenge}

Lastly, we will use [Delly](https://academic-oup-com.utrechtuniversity.idm.oclc.org/bioinformatics/article/28/18/i333/245403), one of the many available bioinformatic tools designated to systematically analyse the mapping information of paired-end sequencing reads to identify different types of structural variants. Delly uses both split-reads and read mapping analyses (discordant mapping) to identify duplications and deletions as well as inversions and translocation; see the paper for details.

Delly first calls variants based on the read mapping information provided by a bam file. 
~~~
$ delly call -g Yue2017_UWOPS034614.genome.fa Yue2017_UWOPS034614.resequencing.bam -q 20 -o Yue2017_UWOPS034614.resequencing.sv.bcf
~~~
{: .bash}

The output file generated by Delly is a binary [vcf](https://samtools.github.io/hts-specs/VCFv4.2.pdf) file (bvcf), a file format that is commonly used to store information about sequence variants such as single-nucleotide polymorphism (SNPs) as well as larger variants. 

You can convert the binary vcf (bvcf) file into a text file using `bcftools view`. Combined with command line tools that allow u to filter for variants that passed the quality filters, this will provide the number of structural variants that are detected by Delly.

~~~
$ bcftools view Yue2017_UWOPS034614.resequencing.sv.bcf | grep -P "\tPASS" | cut -f8 | cut -f2 -d ";" | sort | uniq -c 
~~~
{: .bash}

> ## Exercise
>
> How many structural variants have been identified, and what is the most common structural variant?
> 
>> ## Solution
>>
>>  48 SVTYPE=BND, 212 SVTYPE=DEL, 11 SVTYPE=DUP, 6 SVTYPE=INV 
>> 
> {: .solution}
{: .challenge}

You previously generated a whole-genome alignment of UWOPS03-461.4 with the reference genome assembly. When looking at the whole-genome alignment on chromosome VII (if you cannot seem them, use mummerplot with the -r and -x option to zoom into this specific chromosome and region), one can identify three larger regions between 530 kb and 580 kb that are potentially deleted from UWOPS03-461.4 when compared with the reference strain S288C.

> ## Exercise
>
> Were these regions also identified by Delly and can you identify them in the vcf output (you need to convert the bvcf to text before inspecting the results)?
> 
>> ## Solution
>>
>>  Delly only finds two out of the three deletions, likely due to read mapping issues around the deletions
>> 
> {: .solution}
{: .challenge}

We have previously seen that *S. cerevisiae* strain UWOPS03-461.4 has a large number of inter-chromosomal translocations compared with reference strain S288C.

> ## Exercise
>
>
> Can you identify some of these translocations in the read mappinh? Why could it be challanging to identify translocations using short-read data? How could this potential challenges be addresed?
> 
>> ## Solution
>>
>>  Structural rearrangements are often linked to repeats and transposons and to losses of genetic material. Consequently, mapping of short sequencing reads at these locations is very challenging, and thus translocations are often not found. In this particular case, we know from the whole-genome alignment that breakpoints and deletions are often associated with *Ty* transposonse, and thus we expect that SV detection with short read data is very challenging. An alternative would be to use whole-genome alignments to systematically describe these type of structural variations (how could this be conceptually done?) or using reads that are sufficiently long to span repeat or other complex regions. This is one of the reasons why third generation long-read sequencing is superior in identifying structural variation.
>> 
> {: .solution}
{: .challenge}


