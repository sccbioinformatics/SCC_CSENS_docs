# Instructions for Bioinformaticians at the Lund Stem Cell Center using COSMOS-SENS at LUNARC.



In order to keep the system orderly we have set out some guidelines which everyone should follow to maintain a smoothly running platform. Please read this document *carefully* before you use the system.

## What is COSMOS-SENS?

The GDPR is an EU directive that protects information and data of individuals, therefore sequence data from living subjects has to be kept secure under these rules. COSMOS-SENS is a isolated HPC platform housed at LUNARC where we handle and analyse sequence data under GDPR rules. ***This means COSMOS-SENS doesn't have a connection to the internet***, and transfering data to/from COSMOS-SENS is done via sftp (more later). In practical terms, many within the SCC use COSMOS-SENS to analyse data from other species (mouse/rat etc) as this is where our main storage is kept.

## Who is COSMOS-SENS for?

Before you request an account, you should be comfortable using a UNIX based high-performance computing (HPC) platform. This document is mostly for the benefit of users at the Lund Stem Cell Center who are placed in Group 1 (gr01).

**The SCC storage is not for general purpose file storage.** It is for data being actively worked on. for general storage needs you need contact LDC for other options.

## Some general ground rules

1) **Work cleanly**. We have limited disk space so it should be used wisely. Only put raw data and scripts into `/backup`, and make sure data is compressed.

2) **Log out when you are done working** (not just close the browser). We have 20 Thinlinc licenses for our group, so logging out will release a license for someone else.

3) **Do not put published data into `/backup`**. When you publish your own data, move the raw data out of `/backup` to somewhere outside this folder.

4) **Delete any intermediate files you do not need**. SAM files are a good example of this when BAMs have also been made.

5) **Do not duplidate files between personal and shared folders**. This is especially true for files in `backup`.

6) **If you need help, your first point of contact is either Shamit or Stefan**. We will escalate issues to LUNARC if need be. This is to prevent them being swamped.

7) If you have/do anything (software for example) that might be useful to other people, make it known on the Slack workgroup. We can place your software somewhere centrally for all to use.

## Getting access to COSMOS-SENS

### 1) Make an account at SUPR

