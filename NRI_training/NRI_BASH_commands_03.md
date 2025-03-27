# Advanced BASH concepts


Let's go back to our sanger sequence data example

```bash
  cd ~/workshop1/fasta
  ls endoPG_alignment_trimmed.fa
```


# Step 8 Expanded lists of simple BASH commands

```bash
  echo "hello world"
  wc endoPG_alignment_trimmed.fa
  cat endoPG_alignment_trimmed.fa
  head endoPG_alignment_trimmed.fa
  tail endoPG_alignment_trimmed.fa
  grep '>' endoPG_alignment_trimmed.fa
```

Tip - try looking at the manual for some of these

<details>
<summary>What flag would you give to wc to show the number of lines in a file?</summary>
<br>

```bash
  wc -l endoPG_alignment_trimmed.fa
```

</details>

<details>
<summary>What command and flag would you use to head to specify the first five lines?</summary>
<br>

```bash
  head -n5 endoPG_alignment_trimmed.fa
```

</details>


There are many other useful BASH commands:
https://courses.cs.washington.edu/courses/cse391/17sp/bash.html




# Step 9 Pipes and redirects

BASH programs output information to stdout. This can then be used as input
information for the next program. Prgrams are joined together this way using a
pipe symbol |

If there is no program at stdout, information is printed to the screen.

This means, multiple simple commands can be joined together to perform more complex tasks

This is what makes BASH such a powerful language.

```bash
  cat endoPG_alignment_trimmed.fa | grep '>' | head -n 5
  cat endoPG_alignment_trimmed.fa | grep '>' | sort | head -n 5
```



<details>
<summary>How could you count the number of fasta accessions in a file?</summary>
<br>

```bash
  cat endoPG_alignment_trimmed.fa | grep '>' | wc -l
```

</details>


We can also redirect our output from stdout and into a new file.

This is done using the redirect character >

```bash
cat endoPG_alignment_trimmed.fa | grep '>' > endoPG_headers.txt
cat endoPG_alignment_trimmed.fa | grep '>' | head -n 5 > endoPG_headers_top5.txt
```

We can use two >> characters to append to the end of a file rather than making a
new file.




# Step 10 Variables

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
  echo "$VARIABLE1"
  echo '$VARIABLE1'
```

We can also perform commands and use it to set variables.

These are performed in "subshells", which performs those commands and gives the
output to the variable. These subshells are written within brackets preceeded by
the $ symbol. Again, this means *interpret this*.

```bash
  FILE1=$(ls /projects/group-nri-ph/NRI_training/data/illumina/SRR5368359_1M_reads-F.fastq.gz)
  echo $FILE1
```

How can this help us write commands for our programs? - e.g. trimming

```bash
  INSTALL_DIRECTORY=$EBROOTTRIMMOMATIC
  FILE1=$(ls /projects/group-nri-ph/NRI_training/data/illumina/SRR5368359_1M_reads-F.fastq.gz)
  FILE2=$(ls /projects/group-nri-ph/NRI_training/data/illumina/SRR5368359_1M_reads-R.fastq.gz)
  ADAPTERS=$(ls /projects/group-nri-ph/NRI_training/data/illumina/illumina_PE.fasta)

  java -jar $INSTALL_DIRECTORY/trimmomatic-0.39.jar \
    PE -phred33 -trimlog log.txt \
    $FILE1 \
    $FILE2 \
    f_read_trimmed_paired.fq.gz f_read_trimmed_unpaired.fq.gz \
    r_read_trimmed_paired.fq.gz r_read_trimmed_unpaired.fq.gz \
    ILLUMINACLIP:$ADAPTERS:2:30:10 \
    LEADING:5 TRAILING:5 SLIDINGWINDOW:4:15 MINLEN:25
```




# Step 11 Looping and conditionals

We often find ourselves wanting to do the same thing many times.

This could:
  * renaming many files
  * performing an analysis each time on a different dataset.

```bash
  cd ~/workshop1
  mkdir loops
  cd loops
```

## The FOR loop

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
  ls /projects/group-nri-ph/NRI_training/data/looping
```

We can use them as input for a loop:

```bash
  for FILE in $(ls /projects/group-nri-ph/NRI_training/data/looping); do
    echo $FILE
  done
```

There are other conditional statements such as IF and WHILE.




# Step 12 Putting commands into a script

Commands can be put into a file that can be executed at a later point.

These are termed BASH scripts.

Typically we give them the file extension .sh

```bash
  nano bash_program1.sh
```

These files start with a special line telling the cluster to read the file
contents in the language of BASH. This is known as the shebang

An simple bash script may look something like this:

```bash
  #!/bin/bash
  echo "Hello world"
```

This can be saved to the file bash_program1.sh
exit - using ctrl+x
save - Y

This is a very simple example, but a pipeline of analyses may be contained in a
bash script e.g. trimming and genome assembly.

The file will be need to be made executable using the chmod command:

```bash
  ls -lh bash_program1.sh
  chmod +x bash_program1.sh
  ls -lh bash_program1.sh
```

Note the addition of the 'x' standing for executable to user, group and all permission columns.

The program can then be run:

```bash
  ./bash_program1.sh
```


# Step 13 Using variables in a script

BASH scripts can make use of special variables containing information collected
from the command line using variables $1, $2, etc. This allows you provide
information such as file IDs for a script to act upon.

```bash
  nano bash_program2.sh
```


```bash
  #!/bin/bash
  echo “Hello world”
  echo “Hello $1”
```

This can be saved to the file bash_program2.sh
exit - using ctrl+x
save - Y

This is a very simple example, but a pipeline of analyses may be contained in a
bash script e.g. trimming and genome assembly.

As before, the file will be need to be made executable using the chmod command:

```bash
  chmod +x bash_program2.sh
```

Note the addition of the 'x' standing for executable to user, group and all permission columns.

Because the program contains the variable $1, it will collect the first piece of information we give it from the command line. e.g. in this case, we can give our name.

```bash
  ./bash_program2.sh Andrew
```


<details>
<summary>What output do you see here?
<br>

```bash
  Hello world
  Hello Andrew
```

</details>

This is very useful for our scripting. We can use this to pass files into BASH scripts that can run our bioinformatic analyses.

This allows us to write a generic script on how to run a program or analysis pipeline and then vary the input data going into it!
