# Instructions for Bioinformaticians at the Lund Stem Cell Center using COSMOS-SENS at LUNARC.



In order to keep the system orderly we have set out some guidelines which everyone should follow to maintain a smoothly running platform. Please read this document *carefully* before you use the system.

## What is COSMOS-SENS?

The GDPR is an EU directive that protects information and data of individuals, therefore sequence data from living subjects has to be kept secure under these rules. COSMOS-SENS is a isolated HPC platform housed at LUNARC where we handle and analyse sequence data under GDPR rules. ***This means COSMOS-SENS doesn't have a connection to the internet***, and transfering data to/from CS is done via sftp (more later). In practical terms, many within the SCC use COSMOS-SENS to analyse data from other species (mouse/rat etc) as this is where our main storage is kept.

## Getting access to COSMOS-SENS

### 1) Make an account at SUPR

The first thing you need to do is make an account at [SUPR](https://supr.naiss.se/). Make sure you provide a correct mobile phone number since your phone will be needed in the future for two-factor authentication. When you're done, contact Shamit Soneji (shamit.soneji [at] med.lu.se) stating which SCC lab you belong to. You will then be added to the list of users, after which, you will log back in to SUPR and apply for an account at LUNARC.

### 2) Sign the agreement

**They will send you a user agreement that you will need to print, sign, and scan**. Send the PDF to Shamit for counter-signing, and then someone from LUNARC will then contact you with your password.

### 3) Setup 2FA

LUNARC also uses two-factor authentication using Pocket Pass. Follow the instructions [here](https://lunarc-documentation.readthedocs.io/en/latest/getting_started/authenticator_howto/) to set this up. 

### 4) Request a Static IP address

This is important. As a further layer of security we employ IP address filtering to make sure only authorised users can establish a connection to COSMOS-SENS. While the above is being done, contact LDC (servicedesk@lu.se) and request a fixed IP address for your VPN. When you have this, email it to Shamit for communicaton to LUNARC so it can be whitelisted. If you work on a machine connected by a cable to the wall at the BMC too, ask LDC to fix the IP adress for your port number. Communicate this IP adress at the same time so both VPN and fixed port IPs can be whitelisted.

### 5) Setup the VPN on your laptop/workstation

Go to the LDC guide [here](https://luservicedesk.service-now.com/support_en?id=kb_article_en&sys_id=39c1256187ed8190e42687fc8bbb3578) (will require lucat login) and setup the VPN on your device.


# WARNING!

**DO NOT SHARE YOUR PASSWORD! This volates the terms of he agreement and your account will be disabled if caught.**

## Connecting to COSMOS-SENS

The only way to use COSMOS-SENS is via a web browser where you point it to:

[https://sens05.lunarc.lu.se](https://sens05.lunarc.lu.se)

Login with your **LUNARC** credentials and then using using two-factor authentication using Pocket Pass.

## Where do I put my data?

TBC

Storage is expensive so what we have should be used efficiently. There are two main folders on COSMOS-SENS:

`/raw/<userid>`
and
`/processed/<userid>`

## /raw
This is where you can put raw fastq files. This folder is limited to 400Tb and is backed up to a mirror nightly. Please make sure that fastq files are gzipped.

## /processed
This is where intermediate files should be placed. For example the output from your pipelines that process the data from fastq files. If you use any of the nf-core pipelines, make sure you delete the `work` folder which is normally full of stuff you don't need. If your own pipeline produces SAM files, delete them afterwards of they have been converted to BAM.

**This folder is not backed up!** Anything you produced here should be reproducible from your data in `/raw` and your scripts in `/home/<userid>`.

## Modules
Software on Aurora is organised into modules which are loaded when needed, usually when a job is submitted. a rundown of the module system can be seen [here](https://lunarc-documentation.readthedocs.io/en/latest/manual/manual_modules/), but lets load bowtie2 as an example:

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
Done. If you have too many module conflicts happening, you can wipe the slate clean with:

```
$ module purge
```

### Installing software
This is done by LUNARC and they have aksed that all software request go through us. If you need something installed please email Shamit with a URL to what is needed. You can also transfer software across using singularity images (see below).

## Running jobs
When you login into COSMOS-SENS you will be located on the front-end (FE). **This is not a place to run long computations**. The FE is for small interactive jobs, and many people work here. Long memory and processor intensive jobs should be sent to a compute node.

### SLURM
Any jobs such as making and mapping fastq files should be sent to the blades, and for this, LUNARC uses the SLURM job submission system. See below and [here](https://lunarc-documentation.readthedocs.io/en/latest/manual/submitting_jobs/manual_basic_job/) for basic guidelines on how to submit a job.

### Project code
For those registered to out COSMOS-SENS project, the code needed to submit the job is **csens2024-3-4** and this is what you specify in the preamble of you script. For example let say you have a script for mapping reads called `Run_bowtie.sh`:

```
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

bowtie2 .....
```
In short ```-n 5 ``` requests 5 CPU cores, ```-N 1``` requests one node (do not ask for more), ```-t 24:00:00``` kills the job after 24 hours, ```-A csens2024-3-2``` specifies the project code, ```-J BT2``` is the name of your job, ```-o BT2.%j.out``` is the outfile of the job (name+job id),```-e BT2.%j.err``` is where the error log goes.



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

### Run jobs using the local disk
Consider this, your data is on fs1, and you have also specified the output of your job to fs1. This means your data needs will make a round trip from fs1 to the node and back again. While the connections are fast, this creates needless network traffic. The nodes are fitted with local disks which give much hight I/O speeds, and this is where the output of your computation should be sent while the job is running. This is done using the ```$SNIC_TMP``` environment variable.


The slurm system is set up to give you a temporary folder on the nodes for each slurm job you start. The name of your temporary folder is stored in the $SNIC_TMP (https://linuxize.com/post/how-to-set-and-list-environment-variables-in-linux/). This variable does not exist on the frontend. The folder on the node is private to you and will be deleted once the job is finished. So in the last step of your script you need to save your results. For example, you script might look something like:

```
STAR ...\\
--readFilesIn /somewhere/clusterPipeline/merged_cluster/*.gz \\
... \\
--outFileNamePrefix $SNIC_TMP/out/MyAnanlysis_
cp $SNIC_TMP/out/MyAnanlysis_* /somewhere/Mapped
```

### Be nice to your mates

Please be considerate to your co-workers, resources are always limited. Try to use only the resources as you actually need, and importantly think about what your software is able to do. Most of the available software does not benefit from more than 10 cores, and no programs we know of can use two nodes at the same time.


## Reproducible research

**Never update packages in the middle of a project!**. There is always a chance underlying methods have changed that may cause a shift in the numbers and your results could change. The best thing you can do is "freeze" your packages but putting eveything into a singularity container named accordingly, which you can use again if needed knowing that all package version are the same. Singularity containers are also great for getting software onto LS2 without needed to ask for LUNARC support to do an install. You can read more about[Singularity images here](https://sylabs.io/guides/3.6/user-guide/quick_start.html). Singularity version 3.6 is installed on LS2. If you have an immediate need for a package you might think about creating your own singularity image and upload it to LS2. But before you do that, check out the folder "/projects/fs1/common/singularityImages/", there might already be an image that contains the software you are looking for.

I recommend reading [this pdf](pdfs/HowToUseSingularityOnLsens2.pdf) about how to use singularity images and especially how to use the SingSingCell image that Stafan Lang has created.  

### Nextflow and NF-core

Nextflow and the [NF-core modules](https://nf-co.re/) are a fantastic reseource for us bioinformaticians. There are a multitude of pipelines for various NGS applications which go into real depth regarding QC. Nextflow pipelines have a the potential to rid us of all the bash scripting we have been used to. It even frees us from thinking about SLURM or the amount of processors we need for a given task because this is also taken care of. 

Hence, please try to use the nextflow pipelines installed in "/projects/fs1/common/nextflow/". A minimal help on how to start these pipelines on LS2 can be obtained by looking at the test script for these pipelines "/home/stefanl/common/nextflow/test_all_blade.sh". This way you can see how the input files should be structured and how the pipelines are called. IKt was quite a lot of work to install them - so please try to use them using [this guide](pdfs/NextFlow_Pipelines_on_aurora_ls2.pdf) ;-).
