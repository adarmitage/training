
# BASH workshop commands


# Step 1. Where am I, how to I get help on a command?

## Where am I?

print working directory

```bash
  pwd
```

## What is in my current directory?

```bash
  ls
```

## Can I get more information than this?

Flags give increased functionality to a command

File size? Date of creation?

```bash
  ls -l -h
```

Will also show the same result if we do:

```bash
  ls -lh
```

## How do we know what options are available for a command?

Use the Manual command

```bash
  man ls
```
You can navigate these manual pages using the up and down arrows.

F moves forward a page

"q" allows us to quit the manual and returns the screen.


# Step 2. What are some common commands?

See cheat sheet:
https://courses.cs.washington.edu/courses/cse391/17sp/bash.html

You will become familiar with what can be done as we progress.


# Step 3. How do I create / delete a file?

## Make an empty file:

```bash
  touch file1.txt
```

We will look at file contents in a minute. For the moment, we can check that
it is empty using the ls command to see file size.

```bash
  ls file1.txt
  ls -lh file1.txt
```

## Delete a file

```bash
  rm file1.txt
```

## Create a directory (folder)

Make directory command:

```bash
  mkdir test
```

## Delete a directory

The rm command can be reversed (files aren't moved to a trash folder)

This means it can be dangerous to delete whole directories (including everything in them).

The rm command wont let us delete a folder

```bash
  rm test
```

It will let us delete a folder if we use the proper flag.

Can you identify which flag to use from the manual:

```bash
  man rm
```

```bash
  rm -r test
  ls
```


# Step 4. How do I move around?


## Create a directory (folder)

Make directory command:

```bash
  mkdir test
```

## Change directory

The change directory command

```bash
  cd test
```

What command would we use to:
  view our new location?
  view the content of our new directory?

```bash
  pwd
  ls
```

## Going back/up a directory
we can also go up a directory

. can be used to represent the current directory and .. can be used to
represent the above directory.

```bash
  cd ..
```

## Move a specific location in the directory structure

We have been moving relative to our current location. We can also navigate to a
specific location on the computer by using the absolute filepath.

```bash
  cd /home/ec2-user
```

Try practicing some of these commands.
Make a directory, move in and out of it, and back to your home directory.
Note - it important not to use spaces in file or directory names, this may lead
to producing multiple directories!


# Step 5. How to copy and move files?

Lets move to the test director that we created for this part of the workshop:

```bash
  cd /home/ec2-user/test
```

## Identify a file from elsewhere on the computer:

Lets first identify the file we are interested in copying

We can do this with the ls command:

```bash
  ls /home/ec2-user/data/sanger/endoPG_alignment_trimmed.fa
```

We could have also done this using a relative path:
Note - using tab completion as you build a path can make this easier.
```bash
  ls ../data/sanger/endoPG_alignment_trimmed.fa
```

## Copying a file:

The copy command can be used:

Note - as descibed above, "." means this location. We could alternatively, give
a new filename and the file would be renamed when copied.

```bash
  cp ../data/sanger/endoPG_alignment_trimmed.fa .
```

## Moving a file:

The mv command can be used to move or rename a file.

Lets make a new directory and move our fasta file into there

```bash
mkdir fasta
mv endoPG_alignment_trimmed.fa fasta/.
ls
cd fasta
ls
```

With this, we now have to tools to navigate the cluster, move copy and delete files.


# Step 6. Viewing files and extracting information

We can view and edit files using some specialised programs

## View a file using less

Less is a simple program to view file contents. It is the same as the viewing
program with used with the manual command.

```bash
  less endoPG_alignment_trimmed.fa
```

We can quit this file using q. f and b can be used to quickly navigate forward/backwards a page.

less uses automatic line wrapping to show more information on the screen.
Can you use the manual page for less to find a flag to turn this off?

```bash
man less
less -S endoPG_alignment_trimmed.fa
```

## Edit a file using nano

```bash
nano endoPG_alignment_trimmed.fa
```

This opens a new screen similar to less. We can edit the file here.
If we want to exit we follow the instructions at the bottom of the screen.
Ctrl+x (to exit)
Y (yes we want to save)
Enter (keep the current filename)


I would consider these the core skills of BASH.

There are a lot of commands to view and manipulate files.
These give BASH a lot of its "power" as a computing language.

But BASH is the computing environment for the server. Lets keep bioinformatics
focussed for now and show how we can run a bioinformatics program on the cluster.


# Step 6. Running bioinformatics programs

The server comes with basic BASH programs installed (cd, mv, cp, less, nano etc.).

I have also installed some bioinformatic programs onto the server (Trimmomatic, spades).

We can discuss installation of programs on Friday.

## Identify illumina sequence data

```bash
ls /home/ec2-user/data/illumina/SRR5368359_1M_reads-F.fastq.gz
ls /home/ec2-user/data/illumina/SRR5368359_1M_reads-R.fastq.gz
```

```bash
cd /home/ec2-user/test
mkdir trimming
cd trimming
cp /home/ec2-user/data/illumina/SRR5368359_1M_reads-F.fastq.gz f_read.fq.gz
cp /home/ec2-user/data/illumina/SRR5368359_1M_reads-R.fastq.gz r_read.fq.gz
ls
```


## Trimming adapters and low quality data from illumina sequencing reads

Trimmomatic is a program written in the language Java, this means we need to run
it slightly differently to other programs.

We specify that we are using Java, then give it the location of the installed program.

```bash
java -jar /home/ec2-user/prog/trimmomatic/Trimmomatic-0.39/trimmomatic-0.39.jar
```

```bash
java -jar /home/ec2-user/prog/trimmomatic/Trimmomatic-0.39/trimmomatic-0.39.jar \
  PE -phred33 -trimlog log.txt \
  f_read.fq.gz \
  r_read.fq.gz \
  f_read_trimmed_paired.fq.gz f_read_trimmed_unpaired.fq.gz \
  r_read_trimmed_paired.fq.gz r_read_trimmed_unpaired.fq.gz \
  ILLUMINACLIP:/home/ec2-user/prog/trimmomatic/Trimmomatic-0.39/adapters/illumina_PE.fasta:2:30:10 \
  LEADING:5 TRAILING:5 SLIDINGWINDOW:4:15 MINLEN:25
```

## Viewing trimming outputs

```
Input Read Pairs: 2116469 Both Surviving: 2060415 (97.35%) Forward Only Surviving: 53728 (2
.54%) Reverse Only Surviving: 1745 (0.08%) Dropped: 581 (0.03%)
```


What files are present?
How has trimming affected file sizes?

```bash
ls
ls -lh
```

## Performing a genome assembly using trimmed sequences


http://cab.spbu.ru/files/release3.14.0/manual.html


Lets make a directory to perform the assembly in:

```bash
cd /home/ec2-user/test
mkdir assembly
cd assembly
```

Where are the trimmed illumina reads in comparison to our current location?
```bash
ls ../trimming
```

lets set the assembly running:

```bash
spades.py --isolate -m 8 --pe1-1 ../trimming/f_read_trimmed_paired.fq.gz --pe1-2 ../trimming/r_read_trimmed_paired.fq.gz -o spades-test
```

10:59 - 8Gb Lightsail
11:07 - finished 2gb not exceeded

```bash
ls spades-test
less spades-test/scaffolds.fasta

cat spades-test/contigs.fasta | grep '>' | wc -l
```


## Assess assembly quality using QUAST

```bash
quast.py spades-test/contigs.fasta spades-test/scaffolds.fasta -o quast
```


# Advanced BASH concepts

lets go back to our sanger sequence data example

```bash
cd /home/ec2-user/test/fasta
ls endoPG_alignment_trimmed.fa
```

## Expanded lists of simple BASH commands

```bash
echo "hello world"
wc endoPG_alignment_trimmed.fa
cat endoPG_alignment_trimmed.fa
head endoPG_alignment_trimmed.fa
tail endoPG_alignment_trimmed.fa
grep '>' endoPG_alignment_trimmed.fa
```

Tip - try looking at the manual for some of these

What flag would you give to wc to show the number of lines in a file?
What flag would you use to head to specify a number of lines to return?

```bash
wc -l endoPG_alignment_trimmed.fa
head -n5 endoPG_alignment_trimmed.fa
```

There are many other useful BASH commands:
https://courses.cs.washington.edu/courses/cse391/17sp/bash.html


## Pipes and redirects

BASH programs output information to stdout. This can then be used as input
information for the next program. Prgrams are joined together this way using a
pipe symbol |

When there is no program at stdout, information is printed to the screen.

This means, multiple simple commands can be joined together to perform more complex tasks

This what makes BASH such a powerful language

```bash
cat endoPG_alignment_trimmed.fa | grep '>' | head -n 5
cat endoPG_alignment_trimmed.fa | grep '>' | sort | head -n 5
```

How could you count the number of fasta accessions in a file?

```bash
cat endoPG_alignment_trimmed.fa | grep '>' | wc -l
```

We can also redirct our output from stdout and into a new file.

This is done using the redirect character >

```bash
cat endoPG_alignment_trimmed.fa | grep '>' > endoPG_headers.txt
cat endoPG_alignment_trimmed.fa | grep '>' | head -n 5 > endoPG_headers_top5.txt
```

## Variables

An important concept in copmuting is the ability to store information in a variable.

This is similar to storing a number in your calculators memory for later usage.

Storing variables means:
  * you type less, meaning fewer errors!
  * easier to read documentation
  * we can do things such as loops

We can set a variable by:

```bash
VARIABLE1="hello world"
```

We can call it again by using the variable name after a $ symbol.
The $ symbol tells BASH to *interpret this*

Note the difference between using "" and using '' symbols
```bash
echo "$VARIABLE"
echo '$VARIABLE'
```

We can also perform commands and use it to set variables.

These are performed in "subshells", which performs those commands and gives the
output to the variable. These subshells are written within brackets preceeded by
the $ symbol. Again, this means *interpret this*.

```bash
FILE1=$(ls /home/ec2-user/data/illumina/SRR5368359_1M_reads-F.fastq.gz)
echo $File1
```

How can this help us write commands for our programs? - e.g. trimming


```bash
INSTALL_DIRECTORY="/home/ec2-user/prog/trimmomatic/Trimmomatic-0.39"
FILE1=$(ls /home/ec2-user/data/illumina/SRR5368359_1M_reads-F.fastq.gz)
FILE2=$(ls /home/ec2-user/data/illumina/SRR5368359_1M_reads-R.fastq.gz)
ADAPTERS=$(ls /home/ec2-user/prog/trimmomatic/Trimmomatic-0.39/adapters/illumina_PE.fasta)

java -jar $INSTALL_DIRECTORY/trimmomatic-0.39.jar \
  PE -phred33 -trimlog log.txt \
  $FILE1 \
  $FILE2 \
  f_read_trimmed_paired.fq.gz f_read_trimmed_unpaired.fq.gz \
  r_read_trimmed_paired.fq.gz r_read_trimmed_unpaired.fq.gz \
  ILLUMINACLIP:$ADAPTERS:2:30:10 \
  LEADING:5 TRAILING:5 SLIDINGWINDOW:4:15 MINLEN:25
```

# Looping

We often find ourselves wanting to do the same thing many times.

This could:
  * renaming many files
  * performing an analysis each time on a different dataset.

```bash
cd /home/ec2-user/test
mkdir loops
cd loops
```

## for loop

```bash
for NUMBER in 1 2 3 4 5; do
  echo "$NUMBER"
done
```

How could we adapt this to make multiple directories?

```bash
  for ORGANISM in species1 species2; do
    echo "making directory for: $ORGANISM"
    mkdir $ORGANISM
  done
```

We can also use commands to get inputs for loops:

Here are many input files:
```bash
  ls /home/ec2-user/data/looping
```

we can use them as input for a loop:

```bash
for FILE in $(ls /home/ec2-user/data/looping); do
  echo $FILE
done
```

There are other conditionals such as IF and WHILE.

# Putting commands into a script

Commands can be put into a file that can be executed at a later point.

These are termed BASH scripts.

Typically we give them the file extension .sh

These files start with a special line telling the cluster to read the file
contents in the language of BASH. This is known as the shebang

An simple bash script may look something like this:

```bash
#!/bin/bash
echo "Hello world"
```
This could be saved to the file test.sh

This is a very simple example, but a pipeline of analyses may be contained in a
bash script e.g. trimming and genome assembly.

The file will be need to be made executable using the chmod command:

```bash
  ls -lh test.sh
  chmod +x test.sh
  ls -lh test.sh
```

The program can then be run:

```bash
  ./test.sh
```

BASH scripts can make use of special variables containing information collected
from the command line using variables $1, $2, etc. This allows you provide
information such as file IDs for a script to act upon.
