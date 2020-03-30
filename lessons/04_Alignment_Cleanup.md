Approximate time: 20 minutes

## Learning Objectives

- Sort and Index SAM/BAM file
- Mark duplicate reads in BAM file

<img src="../img/workflow_cleanup.png" width="200">

## Sort SAM file

It's necessary for downstream applications to sort reads by reference genome coordinates.
This will assist in fast search, display and other functions.
```markdown
SRR098401.109756285 163 chr10 94760647 60 76M = 94760653  82 ATTA…    ?>@@... 
                        ^^^^^^^^^^^^^^
                         coordinates
```

We’ll use the [Picard](https://broadinstitute.github.io/picard/) toolkit for SAM file manipulation.

Open another script in our course directory called picard.sh
```markdown
cd ..
nano picard.sh
```


Enter the following text:
```markdown
module load picard/2.8.0

picard SortSam \
INPUT=results/na12878.sam \
OUTPUT=results/na12878.srt.bam \
SORT_ORDER=coordinate
```

We have input our SAM file and we will output a Binary Alignment Map (BAM) file, which is a compressed version of SAM format.

Exit nano by typing `^X` and follow prompts to save the file `picard.sh`.

To run the script:
```markdown
sh picard.sh
```


Result 
```markdown
[Fri Nov 22 15:33:22 EST 2019] picard.sam.SortSam …
…
[Fri Nov 22 15:33:22 EST 2019] picard.sam.SortSam done. 
```

Take a look at the results directory:
```markdown
ls results
```

Result:
```markdown
na12878.sam  
na12878.srt.bam 
```

## Mark Duplicates in BAM file
In the sequencing process, many copies are made of a DNA fragment
The amount of duplication may not be the same for all sequences and can cause biases in variant calling
Therefore, we mark the duplicates so the variant caller can ignore them.


Duplicate reads are identified based on their alignment coordinates and CIGAR string.
For example, the below alignment appears to have a G to A mutation in the majority of reads:
<img src="../img/dup_pre.png" width="200">

However, when the duplicates are removed, the number of reads supporting the mutation drops to one.
<img src="../img/dup_post.png" width="200">




the hbc:
https://github.com/hbc/NGS_Data_Analysis_Course/blob/master/sessionVI/lessons/01_alignment.md

