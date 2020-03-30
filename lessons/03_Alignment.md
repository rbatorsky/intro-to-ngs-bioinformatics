Approximate time: 20 minutes

## Goals
- Align short reads to a references genome with BWA
- View alignment using IGV

<img src="../img/workflow_align.png" width="200">

# BWA Alignment

Burrows-Wheeler Aligner ([BWA](http://bio-bwa.sourceforge.net/)) is a software package for mapping low-divergent 
sequences against a large reference genome, such as the human genome. 

The Naive approach to read alignment is to compare a read to every position in the reference genome.
This is too slow!

BWA solves this problem by creating an "index" of our reference sequence for faster lookup.

The following figure shows a short read with a red segment followed by a blue segment that 
we seek to align to a genome containing many blue and red segments.
The table keeps track of all the locations where a given pattern (seed sequence) occurs in the reference genome.
When BWA encounters a new read, it looks up a seed sequence at the beginning of the read. 
This speeds up the search for potential alignment positions for a given read.

<img src="../img/bwa.png" width="300">

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
chr10.fa.amb  <-- Location of ambiguous (non-ATGC) nucleotides
chr10.fa.ann  <-- Sequence names, lengths
chr10.fa.bwt  <-- BWT suffix array
chr10.fa.pac  <-- Binary encoded sequence
chr10.fa.sa   <-- Suffix array index
```

## BWA alignment

Let's check the usage instructions for BWA mem by typing `bwa mem`

```markdown
Usage: bwa mem [options] <idxbase> <in1.fq> [in2.fq]

Algorithm options:

       -t INT        number of threads [1]
       -k INT        minimum seed length [19]
       -w INT        band width for banded alignment [100]
       -d INT        off-diagonal X-dropoff [100]
       -r FLOAT      look for internal seeds inside a seed longer than {-k} * FLOAT [1.5]
       -y INT        seed occurrence for the 3rd round seeding [20]
       -c INT        skip seeds with more than INT occurrences [500]
       -D FLOAT      drop chains shorter than FLOAT fraction of the longest overlapping chain [0.50]
       -W INT        discard a chain if seeded bases shorter than INT [0]
       -m INT        perform at most INT rounds of mate rescues for each read [50]
       -S            skip mate rescue
       -P            skip pairing; mate rescue performed unless -S also in use
...
```

Since our alignment command will have multiple arguments, it will be convenient to write a script.

Go up one level to our main `intro-to-ngs` directory:
```markdown
cd ..
```

Make a new directory for our results
```markdown
mkdir results
```

Open a text editor with the program `nano` and create a new file called `bwa.sh`.
```markdown
nano bwa.sh
```

Enter the following text.
Note that each line ends in a single backslash, which will be read as a line continuation.
Be careful to put a space *before* the backslash and *not after*.

```markdown
bwa mem \
-t 2 \
-M \
-R "@RG\tID:reads\tSM:na12878" \
-o results/na12878.sam \
ref_data/chr10.fa \
raw_data/na12878_1.fq \
raw_data/na12878_2.fq
```

Let's look line by line at the options we've given to BWA:
1. `-t 2` : BWA runs two parallel threads. Alignment is a task that is easy to parallelize 
because alignment of a read is independent of other reads.

2. `-M` : "mark shorter split hits as secondary". This option is needed for GATK compatibility, 
which is a tool we will use for variant calling. [see this explanation on biostars](https://www.biostars.org/p/97323/
)

3. `-R "@RG\tID:reads\tSM:na12878" `: Add a read group tag (RG) and a sample name (SM) to our alignment file header. 
We'll see where this appears in our output. In addition to being required for GATK, it's advisable to always add these labels to make the origin of the reads clear.

4. `-o results/na12878.sam` :  Place the output in the results folder and give it a name

5. The following arguments are our reference, read1 and read2 files, in the order required by BWA:
``` 
ref_data/chr10.fa \
raw_data/na12878_1.fq \
raw_data/na12878_2.fq
```

Exit nano by typing `^X` and follow prompts to save and name the file `bwa.sh`.

Now we can run our script.
```markdown
sh bwa.sh
```

Result:
```markdown
[M::bwa_idx_load_from_disk] read 0 ALT contigs
[M::process] read 9304 sequences (707104 bp)...
[M::mem_pestat] # candidate unique pairs for (FF, FR, RF, RR): (0, 2256, 0, 0)
[M::mem_pestat] skip orientation FF as there are not enough pairs
[M::mem_pestat] analyzing insert size distribution for orientation FR...
[M::mem_pestat] (25, 50, 75) percentile: (120, 160, 216)
[M::mem_pestat] low and high boundaries for computing mean and std.dev: (1, 408)
[M::mem_pestat] mean and std.dev: (172.35, 67.15)
[M::mem_pestat] low and high boundaries for proper pairs: (1, 504)
[M::mem_pestat] skip orientation RF as there are not enough pairs
[M::mem_pestat] skip orientation RR as there are not enough pairs
[M::mem_process_seqs] Processed 9304 reads in 1.034 CPU sec, 0.518 real sec
```

List the files in the results directory by typing `ls results`.
Result:
```markdown
na12878.sam
```

## Sequence Alignment Map (SAM)

Take a look at the output file:
```markdown
cd results
head na12878.sam
```
The file has two sections

Header:
```markdown
@SQ     SN:chr10        LN:133797422        <-- Reference sequence name (SN) and length (LN)
@RG     ID:reads        SM:na12878          <-- Read group (ID) and sample (SM) information that we input to BWA
@PG ID:bwa PN:bwa VN:0.7.17… CL:bwa mem     <-- Programs and arguments used in processing
```

Alignment:
<img src="../img/workflow_align.png" width="400">

More information on SAM format: [https://samtools.github.io/hts-specs/SAMv1.pdf](https://samtools.github.io/hts-specs/SAMv1.pdf)



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
