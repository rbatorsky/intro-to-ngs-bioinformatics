Approximate time: 20 minutes

## Learning Objectives

- Format of FastQ reads
- Run FastQC to asses quality of reads

## Take a look at our raw data

### Fastq format
Change into the raw data directory
```markdown
cd raw_data
```

Take a look at the first few lines of the first file, using the command `head`
```markdown
head na12878_1.fq
```

Result
```markdown
@SRR098401.109756285/1                   <-- Sequence identifier: @Read ID / 1 or 2 of pair
GACTCACGTAACTTTAAACTCTAACAGAAATATACTA…   <-- Sequence
+                                        <-- + (optionally lists the sequence identifier again)
CAEFGDG?BCGGGEEDGGHGHGDFHEIEGGDDDD…      <-- Quality String
```

### Base Quality Scores

The symbols we see in the read quality string are an encoding of the quality score:

<img src="../img/base_qual.png" width="400">

A quality score is a prediction of the probability of an error in base calling: 

<img src="../img/base_qual_table.png" width="400">

Looking back at our reads, we can see that the first base:

Encoded quality C -> Q = 34, or the probability < 1/1000 of being an error.

More information from [Illumina](https://www.illumina.com/science/education/sequencing-quality-scores.html)
 

