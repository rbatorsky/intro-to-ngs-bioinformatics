Approximate time: 20 minutes

## Goals

- Format of FastQ files
- Run FastQC to asses quality of reads

## Take a look at our raw data

### Fastq format
From our main directory `into-to-ngs` change into the raw data directory:
```markdown
cd raw_data
```

Use the command `head` to look at the first few lines of our first fastq file.

```markdown
head na12878_1.fq
```

Result (arrows on the right show explanation of each line):

```markdown
@SRR098401.109756285/1                   <-- Sequence identifier: @Read ID / 1 or 2 of pair
GACTCACGTAACTTTAAACTCTAACAGAAATATACTA…   <-- Sequence
+                                        <-- + (optionally lists the sequence identifier again)
CAEFGDG?BCGGGEEDGGHGHGDFHEIEGGDDDD…      <-- Quality String
```

### Base Quality Scores

The symbols we see in the read quality string are an encoding of the quality score.

This figure shows a mapping of encoded quality score to quality score.

<figure>
<figcaption> Test </figcaption>
<img src="../img/base_qual.png" width="400">
</figure>

A quality score is a prediction of the probability of an error in base calling.
This table 

<img src="../img/base_qual_table.png" width="600" >

Looking back at our reads, we can see that the first base has an encoded of C.
Using the, we see that C encodes a quality of 34.
For e probability < 1/1000 of being an error.

More information from [Illumina](https://www.illumina.com/science/education/sequencing-quality-scores.html)
 

