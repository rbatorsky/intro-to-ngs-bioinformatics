Approximate time: 20 minutes

## Goals
- Align short reads to a references genome with BWA
- View alignment using IGV

<img src="../img/workflow_align.png" width="400">

# BWA Alignment

Burrows-Wheeler Aligner ([BWA](http://bio-bwa.sourceforge.net/)) is a software package for mapping low-divergent sequences against a large reference genome, such as the human genome. 

The Naive approach to read alignment is to compare a read to every position in the reference genome.
This is too slow!

BWA solves this problem by creating an "index" of our reference sequence for faster lookup.

The following figure shows a short read with a red segment followed by a blue segment that we seek to align to a genome containing many blue and red segments.
The table keeps track of all the locations where a given pattern (seed sequence) occurs in the reference genome.
When BWA encounters a new read, it looks up a seed sequence at the beginning of the read. 
This speeds up the search for potential alignment positions for a given read.

<img src="../img/workflow_align.png" width="400">

It has three algorithms:

- BWA-backtrack: designed for Illumina sequence reads up to 100bp (3-step)
- BWA-SW:  designed for longer sequences ranging from 70bp to 1Mbp, long-read support and split alignment
- BWA-MEM: optimized for 70-100bp Illumina reads

We'll use BWA-MEM.
Underlying the BWA index is the [Burrows-Wheeler Transform](http://web.stanford.edu/class/cs262/presentations/lecture4.pdf)

# BWA Index
We'll create this index 

1. Change to our reference data directory
`cd intro-to-ngs/ref_data`

2. Preview our genome using the command `head` by typing:

`head chr10.fa` 

You'll see the first 10 lines of the file `chr10.fa`:
```buildoutcfg
>chr10 AC:CM000672.2 gi:568336…   <-- >name
NNNNNNNNNNNNNNNNNNNNN             <-- sequence
…
```
This is an example of fasta format

3. Load the BWA module, which will give us access to the `bwa` program:
```
module load bwa/0.7.17
```

Test it out without any arguments in order to view the help message.
```markdown
bwa
```

Result:
```markdown
Program: bwa (alignment via Burrows-Wheeler transformation)
Version: 0.7.17-r1198-dirty
Contact: Heng Li <lh3@sanger.ac.uk>

Usage:   bwa <command> [options]

Command: index         index sequences in the FASTA format
…
```

Use the `index` command to see usage instructions for genome indexing

```markdown
bwa index
```

Result
```markdown
Usage:   bwa index [options] <in.fasta>
Options: -a STR    BWT construction algorithm …

```

Run the command:
```markdown
bwa index chr10.fa
```

Result:
```markdown
[bwa_index] Pack FASTA... 1.01 sec
[bwa_index] Construct BWT for the packed sequence...
[BWTIncCreate] textLength=267594844, availableWord=30828588
…
```

When it's done, take a look at the files produced by typing `ls`:
```markdown
chr10.fa      <-- Original sequence
chr10.fa.amb  <-- Location of non-ATGC nucleotides
chr10.fa.ann  <-- Sequence names, lengths
chr10.fa.bwt  <-- BWT suffix array
chr10.fa.pac  <-- Binary encoded sequence
chr10.fa.sa   <-- Suffix array index
```



```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/rbatorsky/galaxy-tutorials/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
