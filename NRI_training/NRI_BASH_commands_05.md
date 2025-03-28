# Good working practice on the cluster

We have covered core skills in how to navigate the cluster, load installed programs and submit jobs into the queue system.
From this, users will have a good basis to perform their own bioinformatic analyses.

However, there are some additional skills and good working practices that will help users along the way and make users good citizens on HPC2.


## Installing Programs

### Module for pre-instaleld prgrams

We know that many computer programs are pre-installed under the module system. These are programs installed into "environments", where all the required dependencies and any envirommental variables are loaded alongside the intended program. As discussed previously, we can use the following:

List the modules that I already have loaded:
```bash
  module list
```

show all of the modules installed on the system, including version numbers:

```bash
  module avail
```

Search for a module that may have been installed:

```bash
  module spider <fragment of program name>
```

These will help you find software to complete your analyses.

Remember when writing any thesis or paper, you will need to include the version number of each bioinformatic program that you have run. Each program may document this or show this differently, you may need to use your own initiative to find this information out.


You can load a module using:

```bash
  module load <environment name>
```

Exit/unload a module environment

```bash
  module unload <environment name>
```


### Manual install of programs

In some cases, we may need to install a program ourselves. It is recommended that you first consider asking for the program to be installed under the module system.

You will not be installing a prgram globally onto the cluster, but for you personally. As such, you should install programs into a personal space in your $HOME area.

We know that SPAdes is already installed under the module system. However, if I wanted to personally install this program I would first identify the program and install instructions online and then run through those install commands. In this case, I could find that SPAdes is documented here,
https://github.com/ablab/spades and install isntructions here https://ablab.github.io/spades/installation.html . In this case, the install instructions are quite straight forward with the program pre-compiled for our operating system. Install of many programs can be complex with many dependencies and requirements to compile the software for the operating system manually.

From the instructions in the manual above, I can install SPAdes myself.

However, I will create a directory to hold this install in my $HOME area, alongside other installed programs:

```bash
  mkdir -p ~/prog/spades
  cd ~/prog/spades
  wget https://github.com/ablab/spades/releases/download/v4.1.0/SPAdes-4.1.0-Linux.tar.gz
  tar -xzf SPAdes-4.1.0-Linux.tar.gz
  cd SPAdes-4.1.0-Linux/bin/
  ls
  pwd
```

The executable file for spades is spades.py. It is contained in the binary directory, as shown in the above commands. We could run it from our current location by typing

```bash
  ./spades.py
```

Or by using the absolute path of:

```bash
  ~/prog/spades/SPAdes-4.1.0-Linux/bin/spades.py
```

This command would work from any location on the cluster.

#### Manual install - Setting the path variable

So we have installed the program SPAdes on the cluster. We showed that we could call this program from anywhere on the cluster.

But this raises a quetstion - why did we have to type the location of the spades.py program here, but not for any of the other simple BASH programs that have been using, or those installed under the module system? Those cases, we would just type spades.py from anywhere on the cluster.

This is because of the $PATH variable. This is a variable loaded automatically on log-in to the cluster. It tells the operating system where to look for executable programs.

```bash
  echo $PATH
```

We can add the location of our SPAdes executable into the $PATH to tell the operating system to look here for the software.

Look how this command adds the location of our installed program to the beginning of existing PATH variable

```bash
  PATH=$HOME/prog/spades/SPAdes-4.1.0-Linux/bin:${PATH}

  echo $PATH
```

You should now be able to call spades from anywhere on the cluster:

```bash
  cd $HOME

  spades.py
```

This is great, but you dont want to modify the PATH variable manually every time you log into the cluster with the location of all of your installed programs.
We can modify the PATH variable on log-in to the cluster by editing hidden files in our $HOME area.

Hidden files start with a ".", which stops them from being seen when you run ls using default settings

```bash
  cd $HOME

  ls
  ls -all
```

You should see a hidden file called .bashrc This file is loaded on start-up and can be used to set local settings.

```bash
  ls .bashrc
  less .bashrc
```

Please open this file in your text editor and add the following lines to the file. These instructions, mean that when .bashrc is run upon log-in to the cluster, a hidden file called .profile will be loaded. We can put information to modify our $PATH variable into this hidden file.

```bash
  nano .bashrc
```

add these lines to the end of the file:
```bash
# Source environmental variables and PATH modifications:
if [ -f ~/.profile ]; then
        . ~/.profile
fi

```

Now that we have told .bashrc to read the file .profile on start-up, we should probably create the file:

```bash
  touch ~/.profile
```

Let's open it and put our instruction in to modify the PATH variable.

```bash
  nano ~/.profile
```

add the line:

```bash
PATH=$HOME/prog/spades/SPAdes-4.1.0-Linux/bin:${PATH}
```

save and exit this file.

Try loggin out of the cluster and back in again. Once you have logged in, try running the spades.py command and see if it works. We can also check to see where this program is installed:


```bash
  spades.py
  which spades.py
```

Congrats, this has allowed you to install programs manually in future. If you do install new programs manually, then make sure to add the location of their executable files into your PATH variable as a new line in your .profile. You may end up with lots of lines in this file, each modifying the path by adding the location of the file to the beginning of the variable

