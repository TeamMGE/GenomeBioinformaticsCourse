---
layout: page
title: Setup
root: .
---

## Access to the compute server 
You will need a terminal program to access the server. This **is already available on Mac and Linux**, and can be found by searching for 'Terminal'.

### Windows
#### MobaXTerm
- Go to the [MobaXTerm download page](https://mobaxterm.mobatek.net/download.html)
- Download
- Follow the installation instructions, if any 

If you have any problems, don't hesitate to ask the teachers for help.

## Log in to Gemini

From the Terminal, you can access the Linux environment for this course, which will take place at the University server Gemini. You can access Gemini through SSH (Secure Shell protocol), which allows connecting securely to a remote server. In the command line, do:
~~~
$ ssh username@gemini.science.uu.nl
~~~
{: .bash}

Replace "username" by your UU username. After you do this, you will need to input your password. Welcome to Gemini!

## Load the course module

In order to access the software you will need for this course, you need to run the following command:
~~~
$ course b3-b3bcg20
~~~
{: .bash}

## Explore and get settled

Remind yourself of how to work in a linux environment. Check where you currently are by running:
~~~
$ pwd
~~~
{: .bash}

And check what files are available to you by doing:
~~~
$ ls -l
~~~
{: .bash}

You will see the folder "data_bb3bcg20". Light blue means that it is a 'soft link' to a different location, but you can treat this as a regular folder. Check the contents of this folder by doing:
~~~
$ ls -l data_bb3bcg20/
~~~
{: .bash}

Lastly, generate your own work environment in your home directory:
~~~
$ mkdir GenomeBioinformatics/
~~~
{: .bash}

You are all set!
