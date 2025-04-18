---
title: "Block 1 - COOI: Recovering a microbial community"
start: true
teaching: 0
exercises: 20
questions:
- What interesting microbes can we find around us?
objectives:
- Practice the usage of command line to handle and analyse sequence files 
- Perform a metagenome assembly, and interpret metagenomic data
keypoints:
- You can obtain genome sequences from environmental sequencing
---


# Sequencing

To start the analysis, you first need to understand your data. The platform used to sequence these samples was of the PacBio Continuous Long Read (CLR) chemistry. 

Start by watching the following videos (16 and 1.5 min):

https://youtu.be/sdxVDy0lSAE

https://youtu.be/_lD8JyAbwEo 


## Set up your environment and get access to the sequencing data

Get to your work environment and get prepared for this COO. Repeat this process for future COOs.

~~~
$ cd GenomeBioinformatics/
$ mkdir -p Block1/COO-I
$ cd Block1/COO-I
$ ln -s ~/data_bb3bcg20/data_bb3bcg20/Block1/COOI/Data .
$ ls -l Data/
$ mkdir Results
$ cd Results
~~~
{: .bash}

These commands have generated a basic folder structure for Block1 and COO-I, and including the folders Data and Results. Moreover, now youu have access to the data, in the form of a symbolic link. Symbolic links are 'ghost' folders that allow access to a file in a different location, thus avoiding the need to generate multiple copies of heeavy files. Now you are ready to get started.

## Check data

The first thing you always have to do with your reads is check their overall quality. This is important because it can help you decide what kind of pre-treatment you have to do. For example, in case of long-read sequencing it sometimes happens that the sequence quality at the end of the reads gets a lot worse. In this case you could decide to trim these low-quality parts of your reads. Another option is that your sequencing provider did not remove the adapter sequences yet, in that case you still need to remove these.

To save space, the file is compressed, but you can read it using the command ‘zless’. 
~~~
$ zless ../Data/Course_sample_metagenome.fastq.gz
~~~
{: .bash}

**What type of file is this? Does it look normal to you?**

You may have noticed that the quality scores are missing. Something may have gone wrong with the communication with the sequencing provider. However, we will still try to see if these reads contain useful information.


