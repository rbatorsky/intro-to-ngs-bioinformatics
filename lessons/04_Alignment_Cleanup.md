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
na12878.sam  
na12878.srt.bam 


<div class="text-purple">This text is purple, <a href="#" class="text-inherit">including the link</a></div>

<div class="text-blue mb-2">
  .text-blue on white
</div>