The first thing you need to do is make an account at [SUPR](https://supr.naiss.se/). Make sure you provide a correct mobile phone number since your phone will be needed in the future for two-factor authentication. When you're done, contact Shamit Soneji (shamit.soneji [at] med.lu.se) stating which SCC lab you belong to. You will then be added to the list of users. 

### 2) Log back in to SUPR and apply for an account at LUNARC.

Left menu under "Accounts". Fill out the details correctly and use your mobile number. This will be needed for 2-factor authentication.

### 3) Request a Static IP address

**This is important, do this straight after step 2**. As a further layer of security we employ IP address filtering to make sure only authorised users can establish a connection to COSMOS-SENS. While the above is being done, contact LDC (servicedesk@lu.se) and request a fixed IP address for your VPN. When you have this, email it to Shamit for communicaton to LUNARC so it can be whitelisted. If you work on a machine connected by a cable to the wall at the BMC too, ask LDC to fix the IP address for your port number. **Communicate this IP adress to Shamit at the same time so both VPN and fixed port IPs can be whitelisted**.


### 4) Sign the agreement

**LUNARC will send you a digital agreement that you will need sign via Lucat**. When both you and Shamit have done this, someone from LUNARC will contact you with details on how you create your password.

### 5) Setup 2FA

LUNARC also uses two-factor authentication using Pocket Pass. Follow the instructions [here](https://lunarc-documentation.readthedocs.io/en/latest/getting_started/authenticator_howto/) to set this up. 



### 6) Setup the VPN on your laptop/workstation

Go to the LDC guide [here](https://luservicedesk.service-now.com/support_en?id=kb_article_en&sys_id=39c1256187ed8190e42687fc8bbb3578) and setup the VPN on your device. **When you click on the link it will look like a blank page. You need to login using Lucat (top-right) to see all connection options**.


## WARNING!

**DO NOT SHARE YOUR PASSWORD! This volates the terms of he agreement and your account will be disabled.**

## Connecting to COSMOS-SENS

**Do not forget to activate your VPN before you try to connect**

The only way to use COSMOS-SENS is via a web browser where you point it to:

[https://sens01.lunarc.lu.se](https://sens01.lunarc.lu.se)

Login with your **LUNARC** credentials and then using using two-factor authentication using Pocket Pass. You will be presented with an option as to which GNOME desktop you would like to use. Pick the one you prefer. 

## Getting data to COSMOS-SENS

Getting data to COSMOS-SENS can **only** be done via sftp via the diode (**cs-diode.lunarc.lu.se**), but this is made easier by mounting a COSMOS-SENS folder to folder on the local machine you are working on. Files can then be copied/moved to this mounted folder which will then become available on COSMOS-SENS.

You will need to install `sshfs` if you haven't already. For Linux machines the `sshfs` package can be installed via your package manager, and for Mac users there is [macFUSE](https://osxfuse.github.io/). Windows users will have to transfer files using WinSCP with the 2FA option or Filezilla with the interactive login option.

To mount a folder (Linux/macOS) do the following:

1) Login to COSMOS-SENS and make a folder. For example `mkdir FileTransferFolder`

2) Make a folder on your **local** machine that you will mount to. For example `ToCSENS`

3) To mount the folder on COSMOS-SENS to your local machine do:

`sshfs <userID>@cs-diode.lunarc.lu.se:/<pathto>/FileTransferFolder/ ToCSENS/`

**NOTE!** If you are using a Mac you will need to use the `-o` flag like so:

`sshfs  -o defer_permissions <userID>@cs-diode.lunarc.lu.se:/<pathto>/FileTransferFolder/ ToCSENS/`

So if your `FileTransferFolder` was made in `/home` it would look something like:

`sshfs <userid>@cs-diode.lunarc.lu.se:/home/<userid>/FileTransferFolder/ ToCSENS/`

4) You can now copy/move files into `ToCSENS` on your local machine where they will be available in COSMOS-SENS. Equally, copying/moving files into `FileTransferFolder` on COSMOS-SENS will make them available on your local computer.

5) When you are done with the connection to COSMOS-SENS, you can unmount the folder by doing

`fusermount -u ToCSENS`

On a Mac (will work for Linux too):

`umount ToCSENS`

**Just to be clear, all files in this mounted folder are located on COSMOS-SENS**. When you unmount `ToCSENS` that folder will be empty again. 

### Getting data to COSMOS-SENS from LSENS2

The process is the same when logged into LS2, the connection is made however like so:

`sshfs <userID>@cs-diode:/<pathto>/FileTransferFolder/ ToCSENS/`

i.e you don't need the `.lunarc.lu.se` part.

## Cut & Paste

Lets get this out of the way now. Cut and paste is **not** allowed from COSMOS outwards, i.e you **cannot** `ctrl-c` in COSMOS and `ctrl-v` to a window on your local machine. You *can* paste *into* COSMOS using the clipboard which is located on the left side of the screen. Click on the arrow to pull it out.

The easiest way to C&P is to mount a COSMOS-SENS folder on to your local machine, and then just have a simple text file open in this window that can be open on both COSMOS and your local computer. This can then be a two-way clipboard between the two systems.

## Where do I put my data?

Storage is expensive, so what we have should be used efficiently. There are two main folders on COSMOS-SENS:

Your `home` folder will contain a folder called `backup`.

#### /home/(userid)/backup
This is where you can put raw fastq files. **This folder is limited to 400Tb and is backed up to a mirror nightly**. Please make sure that fastq files are gzipped.

### /home/(userid)
Intermediate files should be placed outside the backup folder. For example, the output from your pipelines that process fastq files. If you use any of the nf-core pipelines, make sure you delete the `work` folder which is normally full of stuff you don't need. If your own pipeline produces SAM files, delete them afterwards of they have been converted to BAM.