e.g. This is the contents of my .profile, this also includes setting some additional variables that allows other installed programs to run. Note many of these prgrams have now been installed under the module system, so i no longer need to use these installs.

```
  ## Setting program variables:
  export JAVA_HOME=$HOME/prog/java/jdk-22.0.1
  export GENEMARK_PATH=$HOME/prog/genemarkET/gmes_linux_64
  export FUNANNOTATE_DB=$HOME/prog/funannotate/funannotate_db

  ## Prepending program locations to $PATH
  PATH=$HOME/prog/java/jdk-22.0.1/bin:${PATH}
  PATH=$HOME/prog/bedtools/v2.30:${PATH}
  PATH=$HOME/prog/trimmomatic/Trimmomatic-0.39:${PATH}
  PATH=$HOME/prog/STAR/STAR-2.7.11a/bin/Linux_x86_64:${PATH}
  PATH=$HOME/prog/tophat/tophat-2.1.2/src:${PATH}
  PATH=$HOME/prog/blast/ncbi-blast-2.15.0+/bin:${PATH}
  PATH=$HOME/prog/genemarkET/gmes_linux_64:${PATH}
  PATH=$HOME/prog/signalp/signalp-5.0b/bin:${PATH}
  PATH=$HOME/prog/kraken2/KrakenTools-1.0.1:${PATH}
  PATH=$HOME/prog/kraken2/KrakenTools-1.0.1/DiversityTools:${PATH}
  PATH=$HOME/prog/phobius/tmp/tmpX2D4BN/phobius:${PATH}
  PATH=$HOME/prog/effectorP/EffectorP-3.0:$PATH
  PATH=$HOME/prog/canu/canu-2.3/bin:${PATH}
  PATH=$HOME/prog/mitohifi-dependencies/MitoHiFi/src:${PATH}
  PATH=$HOME/prog/dorado/dorado-0.9.1-linux-x64/bin:${PATH}
  PATH=$HOME/prog/spades/SPAdes-4.1.0-Linux/bin:${PATH}
```

Note - if you run module load, it will detect the module's version of SPAdes, before your personal install.

### Conda

SPAdes was relatively simple to install. However, many programs have complex sets of other prgrams that they depend upon and require variables to be set before they can be run. Install of these programs can be tedious. This is one of the reasons why we use the module system, as many of these install issues can be dealt with centrally and enbironments can be loaded containing all required dependencies.

Similar to module, Anaconda ("conda") allows install of environments that have been pre-built by the community. This includes required dependencies and environmental variables.

Anaconda3 is a program that manages these environments and has been installed on the cluster under the moodule system. We need to load that up on.

We only need to set-up Anaconda3 once, then it will start running every time we log into the cluster.

To do this, do the following:

```bash
  module load Anaconda3
  conda init
```

Now log out of the cluster and log back in again. You should now find that conda is running in the background, represented by the "base" you see next to your command prompt.

"base" means that you dont have any conda evironments currently open.


We don't want to ever install any conda programs into this base envionment as they may conflict with other conda-installed programs. Instead we create an environment for each prgram we want to install. We can load and environment, run our analysis and then unload it.

As an example, lets create an environment where we will install genome assembly programs and install SPAdes there using conda.

First run a google search to see if any conda environments exist for the program... https://anaconda.org/bioconda/spades this will help us get instructions on how to download the conda environment.

We will then want to create the conda environment, before using conda to download the envibment from the named online repository of conda environments.

```bash
  conda create -n assembly
```
Follow interactive prompts, where requested.


To activate this environment run:

```bash
  conda activate assembly
```

 You should now see that the name of your environment has changed from base to assembly.

 You can install spades into this environment by running:

 ```bash
   conda install bioconda::spades
 ```

 This will install the program and any required dependencies. You may need to follow interactive prompts, where requested.

 You can deactive the environment by typing:

 ```bash
  conda deactivate
 ```


You can now install prgrams via conda. To run these programs then:

```bash
  conda activate assembly
    spades.py # run the program or submit an sbatch script to run the program
  conda deactivate
```


## Github

Github is an important tool to document code. It allows people to repeat your work, prevents loss of code if the cluster goes down and helps you keep track of what you have done across your project.

Here are some instructions on how to structure your $HOME area with git repositories that may help you document your work and store slurm submission scripts.

Lets make an area where we can download git repositories to

```bash
  cd $HOME
  mkdir -p git_repos/nri_repos
```

We can download a git repository using git clone. Lets do this by downloading the git repository that we have been following over this training...
https://github.com/adarmitage/training . You can get the link to download the repository by clicking on the "<> Code" button -> "local" -> "HTTPS".

```bash
  cd $HOME/git_repos/nri_repos
  git clone https://github.com/adarmitage/training.git
```

Because this is a public repository, this should download without asking you for any information e.g. username or password.

```bash
  ls
  ls training/NRI_training
  less training/NRI_training/NRI_BASH_commands_05.md
```

### Setting up GitHub to access private repositories and make edits to files

