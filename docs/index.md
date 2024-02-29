# Instructions for Bioinformaticians using the Lund Stem Cell Center LS2 compute platform.

Congratulations, you have been deemed worthy enough to be granted access to LS2.

In order to keep the system orderly we have set out some guidelines which everyone should follow to maintain a smoothly running platform. Please read this document *carefully* before you use the system.

## What is LS2?

The GDPR is an EU directive that protects information and data of individuals, therefore sequence data from living subjects has to be kept secure under these rules. LS2 is a isolated HPC platform housed at LUNARC where we handle and analyse sequence data under these GDPR rules. ***This means LS2 doesn't have a connection to the internet***, and transfering data to/from LS2 is done via inbox/outbox system. In practical terms, many within the SCC use LS2 to analyse data from other species (mouse/rat etc) as this is where our main storage is kept.

## Getting access to LS2

### 1) Make an account at SUPR

The first thing you need to do is make an account at [SUPR](https://supr.naiss.se/). Make sure you provide a correct mobile phone number since your phone will be needed in the future for two-factor authentication. When you're done, contact Shamit Soneji (shamit.soneji [at] med.lu.se) stating which SCC lab you belong to. You will then be added to the list of users, after which, you will log back in to SUPR and apply for an account at LUNARC.

### 2) Sign the agreement

**They will send you a user agreement that you will need to print, sign, and scan**. Send the PDF to Shamit for counter-signing, and then someone from LUNARC will then contact you with your password.

### 3) Setup 2FA

LUNARC also uses two-factor authentication using Pocket Pass. Follow the instructions [here](https://lunarc-documentation.readthedocs.io/en/latest/getting_started/authenticator_howto/) to set this up. 

### 4) Request a Static IP address

As a further layer of security we employ IP addrss filtering to make sure only authorised users cann see a connection to LS2. While the above is being done, contact LDC (servicedesk@lu.se) and request a fixed IP address for your VPN. When you have this, email it to Shamit for communicaton to LUNARC so it can be whitelisted.

### 5) Setup the VPN on your laptop/workstation

Go to the LDC guide [here](https://luservicedesk.service-now.com/support_en?id=kb_article_en&sys_id=39c1256187ed8190e42687fc8bbb3578) (will require lucat login) and setup the VPN on your device.


## Connecting to Aurora LS2

If all the above has been done, fire up your VPN and try to connect.

### Server address

The server address is aurora-ls2.lunarc.lu.se. You can connect using a terminal:

```
ssh <username>@aurora-ls2.lunarc.lu.se
```
It will ask for your password, and then your two-factor code from your Pocket Pass.

 You can also connect using Thinlinc which gives you a full Linux desktop. Follow the instuctions [here](https://lunarc-documentation.readthedocs.io/en/latest/getting_started/using_hpc_desktop/) to use that. 

## Where do I put my data?

LS2 has four primary drives fs1, fs3, fs5 and fs7 found at:


/projects/fs1/<username\>

/projects/fs3/<username\>

/projects/fs5/<username\>

/projects/fs7/<username\>


Each of these drives are mirrored nightly to a unit the same size. No snapshots are stored, this is merely to protect against mechanical failure.

These drives should be used in a certain way, and this is how:

1. **Raw data** - this (and ONLY this) should go to /projects/**fs5**/\<username\>
    Raw data are e.g. fastq files (gzipped!!) or raw read folders.

2. **Processed data** (BAMS, counts etc) should be directed to either **fs1** or **fs3** depending on which one you were assigned to when given access. Intermediate files such as SAM files etc should be removed to save space.

3. Raw data that has been published should be *moved* to **fs7** for achive.

4. Scripts should be stored on fs1/fs3.

**UNLESS INSTRUCTED OTHERWISE, ASSUME YOUR PLACE IS ON FS1**


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
When you login into LS2 you will be using the front-end (FE). **This is not a place to run long computations**. The FE is for small intractive jobs, and many people work here which is why long memory and processor intensive jobs should be sent to a compute node.

### SLURM
Any jobs such as making and mapping fastq files should be sent to the blades, and for this, LUNARC uses the SLURM job submission system. See below and [here](https://lunarc-documentation.readthedocs.io/en/latest/manual/submitting_jobs/manual_basic_job/) for basic guidelines on how to submit a job.

### Project code
For those registered to the LS2 project, the code needed to submit the job is **lsens2018-3-3** and this is what you specify in the preamble of you script. For example let say you have a script for mapping reads called `Run_bowtie.sh`:

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
In short ```-n 5 ``` requests 5 CPU cores, ```-N 1``` requests one node (don't ask for more), ```-t 24:00:00``` kills the job after 24 hours, ```-A lsens2018-3-3``` specifies the project code, ```-J BT2``` is the name of your job, ```-o BT2.%j.out``` is the outfile of the job (name+job id),```-e BT2.%j.err``` is where the error log goes.



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
