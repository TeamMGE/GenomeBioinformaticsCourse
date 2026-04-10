---
title: "Block 2 - COOII: Making our own orthologs between animals and plotting their conservation: code answers"
start: false
teaching: 0:00
exercises: 0:00
questions: 
- how well (on average) are two orthologs between two species conserved?  
objectives: 
- This page is just to provide example code for the exercise 
- Orthologs between human and mouse, or between human and zebrafish, have very different levels of conservation 
---


# bidirectional_best_blast_hits
```

def make_and_write_bbh(blastfile1, blastfile2, outputfile):
    bh_of_human = {}
    n = 0
    print ("start reading first blast file")
    file=open(blastfile1, "r")
    for line in file:
        n += 1
        elements = line.split()
        id1 = elements[0]
        id2 = elements[1]
        identity = float(elements[2])
        bitscore = float(elements[11])
        if id1 in bh_of_human:
            if bitscore > bh_of_human[id1][1]:
                #we have a new best hit!
                bh_of_human[id1] = [id2,bitscore, identity]
        else:
        ## first time we record something about this guy so let's initilizae it ..'
            bh_of_human[id1] = []
            bh_of_human[id1] = [id2,bitscore, identity]
    file.close()
    ## report 
    print ("number of lines:", n)
    print ("done with reading best hits specis A vs species B")
    print (len(bh_of_human))

    bh_of_mouse = {}
    n = 0
    print ("start reading species B to species A blast file")
    file=open(blastfile2, "r")
    for line in file:
        n += 1
        elements = line.split()
        id1 = elements[0]
        id2 = elements[1]
        identity = float(elements[2])
        bitscore = float(elements[11])
        if id1 in bh_of_mouse:
            if bh_of_mouse[id1][1] < bitscore:
                #we have a new best hit!
                bh_of_mouse[id1] = [id2,bitscore, identity]
        else:
        ## first time we record something about this guy so let's initilizae it ..'
            bh_of_mouse[id1] = []
            bh_of_mouse[id1] = [id2,bitscore, identity]
    file.close()

    print ("number of lines:", n)
    print ("done with reading best hits species B vs species A")
    print (len(bh_of_mouse))

    ## loop over all human proteins with a best hit 
    ## and check whether their best in mouse has them as a best hit
    ## and if they do, write the pair to a file 
    ## report the number of pairs to the screen

    n = 0
    bbh = {}
    for id1 in bh_of_human:
        mouse_id = bh_of_human[id1][0]
        if mouse_id in bh_of_mouse:
            if bh_of_mouse[mouse_id][0] == id1:
                    n += 1
                    bbh[id1] = mouse_id

    print ("Number of orthologs:", n)

    orthologpairs_out = outputfile
    results_file = open (orthologpairs_out,"w")
    for gene in bbh:
        ## print (gene, bbh[gene], bh_of_human[gene][2], bh_of_human[gene][1] ,file=results_file) 3.6 !!!!
        results_file.write( " ".join([gene, bbh[gene], str(bh_of_human[gene][2]), str(bh_of_human[gene][1])]) + "\n" ) 

    results_file.close()

##
## use our function! 
##

make_and_write_bbh ("./blast_out_human_vs_mouse.txt", "./blast_out_mouse_vs_human.txt","./orthologs.txt" )


```
# distribution_of_identities_of_orthologs
```
## one solution:



def plot_identities_of_orthologs (orthologpairs_out):
    identities = []
    file=open(orthologpairs_out, "r")
    for line in file:
        elements = line.split()
        identities.append(float(elements[2]))
    file.close()

    # PLotting a histogram
    import matplotlib.pyplot as plt
    %matplotlib inline

    ##plt.hist([float(x) for x in identities], bins=100)
    plt.hist(identities, bins=50)


    plt.title('Distribution of identity values')
    plt.xlabel('Identity value')
    plt.ylabel('Frequency')
    plt.show()

    ## or do it yourself 
    identities.sort(key=float)
    highscore = float(identities[-1])
    print ("highest score:", highscore)
    nbins = int(20)
    count = {}
    for bin in range (0, nbins+1):
        count[bin] = 0

    for score in identities:
            bin = int (float(score) / highscore * nbins)
            count[bin] += 1

    for bin in range (0, nbins+1):
        print (bin, bin * (highscore/nbins), count[bin])


## Do it
plot_identities_of_orthologs("./orthologs.txt")
```

# also_make_bbh_orthologs_and_identity_distribution_for_human_zebrafish
```
make_and_write_bbh ("./blast_out_human_vs_zebrafish.txt", "./blast_out_zebrafish_vs_human.txt","./orthologs_hz.txt" )
plot_identities_of_orthologs("./orthologs_hz.txt")

```

## optional to plot them in the same plot ... 
```

import matplotlib.pyplot as plt
%matplotlib inline

identities1 = []
orthologpairs_out = "./orthologs_hz.txt"
file=open(orthologpairs_out, "r")
for line in file:
    elements = line.split()
    identities1.append(float(elements[2]))
file.close()

identities2 = []
orthologpairs_out = "./orthologs.txt"
file=open(orthologpairs_out, "r")
for line in file:
    elements = line.split()
    identities2.append(float(elements[2]))
file.close()

plt.title('Distribution of identity values')
plt.xlabel('Identity value')
plt.ylabel('Frequency')
plt.hist([identities1, identities2], bins=20, color = ["orange","green"], label=['human-zebrafish', 'human-mouse'])
plt.legend(loc='upper left')
plt.show()
```