Let's make some configuration adjustmanets for git on the cluster:

```bash
  git config --global user.name <github username>
  git config --global user.email <github email>
```

We will have all set a password for github. We will also need to set a personal access token for our account. Information on access tokens can be found at: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens

The access token is only generated once. It can never be viewed again once generated. Therefore, once generated, it will be important to store it somewhere safe.

To generate a personal acces token you need to:
1. Log into github and click on your face/icon in the top-right corner. Then fo to "Settings"
2. IN the bottom left of the Settings page, select "<Developer Settings>"
3. On the left-hand panel, click "Personal access tokens". From that drop down list select "Tokens (classic)"
4. In the central panel select "Generate new token"-> "Generate new token (classic)"
5. You will need to enter your password / 2-factor authentication.
6. Provide a name for the authentication token in "Note" e.g. HPC2 validation. Then select "No expiration", from expiration menue. Finally, tick all boxes under "Repo" and "Project". Then Select "Generate token".
7. You will now be shown your personal access token. Ensure that you copy it as you will NEVER see it again.


Paste the token somewhere convenient in your $HOME area. e.g. into the file

```bash
  nano $HOME/git_repos/token.txt
```

Also make a note of it in a file on your local computer and save it to OneDrive

When on the cluster, if you are ever asked for your password, it is actually asking for this token. In those cases, open the token file and copy that text, so that it can be pasted when asked for the password.

```bash
  cat $HOME/git_repos/token.txt
```

We can now use this access token associated with your personal github account to download private github repositories that you have access too.

Assuming that you have messaged Andrew Armitage your github username, he should have added you into the nri_pathogenomics organisation which contains a lot of private repositories. https://github.com/NRI-pathogenomics

The most useful of these private repositories is the seq_tools repository https://github.com/NRI-pathogenomics/seq_tools. This contains all of andrew's install instructions for programs on hpc2 (https://github.com/NRI-pathogenomics/seq_tools/blob/master/install/instructions_hpc2.md). However, more importantly, it has slurm submission scripts for a wider range of bioinformatic analyses.

Let's try cloning this repository down

```bash
  cd $HOME/git_repos/nri_repos
  git clone https://github.com/NRI-pathogenomics/seq_tools.git
```

If you wanted you could now use the slurm submission script for genome assembly (https://github.com/NRI-pathogenomics/seq_tools/blob/master/assembly/spades/short_read/sub_spades.sh) at:

```bash
  ls $HOME/git_repos/nri_repos/seq_tools/assembly/spades/short_read/sub_spades.sh
  less $HOME/git_repos/nri_repos/seq_tools/assembly/spades/short_read/sub_spades.sh
```

### Make a personal GitHub project

let's go to your personal profile on github and make a repository for you to document some code in.

To make a new personal project:
1. Log into github and click on your face/icon in the top-right corner. Then fo to "Profile"
2. Clock on "Repositories" in the top left.
3. Clock "New" in the central panel
4. Give the repository a name. I suggest keeping it short, no spaces and avoiding it starting with a capital letter. "botrytis_diagnostics", "alternaria_genomics", "phytophthora_RNA-Seq" "Andrew_PhD" would all be good.
5. For the moment, make the directory public. If doing this for your main work, consider asking Andrew to make a repository for you in NRI_pathogenomics which could be private (but would also allow your PI / colleagues to view the code).
6. Select "Add a README file". If you want, consider adding an MIT license, bu this is not necessary.
7. Create a repository

You should now be able to navigate into your repository and clone it down onto the cluster as we did above.

```bash
  cd $HOME/git_repos/nri_repos
  git clone <HTTP address>
```

### Remote text editing of github files

We can document the commands that we are running in our github repositories from our local computers using remote text editors. Using WinSCP (windows) or Atom via sshfs (mac), see if you can navigate into your github repository and open the README.md file.

Once opened, make some edits and save the file.

e.g. try adding the text:

```
  This file contains documentation on bioinformatic analysis performed in <ProjectName>.

  All work documented in this project was performed in the following directory on the cluster:

  \```bash
    ProjectDirectory=/projects/group-nri-ph/nri_projects/training_${USER}
    mkdir $ProjectDirectory
    cd $ProjectDirectory
  \```
  All documentation in the file below relates to analyses performed at this location.

```

Hopefully, this makes documenting your code easier. You can also use this to write your own SLURM job submission scripts and save them into your repository.

Note that this text is in markdown format (README.md) which will be displayed / processed when viewed online.

### Committing changes on github

Once you have modified files in a github repository, we can commit these changes to the code and push them up into the cloud.

These commands only work if we are currently within the git repository that we are editing.

```bash
  cd $HOME/git_repos/nri_repos
  cd <repository name>
```

Commit the change that you have made to the file using git commit and add a message to describe the change made:

```bash
  git commit README.md -m "Added information on where the project directory was located on the cluster."
```

After we have saved changes to our files with commit messages, we can push these changes up to the cloud

```bash
  git push
```
You will need to enter your username and personal access token here.

Once this is done, look for the changes on your github personal repositories.
