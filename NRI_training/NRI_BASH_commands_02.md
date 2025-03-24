

# Running a bioinformatics job on the cluster

# Step 1. Establishing an interactive session using srun

We can get information on the structure of the computer cluster using the following command:

```bash
  sinfo
```

And get information on the current use of the cluster's queue system using

```bash
  squeue
```

Typically we use the command *sbatch* to submit jobs onto the cluster.
However, this required us to understand how to package our commands into scripts that can be submitted to the cluster. We will learn this tomorrow.

In the meantime, lets open an interactive job on the cluster. This will only be possible if there is space in the queue for us.

We do this using the *srun* command. Note that we request to open a connection in the language of BASH. The rest of the commands are giving information on how the job should be allocated within the queue system. This includes assigning our interacitve job to the cpu-short queue which has a maximum timelimit of 2 hours.

```bash
  srun --partition=cpu-short --nodes=1 --ntasks=1 --cpus-per-task=10 --mem=40G --pty bash
```
If this has worked then you should have seen a change from your bash terminal saying \<username\>@gm-hpc2-login01 to \<username\>@gm-hpc2-comp001 or a similar code, depending upon which worker node that you have been allocated to.

You should also now see your job listed within the queue system when you type:

```bash
  squeue
```

We can logout of the worker node by typing:

```bash
  exit
```

Note - srun jobs should be run as the exception on the cluster. We will learn how to run jobs wihtin the queue system tomorrow.

Make sure that you are logged into a worker node before continueing to the next step.

# Step 2. Running bioinformatics programs from a worker node - Read trimming

<!--
For this section, it will be best to avoid working in our home directories. This area should only be used to install programs and document our code.

Lets move to a dedicated area for project data:

```bash
  cd /projects/group-nri-ph/NRI_training
  mkdir $USER
  cd $USER
```
 -->

The cluster comes with basic BASH programs installed (mv, cp, less, nano etc.).

We can use which to show where these programs are actually installed:

```bash
  which mv
  which less
```

Many bioinformatics are also installed on the server under the module system (Trimmomatic, SPAdes).

We can load these program

```bash
  module load Trimmomatic
  module load SPAdes
```

We can discuss the module systems & installation of programs on Friday.

## 2.1 Identify illumina sequence data

I have downloaded an illumina sequencing run from NCBI (fungal species)

I have then subsetted 1million reads for us to work with.

```bash
  ls /projects/group-nri-ph/NRI_training/data/illumina/SRR5368359_1M_reads-F.fastq.gz
  ls /projects/group-nri-ph/NRI_training/data/illumina/SRR5368359_1M_reads-R.fastq.gz
```

Lets prepare a directory to move these files into

```bash
  cd ~/workshop1
  mkdir trimming
  cd trimming
  cp /projects/group-nri-ph/NRI_training/data/illumina/SRR5368359_1M_reads-F.fastq.gz f_read.fq.gz
  cp /projects/group-nri-ph/NRI_training/data/illumina/SRR5368359_1M_reads-R.fastq.gz r_read.fq.gz
  ls
```


## Step 2.2 Trimming adapters and low quality data from illumina sequencing reads

We want to run the program Trimmomatic. The first step of running a bioinformatics
program is to read the manual. These aren't BASH programs, so we may have to do
some googling to find it.

Here it is:
http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf

Trimmomatic isn't an easy program to run.
For new users, 90% of bioinformatics is bashing your head against a wall while
wandering why a program doesn't run. This is perfectly normal.

I suggest you copy the lines below, rather than type these commands yourself.

Trimmomatic is a program written in the language Java, this means we need to run
it slightly differently to other programs.

We specify that we are using Java, then give it the location of the installed program.

```bash
  cd ~/workshop1/trimming

  java -jar $EBROOTTRIMMOMATIC/trimmomatic-0.39.jar
```

There are a lot of commands to enter here on a single line. We can split this
onto multiple lines using the \ character.

