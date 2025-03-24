
# BASH workshop commands




# Step 1. Where am I, how do I get help on a command?


## 1.1 Where am I?

print working directory

```bash
  pwd
```


## 1.2 What is in my current directory?

```bash
  ls
```


## 1.3 Can I get more information than this?

Flags give increased functionality to a command

File size? Date of creation?

```bash
  ls -l -h
```

Will also show the same result if we do:

```bash
  ls -lh
```


## 1.4 How do we know what options are available for a command?

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

You will become familiar with commands are available as we progress.

Core commands:

```bash
  pwd
  ls
  touch <file_name>
  rm <file>
  mkdir <directory_name>
  cd <directory_location>
  cp <original_file> <new_file>
  mv <original_file> <new_file>
  less <file>
  nano <file>
```


# Step 3. How do I create / delete a file?


## 3.1 Make an empty file:

```bash
  touch file1.txt
```

We will look at file contents in a minute. For the moment, we can check that
it is empty using the ls command to see file size.

```bash
  ls file1.txt
  ls -lh file1.txt
```


## 3.2 Delete a file

```bash
  rm file1.txt
```


## 3.3 Create a directory (folder)

Make directory command:

```bash
  mkdir workshop1
```


## 3.4 Delete a directory

The rm command can be reversed (files aren't moved to a trash folder)

This means it can be dangerous to delete whole directories (including everything in them).

The rm command wont let us delete a folder

```bash
  rm workshop1
```

It will let us delete a folder if we use the proper flag.

Can you identify which flag to use from the manual:

```bash
  man rm
```

```bash
  rm -r workshop1
  ls
```




# Step 4. How do I move around?


## 4.1 Create a directory (folder)

Make directory command:

```bash
  mkdir workshop1
```

## 4.2 Change directory

The change directory command

```bash
  cd workshop1
```

What command would we use to:
<details>
<summary>  ...view our new location?</summary>
<br>

```bash
  pwd
```

</details>
<details>
<summary>  ...view the content of our new directory?</summary>
<br>

```bash
  ls
```

</details>

## 4.3 Going back/up a directory
we can also go up a directory

. can be used to represent the current directory and .. can be used to
represent the above directory.

```bash
  cd ..
```

## 4.4 Move to a specific location in the directory structure

We have been moving relative to our current location. We can also navigate to a
specific location on the computer by using the absolute filepath.

```bash
  cd /projects/group-nri-ph/NRI_training
```

Or go back to our home area of the cluster

```bash
  cd ~
```

Try practicing some of these commands.
Make a directory, move in and out of it, and back to your home directory.
Note - it important not to use spaces in file or directory names, this may lead
to producing multiple directories!




# Step 5. How to copy and move files?

Lets move to the workshop1 directory that we created for this part of the training:

```bash
  cd ~/workshop1
```

## 5.1 Identify a file from elsewhere on the computer:

Lets first identify the file we are interested in copying

We can do this with the ls command:

```bash
  ls /projects/group-nri-ph/NRI_training/data/sanger/endoPG_alignment_trimmed.fa
```

We could have also done this using a relative path:
Note - using tab completion as you build a path can make this easier.
```bash
  ls ../../../projects/group-nri-ph/NRI_training/data/sanger/endoPG_alignment_trimmed.fa
```

## 5.2 Copying a file:

The copy command can be used:

Note - as descibed above, "." means this location. We could alternatively, give
a new filename and the file would be renamed when copied.

```bash
  cp ../data/sanger/endoPG_alignment_trimmed.fa .
```

## 5.3 Moving a file:

The mv command can be used to move or rename a file.

Lets make a new directory and move our fasta file into there

```bash
mkdir fasta
mv endoPG_alignment_trimmed.fa fasta/.
ls
cd fasta
ls
```

*With this, we now have to tools to navigate the cluster, move copy and delete files.*




# Step 6. Viewing files and extracting information

We can view and edit files using some specialised programs

## 6.1 View a file using less

Less is a simple program to view file contents. It is the same as the viewing
program with used with the manual command.

```bash
  less endoPG_alignment_trimmed.fa
```

We can quit this file using q. f and b can be used to quickly navigate forward/backwards a page.

less uses automatic line wrapping to show more information on the screen.
<details>
<summary>Can you use the manual page for less to find a flag to turn this off?</summary>
<br>

```bash
  man less
  less -S endoPG_alignment_trimmed.fa
```

</details>




## 6.2 Edit a file using nano

```bash
  nano endoPG_alignment_trimmed.fa
```

This opens a new screen similar to less. We can edit the file here.
If we want to exit we follow the instructions at the bottom of the screen.
Ctrl+x (to exit)
Y (yes we want to save)
Enter (keep the current filename)


*I would consider these the core skills of BASH.*

There are a lot of commands to view and manipulate files.
These give BASH a lot of its "power" as a computing language.

But BASH is the computing environment for the server. Lets keep bioinformatics
focussed for now and show how we can run a bioinformatics program on the cluster.
