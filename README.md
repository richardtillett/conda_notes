# Conda notes

## What is conda?

Conda is a package manager for installing software on Linux (and Mac) systems. However, unlike the mainline package managers `yum` and `apt` that ship with Fedora/CentOS/RHEL and Ubuntu/Debian, respectively, conda lets you **install software without requiring sudo privileges.** This makes conda an excellent choice for end-users of shared systems, HPC systems, etc.

The conda project has strong ties to the python community, so it makes sense that a great many python libraries can be found in the `default` and `anaconda` channels, among others. For bioinformatics researchers and life scientists (including this author), there `bioconda` channel is generally excellent too.

The aim of this document is to walk new users of conda through miniconda installation steps, setting up their channels, finding and installing packages, showing the smart practice of using environments, and other useful conda commands.

For more detailed documentation, check out the official docs too.

* Conda documentation [docs home](https://conda.io/projects/conda/en/latest/) and [conda user guide -- getting started](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)
* Bioconda [https://bioconda.github.io](https://bioconda.github.io)
* A [conda cheatsheet](https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html)


## Installing miniconda

Visit the [miniconda](https://conda.io/miniconda.html) download page and get ready to download the installer of your choice/system.

* For miniconda 3 on linux **(we will install this one, miniconda 3 for Linux)**
  * `wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh`
* Mac users who want python3-based miniconda might use this command:
  * `curl -O https://repo.continuum.io/miniconda/Miniconda3-latest-MacOSX-x86_64.sh`

Next, make the installer executable with `chmod` and then run the installer. Be prepared to interact with it a little bit, which will include reading (scrolling) through a EULA.

```bash
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh
```

It will run for a bit, then ask you to scroll through the license and confirm installation details. Then it will run for a bit more. When it is complete, **it instructs us to exit our terminal and launch a new one. Let's do exactly that.**

## Configuring your conda with channels
Now we install some useful channels (searchable repositories, essentially) for Miniconda

```bash
conda config --add channels r
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
```

## Using conda to search for bioinformatics tools

Searching for packages by (exact) name

Let's try searching for Lior Pachter's kallisto RNA-seq tool, and the slightly more traditional bowtie-2 alignment tool

```bash
conda search kallisto
conda search samtools
conda search bowtie-2
```

So, kallisto is found, but bowtie-2 is not. *Let's use google* to see if there might be a bowtie 2 package but with a slightly different name than the one we searched for. Google the phrase `conda bowtie-2`.

It looks like the correct package name is `bowtie2`. Let's try:

`conda search bowtie2`

## Installing named packages:

You have choices. There are two ways to install things with conda.
1. Directly e.g. `conda install kallisto`
2. Toggle-able Environments

Option 1 is much simpler. It installs it without an environmentm into your base installation. But this can get you into trouble in the long run. If you need to change versions of an installed software, it will be at least a bit more more work to remove the existing one and replace it with the different version `conda remove [tool]`. In worse scenarios, if you have incompatible software, they will conflict unless encapsulated within environments.

Installing to an environment, and activating the environment take these two forms:

```bash
conda create -n [my_cool_env] [tool1] [tool2] ... [toolN]
conda activate [my_cool_env]
```
Using environments is smarter in the long run. It comes with cognitive costs, though. You have to remember your environment name (or look for it) and activate your desired environment after logging in.

Let's install bowtie2 and samtools into an environment.

`conda create -n align_env -c bioconda bowtie2 samtools`

You can activate the `align_env` to use these versions

`conda activate align_env` (and now verify that they're installed using `which` and test run them too)

And deactivate the env thusly

`conda deactivate`

### Installing a specific version of a package in a new environment

To create a specifc environment for Stacks, version 1.47, this command looks like:

```bash
conda create -n stacks147_env -c bioconda stacks=1.47
```



## What did I install last week/month/year?

First, take a look at the usage and syntax for `conda info`, by using

`conda info --help` (the --help option can be used for all conda verbs)

If you forget the names of some of your Environments

`conda info -e`

to see what is a part of that Environment

`conda list -n my_cool_env`

to see what is installed in base

`conda list -n base` (base is an environment too)

To check the overall configuration of your conda installation

`conda info`


## Adding more tools to an existing environment

To install new things (here, FastQC) to an environment you previously made

First, search for FastQC (google for the capitalization or not, then)

`conda search fastqc`

Then,

`conda install -n align_env -c bioconda fastqc`

Verify by activating the environment, `which`, and testing the help messages of fastqc

### Updating conda
`conda update conda`

### Updating installed packages
Updating something installed to base
`conda update bowtie2`

Updating a tool that is in an environment
`conda update -n kallisto_env bowtie2`

## Toggling Environments

(if in an environment)
```bash
conda deactivate
conda activate align_env
```

## Removing packages
Removing packages in an environment

`conda remove -n align_env samtools`

Removing entire environments

`conda remove -n align_env --all`

Removing something installed to base (the lazy way)

`conda remove -n kallisto`

## Turning the entire conda system off

1. open (edit) your `~/.bashrc` file using `nano`
2. scroll all the way down until you see lines related to Miniconda
3. comment those out with the `#` symbol at the start of the line
4. log out and log back in (this is more safe than just using `source ~/.bashrc` with conda)
