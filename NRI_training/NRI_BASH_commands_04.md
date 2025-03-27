# Submitting jobs to the cluster using the SLURM job management system


We have previously created an interactive job on the cluster via srun. This allowed us to run a bioinformatics program, but is generally considered bad practice as it is not making use of the queue system and therefore not being a good cluster citizen. Where possible, we should package our commands into scripts and submit these into the queue to be allocated to the cluster. We do this using the tool sbatch.


Please log into the cluster and navigate to your home directory.

## Making a project directory for our work

As mentioned previously, we shouldn't be working and storing data in our home areas of the cluster.

Let's make a specific project directory for your training and use this as an opportunity to put our understanding of variables into practice...

Each of us will need to make a unique directory for our work. We will use our username as a unique bit of information to differentiate these directories.

When we log into the cluster a  variable call $USER is automatically set with your full user information:

```bash
  echo $USER
```

we also heard yesterday, how there was a common area that NRI staff could store research projects in:


```bash
  ls -d /projects/group-nri-ph/nri_projects # the -d option confirms if this directory exists
  ls /projects/group-nri-ph/nri_projects # this will show you the directory contents
```

We can make a directory for our project here...



Let's use this information, to make a directory in this area

```bash
  echo $USER
  ProjectDirectory=/projects/group-nri-ph/nri_projects/training_${USER}
  mkdir $ProjectDirectory
```
Note that we are using the "absolute path" here (from the base of the file system) rather than the "relative path" (from where we currently are). This helps ensure that the directory is created in the location of the cluster where we want it.


By setting $ProjectDirectory as a variable, we now have a quick way to navigate to this area if we need it in future.
Note - any set variables are lost if you log out of the cluster or log into a worker node.

```bash
  cd $ProjectDirectory
  pwd
  ls
```

If you come to do analyses on HPC2, you should set up a project directory in this area.


## Organise data

Trimmed DNA sequence data has been loaded into a folder qc_dna

```bash
  ls /projects/group-nri-ph/NRI_training/data
  ls -d /projects/group-nri-ph/NRI_training/data/qc_dna
```


If you run the following commands, you will see that this contains a structure of directories where our data has been well-organised.

Please use similar structures of directories in your own work. This helps yourself and future users navigate your data.

```bash  
  ls /projects/group-nri-ph/NRI_training/data/qc_dna/E.coli/SRR5368359/
  ls /projects/group-nri-ph/NRI_training/data/qc_dna/E.coli/SRR5368359/F
  ls /projects/group-nri-ph/NRI_training/data/qc_dna/E.coli/SRR5368359/R  
```


Let's copy this entire director structure to our $ProjectDirectory

```bash
  DataDirectory=$(ls -d /projects/group-nri-ph/NRI_training/data/qc_dna)
  cp -r $DataDirectory $ProjectDirectory/.
```

What does the -r flag mean here for the cp command?

copy recursively - this directory and all data within it.

What does the "/." mean at the end of the cp command?

When referring to file structures in BASH "." typically means "here". In contrast, ".." means "up one directory"


Let's check that the data have been moved across correctly. Note that we can use * as a wildcard character, that will match any text available.

```bash
  ls
  ls qc_dna/*/*/*
```

Pat yourself on the back, you are now working as a good HPC2 citizen with a well-organised project directory.


## Check status of the queue

As we learnt before, we can get information on the cluster queue using the squeue command

```bash
  squeue
```

## Make a submission script containing our commands

We want to perform genome assembly using the data that we copied across.

Yesterday, we used the following srun command to log into the cluster, and the following genome assembly command:

```bash
  module load SPAdes
  mkdir ~/workshop1/assembly
  cd ~/workshop1/assembly
  srun --partition=cpu-short --nodes=1 --ntasks=1 --cpus-per-task=20 --mem=50G --pty bash
  spades.py --isolate --memory 50 --threads 20 -1 ../trimming/f_read_trimmed_paired.fq.gz -2 ../trimming/r_read_trimmed_paired.fq.gz -o spades_assembly
```


Lets make a BASH submission script that will contain the information that we need to submit a job to the cluster:

```bash
  nano spades_slurm.sh
```

Within this file enter:
Remember it is ctrl+x to exit nano. You will want to save the changes that you made to the file.
```bash
#!/bin/bash
#SBATCH --partition=cpu-standard
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=20
#SBATCH --mem=50G

echo “Performing genome assembly”
# module load SPAdes

F_Reads=$1
R_Reads=$2
OutputDirectory=$3

echo "Using variables:"
echo “$F_Reads”
echo “$R_Reads”
echo “$OutputDirectory”
mkdir -p $OutputDirectory

spades.py --memory 50 --threads 20 --isolate -1 $F_Reads -2 $R_Reads -o $OutputDirectory

echo "Assembly completed"

```

Note the use of the extended shebang, with instructions on how to run this job on the cluster. Note that the resources requested in the shebang (CPUs = 20, mem=50G) match or slightly exceed the resources requested by the program (--memory 50 --threads 20)

What are variables $1, $2 and $3 doing here?

They allow us to pass-on three pieces of information from the command line into the script.
In this case this information will be stored as location of two input files and the name for an output directory that will be created by the script.

## Submit our job onto the cluster

Now that we have our commands packaged into a submission script, we can submit our job to the cluster.

```bash
  sbatch spades_slurm.sh qc_dna/E.coli/SRR5368359/F/SRR5368359_F_trimmed_paired.fq.gz qc_dna/E.coli/SRR5368359/R/SRR5368359_R_trimmed_paired.fq.gz assembly
  /spades/E.coli/SRR5368359
```

This could also have been written as the following, which would make use of variables to help make the code more readable.

```bash
  F_Reads=$(ls qc_dna/E.coli/SRR5368359/F/*_F_trimmed_paired.fq.gz)
  R_Reads=$(ls qc_dna/E.coli/SRR5368359/R/*_R_trimmed_paired.fq.gz)
  OutputDirectory=assembly/spades/E.coli/SRR5368359
  sbatch spades_slurm.sh $F_Reads $R_Reads $OutputDirectory
```


Once submitted, we can check the status of our job on the cluster using squeue

```bash
  squeue
```

You may see your job listed (look for your username), in a queued state (PD) or running state (R). You can see which node that it has been assigned to and the JOBID associated with the job.

Once the job is running (in R state) a logfile will be created. This is created in the director from where the job was orginaly submitted from. For this reason, it is important to submit your jobs from a consistent place on the cluster, to ensure your logfiles all end up in one place. Therefore, always submit your jobs from your $ProjectDirectory (not a subfolder).

Your logfile will be called slurm-<$JOBID>.out.

Lets list all of our logfiles in the current directory, again making use of the wildcard *.

```bash
  ls slurm-*.out
```

Try opening one of these files to view job-progress using the less command.



Congratulations, you can now write SLURM submission scripts to run jobs on the cluster!

Note - We would not normally store submission scripts in your $ProjectFolder area. I recommend storing all submission scripts in a subfolder of your $HOME area. We will talk tomorrow about setting up GitHub as a space to document commands and store SLURM submission scripts.
