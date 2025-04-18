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

#  A curious biofilm

You are biking on your way to the Utrecht Science Park, and your bike’s chain comes out. You stop, and atart to fix it. Your hands get greasy, it’s raining and you feel miserable. Once you are done, you look up. You had just stopped by a beautiful canal, and the falling of droplets on the water is mesmerising. Between the grass and the water, sheltered below a rock formation, you notice an odd slime. It is of a brownish, orange-y color, and you recognise it is a biofilm. You have learnt that microorganisms can form their own environments, where they interact, compete and thrive. You wonder what could be in there…

<img width="573" alt="image" src="https://github.com/TeamMGE/GenomeBioinformaticsCourse/blob/main/fig/Block1_COO-I_Intro1.png" />
![test](../fig/Block1_COO-I_Intro1.png)


Since the year 2000, the emergence of Next Generation Sequencing (NGS) has facilitated the development of metagenomics. A metagenome is the combined genomic information of all organisms in a given sample. The metagenome contains information about the composition (taxonomy) and functions (encoded genes) of a microbial community. There are different ways to process and analyze metagenomes. First, targeted amplification and sequencing of the 16S ribosomal RNA gene, also known as 16S rRNA amplicon sequencing, is used to determine the relative abundances of taxa in the sample (taxonomic profiling). Second, shotgun metagenomic sequencing reads random parts of the total community DNA (or RNA in the case of meta-transcriptomics), allowing genomes (or transcriptomes) of all organisms to be sampled. Thus, genome-resolved metagenomics is an approach that enables extracting, sequencing, and analysing DNA without the need for PCR amplification and primers. Using metagenomics, we can now study the genomic content of uncultivated microorganisms from a natural environment, which helps us to identify them, determine their potential function within the environment, and guide us to their successful cultivation. Microbial communities have been investigated to contribute to various global biogeochemical cycles (carbon, nitrogen, phosphorus cycling), the cycling of greenhouse gases through, e.g., respiration of organic matter (methane, CO2, N2O), and the metabolization of toxic compounds (key word: bioremediation). Furthermore, mining of the genomic potential has also led to the discovery of many new bioactive substances, including novel antibiotics.

You decide to take action. You sample the biofilm, and take it to your Bioinformatics teachers. Since there is little biomass, and we want to avoid contaminants, you all agree to first try and enrich some of the microorganisms, focusing on those who can survive (or even thrive) without oxygen. To do that, you include your biofilm in an anaerobic bioreactor, and, after a few days, you have a stable community. Then, you take a sample from this community to the sequencing facility, where they disrupt cells using sonication, and purify DNA using beat-based methods. Then, they prepare DNA libraries to input the DNA to a PacBio sequencer. Once the long-read sequences are ready, they send them your way. Now, you focus on genome-resolved metagenomics of this prokaryotic community.

<img width="573" alt="image" src="https://github.com/TeamMGE/GenomeBioinformaticsCourse/blob/main/fig/Block1_COO-I_Intro2.png" />


# Steps for this analysis
- [Sequencing](https://github.com/TeamMGE/GenomeBioinformaticsCourse/blob/main/_episodes/11-COOI.md)
- Read processing
- Assembly
- Binning
- Bin refinement
- Assessment