```bash
  java -jar $EBROOTTRIMMOMATIC/trimmomatic-0.39.jar \
    PE -phred33 -trimlog log.txt \
    f_read.fq.gz \
    r_read.fq.gz \
    f_read_trimmed_paired.fq.gz f_read_trimmed_unpaired.fq.gz \
    r_read_trimmed_paired.fq.gz r_read_trimmed_unpaired.fq.gz \
    ILLUMINACLIP:/projects/group-nri-ph/NRI_training/data/illumina/illumina_PE.fasta:2:30:10 \
    LEADING:5 TRAILING:5 SLIDINGWINDOW:4:15 MINLEN:25
```

it would otherwise be displayed as:

```bash
  java -jar $EBROOTTRIMMOMATIC/trimmomatic-0.39.jar PE -phred33 -trimlog log.txt f_read.fq.gz r_read.fq.gz f_read_trimmed_paired.fq.gz f_read_trimmed_unpaired.fq.gz r_read_trimmed_paired.fq.gz r_read_trimmed_unpaired.fq.gz ILLUMINACLIP:/projects/group-nri-ph/NRI_training/data/illumina/illumina_PE.fasta:2:30:10 LEADING:5 TRAILING:5 SLIDINGWINDOW:4:15 MINLEN:25
```

This command took ~5 minutes to run on aa0377h@gm-hpc2-comp001.


## 2.3 Viewing trimming outputs

We got some output from running trimmomatic. What does it tell us?

These are the results from using the total set of reads (rather than the 1M read dataset).
How does yours compare?

```
  Input Read Pairs: 2116469 Both Surviving: 2060415 (97.35%) Forward Only Surviving: 53728 (2
  .54%) Reverse Only Surviving: 1745 (0.08%) Dropped: 581 (0.03%)
```

Output files have been produced by Trimmomatic
<details>
<summary>...What files are present?</summary>
<br>

```bash
  ls
```

</details>
How has trimming affected file sizes?
<details>
<summary>...What files are present?</summary>
<br>

```bash
  ls -lh
```

</details>

Once done, we need to log out of our interactive session on the worker node

```bash
  exit
```



## 3. Performing genome assembly using trimmed sequences

We will need to open a new connection to the cluster, this time requesting more CPUs and RAM.

```bash
  srun --partition=cpu-short --nodes=1 --ntasks=1 --cpus-per-task=20 --mem=50G --pty bash
```

We will also need to load SPAdes module, if we haven't done so already:

```bash
  module load SPAdes
```

## 3.4 Making a directory for the assembly

Lets make a directory to perform the assembly in:

```bash
  cd ~/workshop1
  mkdir assembly
  cd assembly
```

Where are the trimmed illumina reads in comparison to our current location?

```bash
  ls ../trimming
```

## 3.5 Running the assembly:

<details>
<summary>What is the first step of running a bioinformatics program?</summary>
<br>

Here is the manual:
https://ablab.github.io/spades/running.html

</details>

This program isn't written in Java, meaning it is simpler to run

We can view spades and its options by typing:

```bash
  spades.py --help
```

After reading the manual we may come to a conclusion on the commands we want to run.

Lets set the assembly running:

```bash
  spades.py --isolate --memory 50 --threads 20 -1 ../trimming/f_read_trimmed_paired.fq.gz -2 ../trimming/r_read_trimmed_paired.fq.gz -o spades_assembly
```

Assembly of this data took <5 minutes to run on aa0377h@gm-hpc2-comp001.

This has produced a number of output files. We can discuss assembly output files
in the small group discussions.

Notable files here are the spades log file, the contigs.fasta and the
scaffolds.fasta files.

The scaffolds.fasta file is the "final result" from spades

```bash
  ls spades_assembly
  less spades_assembly/scaffolds.fasta
```

How many fragments / contigs did we assembly the genome back into?
If we assume one contig for each fasta entry,
then we can count the number of lines containing the fasta heading ">"

```bash
  cat spades_assembly/scaffolds.fasta | grep '>' | wc -l
```



## Step 4 Assessing assembly quality

Quast is a tool to collect assembly statistics from a genome in a fasta file

## 4.1 Running QUAST

```bash
  module load QUAST
```

Here is the Quast manual:
http://quast.sourceforge.net/docs/manual.html

```bash
  quast.py spades_assembly/scaffolds.fasta -o quast_output
```

Note - Quast has a lot of additional features, we are just running the most basic
for this analysis.

```bash
  ls quast_output
  less quast_output/report.txt
```