**The backup is only for technical issues, it is not intended for file recovery if you do something wrong**. The `/backup` folders are *mirrored* to another drive, so if you delete a file in `/backup` it will also be deleted from the mirror. **We do not take daily/weekly snapshots**.

## Modules
Software at LUNARC is organised into modules which are loaded when needed, usually when a job is submitted. a rundown of the module system can be seen [here](https://lunarc-documentation.readthedocs.io/en/latest/manual/manual_modules/), but lets load bowtie2 as an example:

First we find the options avabilable using ```module spider```:

```
$ module spider bowtie2

----------------------------------------------------------------------------
  Bowtie2:
----------------------------------------------------------------------------
    Description:
      Bowtie 2 is an ultrafast and memory-efficient tool for aligning
      sequencing reads to long reference sequences. It is particularly good
      at aligning reads of about 50 up to 100s or 1,000s of characters, and
      particularly good at aligning to relatively long (e.g. mammalian)
      genomes. Bowtie 2 indexes the genome with an FM Index to keep its
      memory footprint small: for the human genome, its memory footprint is
      typically around 3.2 GB. Bowtie 2 supports gapped, local, and
      paired-end alignment modes. 

     Versions:
        Bowtie2/2.2.6
        Bowtie2/2.2.8
        Bowtie2/2.2.9
        Bowtie2/2.3.2
        Bowtie2/2.3.4.1
        Bowtie2/2.3.4.2
        Bowtie2/2.4.4

----------------------------------------------------------------------------
  For detailed information about a specific "Bowtie2" module (including how to load the modules) use the module's full name.
  For example:

     $ module spider Bowtie2/2.3.4.1
----------------------------------------------------------------------------
```

You can see there are several version available. To see how to load version 2.4.4 we need to run ```module spider`` again:

```
$ module spider Bowtie2/2.4.4

----------------------------------------------------------------------------
  Bowtie2: Bowtie2/2.4.4
----------------------------------------------------------------------------
    Description:
      Bowtie 2 is an ultrafast and memory-efficient tool for aligning
      sequencing reads to long reference sequences. It is particularly good
      at aligning reads of about 50 up to 100s or 1,000s of characters, and
      particularly good at aligning to relatively long (e.g. mammalian)
      genomes. Bowtie 2 indexes the genome with an FM Index to keep its
      memory footprint small: for the human genome, its memory footprint is
      typically around 3.2 GB. Bowtie 2 supports gapped, local, and
      paired-end alignment modes. 


    You will need to load all module(s) on any one of the lines below before the "Bowtie2/2.4.4" module is available to load.

      GCC/10.3.0
```

So to load bowtie2, we also need to load the ```GCC/10.3.0``` module. So we can now load bowtie2 by doing:

```
$ module load GCC/10.3.0 Bowtie2/2.4.4
```

A short way of doing this is:

```
$ ml GCC/10.3.0 Bowtie2/2.4.4
```

Done. If you have too many module conflicts happening, you can wipe the slate clean with:

```
$ module purge
```

## Installing software
This is done by LUNARC and they have aksed that all software request go through us. If you need something installed please email Shamit with a URL to what is needed. You can also transfer software across using singularity images (see below).

## Running jobs
When you login into COSMOS-SENS you will be located on the front-end (FE). **This is not a place to run long computations**. The FE is for small interactive jobs, and many people work here. Long memory and processor intensive jobs should be sent to a compute node.

### SLURM
Any jobs such as making and mapping fastq files should be sent to the blades, and for this, LUNARC uses the SLURM job submission system. See below and [here](https://lunarc-documentation.readthedocs.io/en/latest/manual/submitting_jobs/manual_basic_job/) for basic guidelines on how to submit a job.

### Project code
For those registered to our COSMOS-SENS project, the code needed to submit the job is **csens2024-3-4** and you need to specify in the preamble of you script. Lets say you want to map reads using bowtie, an example header for a job `Run_bowtie.sh` could look like this:

```shell
#! /bin/bash
#SBATCH -n 5
#SBATCH -N 1
#SBATCH -t 24:00:00
#SBATCH -A lsens2018-3-3
#SBATCH -J BT2
#SBATCH -o BT2.%j.out
#SBATCH -e BT2SingSing.%j.err
module purge

module load GCC/10.3.0 Bowtie2/2.4.4

bowtie2 <rest of the command here>
```
In short `-n 5 ` requests 5 CPU cores, `-N 1` requests one node (do not ask for more), `-t 24:00:00` kills the job after 24 hours, `-A csens2024-3-2` specifies the project code, `-J BT2` is the name of your job, `-o BT2.%j.out` is the outfile of the job (name+job id),`-e BT2.%j.err` is where the error log goes.



The script is then run using:

```
$ sbatch Run_bowtie.sh
```

You can see your own jobs lining up using:

```
$ sacct
```
All jobs using:

```
$ squeue
```
If you know you have screwed something up and you want to kill a job:

```
$ scancel <jobid>
```

### GPU nodes
If your program can use GPUs then you can run on the GPU nodes. You will need to add a partition parameter in your SLURM script so:

```shell
#SBATCH -p sens-gpu
```

Don't forget to load the CUDA modules if you need them:

```shell
module load CUDA/12.1.1
```

## Reference genomes

These are located `/references/AWS-iGenomes/` which have been downloaded from [here](https://ewels.github.io/AWS-iGenomes/). This folder also includes the nf-core pipelines and a few other resources which will be updated along the way.

We have also copied across the `/projects/fs1/common/` folder from LSENS2 which is now located at `/scale/gr01/shared/common/` on COSMOS-SENS.

## Run jobs using the local disk
Most jobs will create temporary files when running, so it makes sense (where possible) to use the local disk on each node to improve speed and reduce network traffic between the nodes and the storage.

The slurm system is set up to give you a temporary folder on the nodes for each slurm job you start. The name of your temporary folder is stored in the `$SNIC_TMP`. This variable does not exist on the frontend. The folder on the node is private to you and will be deleted once the job is finished. So in the last step of your script you need to save your results. For example, you script might look something like:

```
STAR ...\\
--readFilesIn /somewhere/clusterPipeline/merged_cluster/*.gz \\
... <rest of STAR command>\\
--outFileNamePrefix $SNIC_TMP/out/MyAnanlysis_
cp $SNIC_TMP/out/MyAnanlysis_* /processed/<userid>/pathtofinaldestination/
```

So in this case, the output is directed to the **local** storage at `$SNIC_TMP/out/MyAnanlysis_`, and once the mapping is complete the results are copied to where you want you need them.

## Be nice to your mates

Please be considerate to your co-workers, resources are always limited. Try to use only the resources as you actually need, and importantly think about what your software is able to do. Most of the available software does not benefit from more than 10 cores, and no programs we know of can use two nodes at the same time.


## Reproducible research

**Never update packages in the middle of a project!**. There is always a chance underlying methods have changed that may cause a shift in the numbers and your results could change. The best thing you can do is "freeze" your packages but putting eveything into a singularity container named accordingly, which you can use again if needed knowing that all package version are the same. Singularity containers are also great for getting software onto COSMOS-SENS without needing LUNARC support to do an install. You can read more about [Singularity images here](https://sylabs.io/guides/3.6/user-guide/quick_start.html). Singularity is installed on COSMOS-SEMS and no modules are needed to load it. If you have an immediate need for a package you might think about creating your own singularity image and uploading it to COSMOS-SENS. Before you do that, check out the folder "/projects/fs1/common/singularityImages/", there might already be an image that contains the software you are looking for.

I recommend reading [this pdf](pdfs/HowToUseSingularityOnLsens2.pdf) about how to use singularity images and especially how to use the SingSingCell image that Stefan Lang has created.  

## Nextflow and NF-core **(WORK IN PROGRESS)**

Nextflow and the [NF-core modules](https://nf-co.re/) are a fantastic resource for bioinformaticians. There are a multitude of pipelines for various NGS applications which go into real depth regarding QC, mapping methods, annotatation, and reporting. After a pipline is run, you also they the version numbers of the software used for easy reporting in papers, but importantly, the pipeline remains the same everytime you run it ensuring results are comparable.

When nf-core piplines are run, docker images are pulled over the internet for use, but as COSMOS-SENS is a secure server with no internt connection, pipelines have to be downloaded and then transfered to COSMOS-SENS. In this case we are also downloading the required singularity images. If you require a pipeline that isn't there, please contact Shamit to have it downloaded and installed. It is far better we have them in one place for all to use, rather than duplicating them all over the system.

### Setting up to use nf-core

There are several commonly used piplines located at `/scale/gr01/shared/nf-core`, and to use them you need to add a few lines into a file called `.bash_profile`.

1) Open the file using `gedit ~/.bash_profile`

2) Add copy/paste these lines in at the end and save the file:

```
export NXF_OFFLINE='true'
export NXF_SINGULARITY_LIBRARYDIR=/scale/gr01/shared/nf-core/singularity_cache
export NXF_SINGULARITY_CACHEDIR="$NXF_SINGULARITY_LIBRARYDIR"
export SINGULARITY_CACHEDIR="$NXF_SINGULARITY_LIBRARYDIR"
export NXF_TEMP=$SNIC_TMP
export SINGULARITY_BIND="/scale"
export NXF_HOME=/scale/gr01/shared/nf-core/nextflow_sub/
```

You should then logout of COSMOS-SENS and log back in so these changes take effect. These lines set the environment variables that


1) Tells Nextflow where the singularity images are located (`/scale/gr01/shared/nf-core/singularity_cache`)

2) Set the temp dir to the ones located on the nodes (`$SNIC_TMP`).

3) Binds the sigularity images to `/scale` so they can be found system wide.

4) Sets the `home` of Nextflow to the central copy on shared (`/scale/gr01/shared/nf-core/nextflow_sub/`). This means each user doesn't need their own.

When running a job, use the config file located here:

`/scale/gr01/shared/nf-core/cosmo_sens_grp1.config`


This file contains the project ID and automatically sets the RAM and core resources so you don't have to.


### An example using RNAseq

A sample sheet (`samplesheet.csv`) would look something like:

```shell
sample,fastq_1,fastq_2,strandedness
Sample1_R1.fastq.gz,Sample1_R2.fastq.gz,auto
Sample2_R1.fastq.gz,Sample2_R2.fastq.gz,auto
Sample3_R1.fastq.gz,Sample3_R2.fastq.gz,auto
```

and a script `NFC_RNAseq.sh` running the basic rnaseq pipline would look like this:

```shell
module load Nextflow/23.10.1

nextflow run  /scale/gr01/shared/nf_core/nf-core-rnaseq-3.14.0/workflow/main.nf \
-profile singularity \
--input samplesheet.csv \
-c /scale/gr01/shared/nf-core/cosmos_sens_grp1.config \
--genome GRCm38 \
--igenomes_base /references/AWS-iGenomes/ \
--outdir /home/<userid>/RNAseq_project/NFC_out
```


This script, the fastq files, and the samplesheet are all in the same folder. The pipline is launched using:

`sh NFC_RNAseq.sh`

### Cut&Run

```shell
module load Nextflow/23.10.1

nextflow run /scale/gr01/shared/nf-core/nf-core-cutandrun_3.2.2/3_2_2 \
--input samplesheet.csv \
-profile singularity \
-c /scale/gr01/shared/nf-core/cosmos_sens_grp1.config \
--fasta /scale/references/AWS-iGenomes/Homo_sapiens/Ensembl/GRCh37/Sequence/WholeGenomeFasta/genome.fa \
--gtf /scale/references/AWS-iGenomes/Homo_sapiens/Ensembl/GRCh37/Annotation/Genes \
--spikein_bowtie2 /scale/references/AWS-iGenomes/Escherichia_coli_K_12_MG1655/NCBI/2001-10-15/Sequence/Bowtie2Index \
--spikein_fasta /scale/references/AWS-iGenomes/Escherichia_coli_K_12_MG1655/NCBI/2001-10-15/Sequence/WholeGenomeFasta/genome.fa \
--outdir /home/<userid>/<path_to_out>/CaR_Out
```

### Sample sheets
The contents of the sample sheet will vary depending on which pipline you are using, so be sure to read the manual.

### Remove the work folder
nf-core piplines will make a `work` folder if the tmp folder on the node isn't used. **Delete this folder when you are done!** It is normally huge and contains nothing of interest.

## Singularity

Software can be packaged into Singularity containers which provide a complete environment that can be copied over to COSMOS-SENS and used there without having to install other software on COSMOS-SENS. This means you get exactly what you need, and reduces the workload on the good people at LUNARC. *This is also a really good way of practicing reproducible research*. A project can be done using a singularity image, so if you need to go back to it in X years, you have the original software setup you had when you did it. If you upgrade a piece of software, you can make a new version of the image so the previous version still exists in the older version.

In this example we will make a Sigularity image which will contain R and Seurat, which will then be modularised to be used on COSMOS-SENS to be used with a Jupyter Notebook.

First up, install [install Singularity](https://docs.sylabs.io/guides/3.0/user-guide/installation.html) on your system. FYI, on Ubuntu `sudo apt install singularity` actually installs a **game** not the container system!


When you have done this you can make a minimal image by using this recipe:


```shell
Bootstrap: debootstrap
OSVersion: jammy
MirrorURL: http://archive.ubuntu.com/ubuntu/

%post
  export PYTHONNOUSERSITE="true"
  sed -i 's/main/main restricted universe/g' /etc/apt/sources.list
  apt-get update
  # Install R, Python, misc. utilities
  apt-get install -y libopenblas-dev libcurl4-openssl-dev libopenmpi-dev openmpi-bin openmpi-common openmpi-doc openssh-client openssh-server

  apt install -y wget build-essential git vim software-properties-common 

  apt install -y curl vim git libhdf5-dev
  apt install -y libssl-dev libxml2-dev python3-pip

  apt install -y nodejs cmake

  ## next is important for python tsne
  apt install -y libatlas-base-dev
  ## cellexalvrR
  apt install -y pandoc

  pip3 install --upgrade pip
  pip3 install --upgrade jupyter jupyterlab ipython-autotime ipywidgets

  add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
  wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
  add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"

  apt  install -y r-cran-irkernel r-cran-irdisplay r-cran-seurat python3-ipykernel

  apt clean

  mkdir /workspace
  echo "jupyter lab --port 9734 --ip=0.0.0.0 --allow-root --no-browser" > /workspace/launch_jupyter.sh
  chmod +x /workspace/launch_jupyter.sh

%runscript
  /workspace/launch_jupyter.sh

%environment
    export PYTHONNOUSERSITE="true"
    export MYVAR="Hello"
```

This will take the Ubunutu "Jammy" release and install build essentials, Python, base R (4.3), and jupyter notebooks. It will also prep a file `launch_jupyter.sh` that is invoked to open a notebook when the image is used.

To build the image from this recipe:

`sudo singularity build --sandbox sandbox  minimal_recipe.txt`

We can now shell into the image to install more programs as needed:

``singularity shell --fakeroot -w sandbox/``

At this point you treat it like any other Ubunutu distro. For example updating all the distro pacakages:

```
sudo apt update
sudo apt upgrade
```

If libraries are missing you can install them via `apt` for example as you need them.

After shelling in you can start R:

```
Apptainer> R

R version 4.1.2 (2021-11-01) -- "Bird Hippie"
Copyright (C) 2021 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

```

and then install everything you need. For example Seurat v4:

```shell
> update.packages(ask = FALSE, checkBuilt = TRUE)
> install.packages(devtools)

```