# Instructions for Bioinformaticians at the Lund Stem Cell Center using COSMOS-SENS at LUNARC.



In order to keep the system orderly we have set out some guidelines which everyone should follow to maintain a smoothly running platform. Please read this document *carefully* before you use the system.

## What is COSMOS-SENS?

The GDPR is an EU directive that protects information and data of individuals, therefore sequence data from living subjects has to be kept secure under these rules. COSMOS-SENS is a isolated HPC platform housed at LUNARC where we handle and analyse sequence data under GDPR rules. ***This means COSMOS-SENS doesn't have a connection to the internet***, and transfering data to/from COSMOS-SENS is done via sftp (more later). In practical terms, many within the SCC use COSMOS-SENS to analyse data from other species (mouse/rat etc) as this is where our main storage is kept.

## Who is COSMOS-SENS for?

Before you request an account, you should be comfortable using a UNIX based high-performance computing (HPC) platform. This document is mostly for the benefit of users at the Lund Stem Cell Center who are placed in Group 1 (gr01).

!!! note inline end "Note"

    **The SCC storage is not for general purpose file storage.** It is for data being actively worked on. for general storage needs you need contact LDC for other options.

## Nodes
COSMOS-SENS has a mix of nodes you can use. They are:

1) **32 x standard nodes**. 2 x AMD, 48 cores, 256GB RAM, 2TB temporary directory given by `$SNIC_TMP` or `$TMPDIR`.

2) **9 x HiRAM nodes**. 2 x AMD, 48 cores, **2TB** RAM, 2TB temporary directory given by `$SNIC_TMP` or `$TMPDIR`.

If you want to send a job to these nodes you need to add `#SBATCH -p sens-xl` to the preamble of your sbatch script (explained below). `-p` specifies which partition the job is sent to.

3) **3 x GPU nodes** (NVIDIA A40), 2 x 24 cores.  **2TB** RAM

To send jobs here, use `#SBATCH -p sens-gpu`.


!!! note inline end
    By default jobs are sent to the standard nodes, so `-p` does not need to be specified. Make sure your jobs actually **need** the `xl` and `gpu` nodes if you send them there.

## Some general ground rules

!!! warning inline end ""

    1) **Work cleanly**. We have limited disk space so it should be used wisely. Only put raw data and scripts into `/backup`, and make sure data is compressed.

    2) **Log out when you are done working** (not just close the browser). We have 20 Thinlinc licenses for our group, so logging out will release a license for someone else.

    3) **Do not put published data into `/backup`**. When you publish your own data, move the raw data out of `/backup` to somewhere outside this folder.

    4) **Delete any intermediate files you do not need**. SAM files are a good example of this when BAMs have also been made.

    5) **Do not duplicate files between personal and shared folders**. This is especially true for files in `backup`.

    6) **If you need help, your first point of contact is Petter, the SCC HPC Coordinator** (see [Getting help](#getting-help) below).

    7) If you have/do anything (software for example) that might be useful to other people, make it known on the Teams workgroup. We can place your software somewhere centrally for all to use.

## Getting help

If something isn't working, this is the path an issue follows:

1) **Check Teams first**. If it looks like a general problem with the cluster (for example everyone's jobs are stuck, storage is unreachable, or nobody can log in), it's likely already being discussed there.

2) **Raise the issue** with Petter, the SCC HPC Coordinator, via Teams or email (petter.storm [at] med.lu.se). Include what you were doing, what happened, and any relevant error messages or job IDs.

3) **Petter triages it**: clarifying the problem, checking for known issues, coordinating any follow-up, and keeping you informed.

4) **Resolve or escalate**: most things are resolved directly; anything platform-level gets escalated to LUNARC. This exists so LUNARC isn't swamped with issues that can be handled locally first.

## Getting access to COSMOS-SENS

### 1) Make an account at SUPR

The first thing you need to do is make an account at [SUPR](https://supr.naiss.se/). Make sure you provide a correct mobile phone number since your phone will be needed in the future for two-factor authentication. When you're done, contact Petter Storm (petter.storm [at] med.lu.se) stating which SCC lab you belong to. You will then be added to the list of users. 

### 2) Log back in to SUPR and apply for an account at LUNARC.

Left menu under "Accounts". Fill out the details correctly and use your mobile number. This will be needed for 2-factor authentication.

### 3) Request a Static IP address

**This is important, do this straight after step 2**. As a further layer of security we employ IP address filtering to make sure only authorised users can establish a connection to COSMOS-SENS. While the above is being done, contact LDC (servicedesk@lu.se) and request a fixed IP address for your VPN. When you have this, email it to Petter for communicaton to LUNARC so it can be whitelisted. If you work on a machine connected by a cable to the wall at the BMC too, ask LDC to fix the IP address for your port number. **Communicate this IP adress to Petter at the same time so both VPN and fixed port IPs can be whitelisted**.


### 4) Sign the agreement

**LUNARC will send you a digital agreement that you will need sign via Lucat**. When both you and Petter have done this, someone from LUNARC will contact you with details on how you create your password.

### 5) Setup 2FA

LUNARC also uses two-factor authentication using Pocket Pass. Follow the instructions [here](https://lunarc-documentation.readthedocs.io/en/latest/getting_started/authenticator_howto/) to set this up. 



### 6) Setup the VPN on your laptop/workstation

Go to the LDC guide [here](https://luservicedesk.service-now.com/support_en?id=kb_article_en&sys_id=39c1256187ed8190e42687fc8bbb3578) and setup the VPN on your device. **When you click on the link it will look like a blank page. You need to login using Lucat (top-right) to see all connection options**.





!!! danger inline end "WARNING"
    **DO NOT SHARE YOUR PASSWORD! This volates the terms of he agreement and your account will be disabled.**

## Connecting to COSMOS-SENS


!!! note inline end
    **Do not forget to activate your VPN before you try to connect**

The only way to use COSMOS-SENS is via a web browser where you point it to:

[https://sens01.lunarc.lu.se](https://sens01.lunarc.lu.se)

Login with your **LUNARC** credentials and then using using two-factor authentication using Pocket Pass. You will be presented with an option as to which GNOME desktop you would like to use. Pick the one you prefer. 

## Getting data to COSMOS-SENS

Getting data to COSMOS-SENS can **only** be done via sftp via the diode (**cs-diode.lunarc.lu.se**), but this is made easier by mounting a COSMOS-SENS folder to folder on the local machine you are working on. Files can then be copied/moved to this mounted folder which will then become available on COSMOS-SENS.

You will need to install `sshfs` if you haven't already. For Linux machines the `sshfs` package can be installed via your package manager, and for Mac users there is [FUSE-T](https://www.fuse-t.org/). Windows users will have to transfer files using WinSCP with the 2FA option or Filezilla with the interactive login option.

To mount a folder (Linux/macOS) do the following:

1) Login to COSMOS-SENS and make a folder. For example `mkdir FileTransferFolder`

2) Make a folder on your **local** machine that you will mount to. For example `ToCSENS`

3) To mount the folder on COSMOS-SENS to your local machine do:

`sshfs <userID>@cs-diode.lunarc.lu.se:/<pathto>/FileTransferFolder/ ToCSENS/`


So if your `FileTransferFolder` was made in `/home` (on COSMOS) it would look something like:

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

!!! warning inline end "Be smart"

    The point of having data on COSMOS-SENS is that it is protected, so be mindful about which folders you mount. Mounting `/home/user` for example would expose everything you have in your home folder to your local machine, meaning that if someone were to try something malicious on your local computer they would also have access to everything you have on COSMOS-SENS. Everything in your `/home` would also be exposed to any mistakes you make on you local machine.

    It makes far more sense you mount specific folders rather than absolutely everything. Better safe than sorry.

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



!!! note inline end
    **The backup is only for technical issues, it is not intended for file recovery if you do something wrong**. The `/backup` folders are *mirrored* to another drive, so if you delete a file in `/backup` it will also be deleted from the mirror. **We do not take daily/weekly snapshots**.

## Software

Use this checklist, in order, whenever you need a piece of software that isn't already set up for you. Looking for a full analysis pipeline rather than a single tool? Skip ahead to [Nextflow and NF-core](#nextflow-and-nf-core).

### 1. Is it already a module?

There are two places to check: LUNARC's own modules, and a second set of community modules built by end users for tools LUNARC doesn't carry.

#### 1a. LUNARC modules

Software at LUNARC is organised into modules which are loaded when needed, usually when a job is submitted. a rundown of the module system can be seen [here](https://lunarc-documentation.readthedocs.io/en/latest/manual/manual_modules/), but lets load bowtie2 as an example:

First we find the options available using ```module spider```:

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

#### 1b. SCC community modules

Built by the previous coordinator or other end users for tools that aren't in the LUNARC stack (for example `SingSingCell`, `Rustody`, `ImageSmith`). These need one extra step to register the module path before you can see or load them:

```shell
module use /scale/gr01/shared/common/modules
module avail
```

`module avail` lists what's currently registered (you can also just browse `/scale/gr01/shared/common/software/` on disk). Add the `module use` line to your `.bash_profile` (same pattern as the nf-core setup below) so it's always available, rather than needing to run it every session.

!!! warning inline end "Some of these launch an application on load"
    Modules like `SingSingCell` and `ImageSmith` aren't just environment variables — loading them starts a Jupyter session immediately. **Only load them inside a Slurm allocation, never on the front end.**

A typical job script for one of these:

```shell
#! /bin/bash
#SBATCH -n 1
#SBATCH -N 1
#SBATCH -t 24:00:00
#SBATCH -A csens2024-3-2
#SBATCH -J SingSing
#SBATCH -o SingSing.%j.out
#SBATCH -e SingSing.%j.err
module purge
module use /scale/gr01/shared/common/modules
module load SingSingCell/1.1
exit 0
```

Submit with `sbatch`, then check the `.err` file for a browser URL once the job starts (node name, port, and a login token). Open it in a browser **inside your COSMOS-SENS desktop session**, not on your own laptop — the compute node isn't reachable from outside. `scancel <jobid>` when you're done.

### 2. Find or build a container

If there's no module for what you need, the next option is a container. A container packages a piece of software together with everything it depends on (libraries, a specific Python/R version, system tools) into one self-contained, immutable image that runs identically regardless of what's installed on the host. On COSMOS-SENS these are run with **Apptainer** (formerly called Singularity): a single `.sif` file you can copy around and execute directly, with no `module load` or LUNARC install needed.

Building or downloading the image itself always happens *outside* COSMOS-SENS, on an internet-connected Linux machine that matches COSMOS-SENS's **x86_64/amd64** nodes — an image built on an Apple Silicon (arm64) Mac won't run here at all, which is the main reason Mac users are routed to **open COSMOS** below rather than building locally. Once you have a `.sif`, transfer it in via the sshfs/diode method [above](#getting-data-to-cosmos-sens) and run it entirely offline; the actual computational work should happen inside a Slurm allocation, same as any other software, not directly on the front end (see [Running jobs](#running-jobs)).

!!! danger inline end "Keep sensitive data out of the build"
    The machine you build or pull an image on is outside COSMOS-SENS's protected environment. Never copy real sequencing/patient data into an image while building it externally — use test or dummy data, and only point the finished container at your real data once it's running on COSMOS-SENS.

Containers are also the best way to do reproducible research: **never update packages in the middle of a project**: underlying methods can change and shift your results. Freeze the versions you used into a container instead, named accordingly, so you can always come back to the exact same setup, even years later. If you need to upgrade, build a new version and keep the old one as-is.

#### 2a. Ready-made (Biocontainers)

Check whether the tool already exists as a pre-built container before building anything yourself.

First, check the shared image library at `/scale/gr01/shared/common/software/`: someone may have already built or downloaded what you need.

If it's not there, [Biocontainers](https://quay.io/organization/biocontainers) hosts ready-made Singularity/Docker images for most common bioinformatics tools, maintained by the Bioconda/Galaxy community. Since COSMOS-SENS has no internet access, the image has to be pulled *outside* the system and then transferred in:

!!! note inline end
    **Linux**: you can pull directly on your own machine.

    **Mac/Windows**: as above, Apptainer doesn't run natively there either. Ask a colleague with a Linux machine (or Petter) to pull it for you, or use **open COSMOS** (see 2b below).

```shell
apptainer pull bowtie2.sif docker://quay.io/biocontainers/bowtie2:2.5.4--he20e202_3
```

This creates a `.sif` file, which you then copy to COSMOS-SENS using the sshfs/diode method described [above](#getting-data-to-cosmos-sens). Once it's there, run the actual work inside a Slurm job (see [Running jobs](#running-jobs)) rather than on the front end:

```shell
apptainer exec bowtie2.sif bowtie2 <rest of the command here>
```

#### 2b. Build your own

Where you build depends on your machine:

- **Your own Linux machine** (matching COSMOS-SENS's x86_64/amd64 architecture): use `apptainer build`/`apptainer shell` directly, as in the example below.
- **Open COSMOS**: you won't have the permissions to run `apptainer build` directly there. Instead load the `ImageSmith` module inside a Slurm job, the same launcher pattern as `SingSingCell` above (1b) — it gives you a Jupyter Lab terminal with Apptainer already available inside it, from which you can run the same commands as below without needing root.

See [Stefan's tutorial](https://singularity-tutorial.readthedocs.io/) for the full walkthrough of both routes. You don't need to have already tried and failed at this yourself before asking for help — if it looks like more trouble than it's worth, skip straight to [step 3](#3-still-stuck-contact-petter).

##### Minimal example (taken from [Stefan's tutorial](https://singularity-tutorial.readthedocs.io/))

This is an Apptainer definition file for creating a container based on Alpine Linux with Python, JupyterLab, R, and R-Jupyter integration. We use Alpine Linux for this because it produces slim images that take up less space.


Put the following into a file called `Apptainer_example.def`:

```
Bootstrap: docker
From: alpine:latest  # Use the latest Alpine Linux image as the base

%post
    # Update the package index and install essential packages
    apk update

    # Install build tools and libraries required for Python and R
    # You must not have comments after the '\' in the following lines!
    apk add --no-cache bash \
        build-base \
        zeromq-dev \
        libffi-dev \
        musl-dev \
        openblas-dev \
        R \
        R-dev \
        R-doc \
        python3 \
        py3-pip \
        python3-dev \
        py3-setuptools \
        py3-wheel

    # Allow pip to modify system-wide packages
    export PIP_BREAK_SYSTEM_PACKAGES=1

    # Install JupyterLab using pip
    pip3 install --no-cache-dir jupyterlab

    # Install R packages for Jupyter integration
    R -e "install.packages(c('IRkernel', 'IRdisplay'), repos='https://cloud.r-project.org/')"

    # Set up IRkernel to make R available as a Jupyter kernel
    R -e "IRkernel::installspec(user = FALSE)"

%environment
    # Ensure /usr/local/bin is in the PATH so JupyterLab can be found
    export PATH="/usr/local/bin:$PATH"
    # if you want to install more python packages in the sandbox:
    export PIP_BREAK_SYSTEM_PACKAGES=1

%runscript
    # By default, run JupyterLab when the container starts
    jupyter lab --port 9734 --ip=0.0.0.0 --allow-root --no-browser
```

`apptainer build` already runs the whole `%post` section above, so building straight from the `.def` file gets you a working image:

```shell
apptainer build Apptainer_example.sif Apptainer_example.def
```

If you need to debug or try out extra packages first, build a writable **sandbox** instead of a final image:

```shell
apptainer build --sandbox Apptainer_example Apptainer_example.def
```

Shell into it to experiment. A writable sandbox lets you write to the filesystem, but package managers like `apk` still need root to actually install anything — `--writable` alone doesn't grant that, so add `--fakeroot` (this needs to be enabled for your account by an admin; ask Petter if it isn't):

```shell
apptainer shell --fakeroot --writable Apptainer_example
```

From here you can install anything else you need with Alpine's `apk add --no-cache` command, and test it with `jupyter lab ...` directly inside the sandbox. **Once you know what you need, add it to the `%post` section of the `.def` file** rather than leaving it only in the sandbox — that's what makes the image reproducible and rebuildable later, rather than a one-off you can't recreate. Pin package versions where the package manager supports it (e.g. `apk add package=1.2.3`), so rebuilding in a year gives you the same thing.

When you're done experimenting, leave the sandbox before running anything else on the host:

```shell
exit
```

Then build your final image from the **updated `.def` file**, not from the sandbox, so the recipe stays the single source of truth:

```shell
apptainer build Apptainer_example.sif Apptainer_example.def
```

The image can now be copied to COSMOS-SENS. Running it for real means starting it inside a Slurm allocation, the same launcher pattern as `SingSingCell`/`ImageSmith` above:

```shell
#! /bin/bash
#SBATCH -n 1
#SBATCH -N 1
#SBATCH -t 24:00:00
#SBATCH -A csens2024-3-2
#SBATCH -J Jupyter
#SBATCH -o Jupyter.%j.out
#SBATCH -e Jupyter.%j.err
apptainer run Apptainer_example.sif
```

Submit with `sbatch`, then check the `.out`/`.err` files for the node name, port, and token in the printed URL (e.g. `http://snXX:9734/lab?token=...`). Open that URL in a browser **inside your COSMOS-SENS desktop session**, not on your own laptop — the compute node isn't reachable from outside, and the token in the link is your authentication, no separate login needed. `scancel <jobid>` when you're done, to free the node for others.

If you want to swap Alpine for a different base image, check [Stefan's tutorial](https://singularity-tutorial.readthedocs.io/) (or ask Petter) first for any constraints specific to building via ImageSmith on open COSMOS.

### 3. Still stuck? Contact Petter

Installing software as a module is done by LUNARC, and they have asked that all software requests go through the SCC HPC Coordinator (Petter). If you can't find a module or container, or get stuck building one yourself, email Petter with a URL to what is needed.

### 4. Is your container useful to others?

If the container you built is likely to be useful to other people, mention it on the Teams workgroup or ask Petter about it. A broadly useful container can be registered as an SCC community module (see step 1b) instead of everyone keeping their own copy of the `.sif` file. The pattern ([Stefan's module-setup guide](https://singularity-tutorial.readthedocs.io/HowToSetUpAModule/) has the full explanation, though its own example has a couple of small syntax slips) is a small Lua file under `/scale/gr01/shared/common/modules/<name>/<version>.lua` that points at your image in `/scale/gr01/shared/common/software/<name>/<version>/`, for example:

```lua
help([[Runs MyTool via Apptainer]])

local version = "1.0"
local base = "/scale/gr01/shared/common/software/MyTool/" .. version

execute{cmd="apptainer run " .. base .. "/MyTool.sif", modeA={"load"}}

whatis("Name        : MyTool")
whatis("Version     : " .. version)
whatis("Description : Apptainer image for MyTool")

family("images")
```

Then let Petter know so others can `module load` it too.

## Running jobs
When you login into COSMOS-SENS you will be located on the front-end (FE). **This is not a place to run long computations**. The FE is for small interactive jobs, and many people work here. Long memory and processor intensive jobs should be sent to a compute node.

### SLURM
Any jobs such as making and mapping fastq files should be sent to the blades, and for this, LUNARC uses the SLURM job submission system. See below and [here](https://lunarc-documentation.readthedocs.io/en/latest/manual/submitting_jobs/manual_basic_job/) for basic guidelines on how to submit a job.

### Project code

!!! tip inline end ""
    For those registered to our COSMOS-SENS project, the code needed to submit the job is **csens2024-3-2** and you need to specify in the preamble of you script. 
    
Lets say you want to map reads using bowtie, an example header for a job `Run_bowtie.sh` could look like this:

```shell
#! /bin/bash
#SBATCH -n 1
#SBATCH -c 5
#SBATCH -N 1
#SBATCH -t 24:00:00
#SBATCH -A csens2024-3-2
#SBATCH -J BT2
#SBATCH -o BT2.%j.out
#SBATCH -e BT2.%j.err
module purge

module load GCC/10.3.0 Bowtie2/2.4.4

bowtie2 --threads 5 <rest of the command here>
```
In short `-n 1` requests one task, and `-c 5` (`--cpus-per-task`) reserves 5 CPU cores for that task to use. Note this is different from asking for `-n 5`: `-n`/`--ntasks` requests separate tasks (useful for MPI-style programs), not cores for a single multithreaded one — for a program like bowtie2 that just spawns internal threads, `-c`/`--cpus-per-task` is what actually gives it more CPUs to use, and you still need to tell bowtie2 itself to use them (`--threads 5`). The rest: `-N 1` requests one node (do not ask for more), `-t 24:00:00` kills the job after 24 hours, `-A csens2024-3-2` specifies the project code, `-J BT2` is the name of your job, `-o BT2.%j.out` is the outfile of the job (name+job id), `-e BT2.%j.err` is where the error log goes.



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
--outFileNamePrefix $SNIC_TMP/out/MyAnalysis_
cp $SNIC_TMP/out/MyAnalysis_* /scale/gr01/<userid>/pathtofinaldestination/
```

So in this case, the output is directed to the **local** storage at `$SNIC_TMP/out/MyAnanlysis_`, and once the mapping is complete the results are copied to where you want you need them.

## Be nice to your mates

Please be considerate to your co-workers, resources are always limited. Try to use only the resources as you actually need, and importantly think about what your software is able to do. Most of the available software does not benefit from more than 10 cores, and no programs we know of can use two nodes at the same time.


## Nextflow and NF-core

**Note: this document has been updated to show how to run pipelines using Nexflow v24**. The previous document gave instructions for v23.

Nextflow and the [NF-core modules](https://nf-co.re/) are a fantastic resource for bioinformaticians. There are a multitude of pipelines for various NGS applications which go into real depth regarding QC, mapping methods, annotatation, and reporting. After a pipline is run, you also get the version numbers of the software used for easy reporting in papers, but importantly, the pipeline remains the same everytime you run it ensuring results are comparable.

When nf-core piplines are run, docker images are pulled over the internet for use, but as COSMOS-SENS is a secure server with no internt connection, pipelines have to be downloaded and then transfered to COSMOS-SENS. In this case we are also downloading the required singularity images. If you require a pipeline that isn't there, please contact Petter to have it downloaded and installed. It is far better we have them in one place for all to use, rather than duplicating them all over the system.

### Setting up to use nf-core

There are several commonly used piplines located at `/scale/gr01/shared/nf-core`, and to use them you need to add a few lines into a file called `.bash_profile`.

1) Open the file using `gedit ~/.bash_profile`

2) Add copy/paste these lines in at the end and save the file:

```
export NXF_OFFLINE='true'
export SINGULARITY_TMPDIR=$SNIC_TMP
export NXF_SINGULARITY_LIBRARYDIR=/scale/gr01/shared/nf-core/singularity_cache
export NXF_SINGULARITY_CACHEDIR="$NXF_SINGULARITY_LIBRARYDIR"
export SINGULARITY_CACHEDIR="$NXF_SINGULARITY_LIBRARYDIR"
export NXF_HOME=/scale/gr01/shared/nf-core/nextflow_sub/
export NXF_SINGULARITY_HOME_MOUNT=true
```

**You should then logout of COSMOS-SENS and log back in so these changes take effect**. What each line does:

- `NXF_OFFLINE`: stops Nextflow trying to reach the internet to check for pipeline updates, which would otherwise just fail on an offline system like COSMOS-SENS.

- `NXF_SINGULARITY_LIBRARYDIR` / `NXF_SINGULARITY_CACHEDIR` / `SINGULARITY_CACHEDIR`: tell Nextflow where the pre-downloaded singularity images live (`/scale/gr01/shared/nf-core/singularity_cache`), so it uses those instead of trying to fetch images itself.

- `SINGULARITY_TMPDIR`: points temporary build/pull files at node-local scratch (`$SNIC_TMP`) instead of the network filesystem.
- `NXF_HOME`: sets Nextflow's own home directory to the central copy on shared (`/scale/gr01/shared/nf-core/nextflow_sub/`), so each user doesn't need their own.

- `NXF_SINGULARITY_HOME_MOUNT`: mounts your real `$HOME` into the container. Nextflow doesn't do this by default; turn it on if a pipeline step expects to find something there.

!!! note inline end
    `$SNIC_TMP` [only exists inside an active Slurm allocation](#run-jobs-using-the-local-disk), not on the front end. The `SINGULARITY_TMPDIR` line above only resolves to the right value if it gets (re-)read once you're inside your job; if in doubt, set it explicitly in the job script itself rather than relying solely on `.bash_profile`.

When running a job, use the config file located here:

`/scale/gr01/shared/nf-core/cosmos_sens_grp1_NFv24.config`


This file contains the project ID and automatically sets the RAM and core resources so you don't have to.


### An example using RNAseq

A sample sheet (`samplesheet.csv`) would look something like:

```shell
sample,fastq_1,fastq_2,strandedness
Sample1,Sample1_R1.fastq.gz,Sample1_R2.fastq.gz,auto
Sample2,Sample2_R1.fastq.gz,Sample2_R2.fastq.gz,auto
Sample3,Sample3_R1.fastq.gz,Sample3_R2.fastq.gz,auto
```

and a script `NFC_RNAseq.sh` running the basic rnaseq pipline would look like this:

```shell
module load Nextflow/24.10.0

nextflow run /scale/gr01/shared/nf-core/nf-core-rnaseq_3.17.0/3_17_0/main.nf \
-profile singularity \
--input samplesheet.csv \
-c /scale/gr01/shared/nf-core/cosmos_sens_grp1_NFv24.config \
--genome GRCm38 \
--igenomes_base  /scale/references/AWS-iGenomes/ \
--outdir /home/<userid>/RNAseq_project/NFC_out
```


This script, the fastq files, and the samplesheet are all in the same folder. The pipline is launched using:

`sh NFC_RNAseq.sh`

### Cut&Run

```shell
module load Nextflow/24.10.0

nextflow run /scale/gr01/shared/nf-core/nf-core-cutandrun_3.2.2/3_2_2/main.nf \
--input samplesheet.csv \
-profile singularity \
-c /scale/gr01/shared/nf-core/cosmos_sens_grp1_NFv24.config \
--fasta /scale/references/AWS-iGenomes/Homo_sapiens/Ensembl/GRCh37/Sequence/WholeGenomeFasta/genome.fa \
--gtf /scale/references/AWS-iGenomes/Homo_sapiens/Ensembl/GRCh37/Annotation/Genes \
--spikein_bowtie2 /scale/references/AWS-iGenomes/Escherichia_coli_K_12_MG1655/NCBI/2001-10-15/Sequence/Bowtie2Index \
--spikein_fasta /scale/references/AWS-iGenomes/Escherichia_coli_K_12_MG1655/NCBI/2001-10-15/Sequence/WholeGenomeFasta/genome.fa \
--outdir /home/<userid>/<path_to_out>/CaR_Out
```

### Fusion detection (rnafusion)

nf-core/rnafusion **4.1.0** is installed at `/scale/gr01/shared/nf-core/nf-core-rnafusion_4.1.0/`. It works a little differently from the other pipelines here, so read this whole section before you run it.

!!! warning inline end "Different Nextflow version"
    rnafusion needs **`module load Nextflow/25.04.7`**, not the `Nextflow/24.10.0` used in the other examples. On 24.10.x the pipeline fails to compile with `Variable tools already defined`.

#### Start with the ready-made test

`/scale/gr01/shared/nf-core/nf-core-rnafusion_4.1.0/test/` contains small test data, samplesheets, two job scripts (`run_human.sh` and `run_mouse.sh`) and a `README.txt`. The scripts set the `NXF_*` variables themselves, so you don't need the `.bash_profile` setup to run them. Copy one script to an empty folder and submit it:

```shell
mkdir ~/rnafusion_test && cd ~/rnafusion_test
cp /scale/gr01/shared/nf-core/nf-core-rnafusion_4.1.0/test/run_human.sh .
sbatch run_human.sh
```

The test takes a few minutes once the job starts. For human you should get 15 fusions from both Arriba and STAR-Fusion (for example FGFR3--TACC3). For mouse you should get none, because the test data is from normal liver. Run the test first, before your own data. It is also a good template: copy one of its samplesheets, put in your own fastq paths, and change `--input`.

#### Only Arriba and STAR-Fusion

Only **Arriba** and **STAR-Fusion** have references installed, so you must always pass `--tools arriba,starfusion`. The other tools in the pipeline (FusionCatcher, fusion-report, FusionInspector, CTAT-splicing, StringTie, Salmon) have no references and will fail.

#### Human or mouse

- **Human** (GRCh38, GENCODE v46): `--genomes_base /scale/gr01/shared/nf-core/references/rnafusion_4.1.0`
- **Mouse** (GRCm39, GENCODE M31): `-params-file /scale/gr01/shared/nf-core/nf-core-rnafusion_4.1.0/GRCm39.yaml` **instead of** `--genomes_base`. nf-core/rnafusion doesn't officially support mouse. It works here because that file gives every reference explicitly.

#### Sample sheet

The columns are the same as for RNAseq (`sample,fastq_1,fastq_2,strandedness`), but strandedness must be `forward` or `reverse`. **`auto` is not accepted**, unlike in rnaseq.

```shell
sample,fastq_1,fastq_2,strandedness
Sample1,Sample1_R1.fastq.gz,Sample1_R2.fastq.gz,reverse
Sample2,Sample2_R1.fastq.gz,Sample2_R2.fastq.gz,reverse
```

Check your library prep; most stranded Illumina kits (e.g. TruSeq Stranded) are reverse.

#### Running it

Run rnafusion inside `sbatch` on a **single node** with the `_local` config, the same pattern as [If you have *many* small fastq files](#if-you-have-many-small-fastq-files) below. A job script `rnafusion.sh` for human looks like this:

```shell
#! /bin/bash
#SBATCH -n 48
#SBATCH -N 1
#SBATCH -t 48:00:00
#SBATCH -A csens2024-3-2
#SBATCH -J rnafusion
#SBATCH -o rnafusion.%j.out
#SBATCH -e rnafusion.%j.err

module load Nextflow/25.04.7

nextflow run /scale/gr01/shared/nf-core/nf-core-rnafusion_4.1.0/4_1_0/main.nf \
-profile singularity \
--input samplesheet.csv \
-c /scale/gr01/shared/nf-core/cosmos_sens_grp1_NFv24_local.config \
-c /scale/gr01/shared/nf-core/nf-core-rnafusion_4.1.0/cosmos_limits.config \
--genomes_base /scale/gr01/shared/nf-core/references/rnafusion_4.1.0 \
--tools arriba,starfusion \
--outdir /home/<userid>/<project>/rnafusion_out
```

For mouse, replace the `--genomes_base` line with:

```shell
-params-file /scale/gr01/shared/nf-core/nf-core-rnafusion_4.1.0/GRCm39.yaml \
```

Submit it with `sbatch rnafusion.sh`. Unlike the test scripts, this script gets the `NXF_*` variables from your `.bash_profile` (see [Setting up to use nf-core](#setting-up-to-use-nf-core)).

!!! danger inline end "Don't use the distributed config inside sbatch"
    The normal `cosmos_sens_grp1_NFv24.config` sends each step out as its own Slurm job. Nextflow can't submit jobs from inside a job on COSMOS-SENS, so with that config inside `sbatch` it fails with `sbatch: error: Batch job submission failed: Invalid mcs_label specified`. Running rnafusion the distributed way from the front end (as in the RNAseq example) **hasn't been tested yet**, so stick to the single-node job above.

**Always add the extra `-c .../cosmos_limits.config`**, on top of the cluster config. It does two things:

- It caps each task at 48 CPUs, 250 GB and 48 h. Without it, a retried STAR task asks for 400 GB of RAM (200 GB × the attempt number). That is more than a standard node has (256 GB), so the job never starts.
- It switches off the internet URLs in the pipeline defaults. Without it, the pipeline fails or hangs for about 15 minutes at startup, because COSMOS-SENS has no internet.

Run each analysis from **its own folder**. Two runs launched from the same folder share the `.nextflow/` folder and overwrite each other's `.nextflow.log`.

#### Results

The fusion calls end up in:

- `<outdir>/arriba/*.arriba.fusions.tsv`
- `<outdir>/starfusion/*.starfusion.abridged.tsv`

You will see this warning at startup. **It is harmless**, because it comes from the cluster config:

```
The following invalid input values have been detected: --igenomes_base
```

### Sample sheets
The contents of the sample sheet will vary depending on which pipline you are using, so be sure to read the manual.

### Remove the work folder
nf-core pipelines will make a `work` folder if the tmp folder on the node isn't used. It holds every task's intermediate files, and it's what `-resume` uses to skip work that's already been done on a re-run. So it's only safe to delete once you're confident you won't need to resume that particular run again, for example once it's finished successfully and you've copied out the results you need — deleting it means a future `-resume` starts from scratch instead of picking up where you left off. It is normally huge and, once you no longer need it for resuming, contains nothing of further interest.

### Available pipelines
The available pipelines are located in `/scale/gr01/shared/nf-core`. If the one you need is missing, contact Petter for it to be downloaded.

### If you have *many* small fastq files
Some well-based methods generate *thousands* of small fastq files which do not play nice with the queuing system on CSENS when using an nf-core pipeline. The jobs are fired-off to the nodes so quickly that SLURM cannot keep up, but importantly, setting up the task actually takes longer than the compute subsequently done.

If you are in this situation, you need to run the pipeline **locally** on a **single** node instead. This means all the pipeline steps will be carried out on single server, and not distributed over multiple nodes which is what normally happens. Do this this you have to used a different config file, namely `cosmos_sens_grp1_NFv24_local.config`. So, to use RNAseq as an example you would submit a job like so:


```shell
#! /bin/bash
#SBATCH -n 48
#SBATCH -N 1
#SBATCH -t 24:00:00
#SBATCH -A csens2024-3-2
#SBATCH -J nfc_rnaseq
#SBATCH -o nfc_rnaseq.%j.out
#SBATCH -e nfc_rnaseq.%j.err

module load Nextflow/24.10.0

nextflow run /scale/gr01/shared/nf-core/nf-core-rnaseq_3.17.0/3_17_0/main.nf \
-profile singularity \
--input samplesheet.csv \
-c /scale/gr01/shared/nf-core/cosmos_sens_grp1_NFv24_local.config \
--genome GRCm38 \
--igenomes_base /references/AWS-iGenomes/ \
--outdir /home/<userid>/RNAseq_project/NFC_out

```

This will run the entire pipeline on a single node.

## IGV
Integrative Genomics Viewer (IGV) is a nice tool to visualise BAM/bigwig files. This is available to use on COSMOS-SENS, but it should not be used on the front-end as it will slow the system down for others. Instead you should login to a node interactively and use it from there.

Open a terminal and do the following. It will take a moment to find a free node. `-t 60` means a 60 min session

```shell
interactive -N 1 --ntasks-per-node=4 -t 60
```

When you have logged into a node, your prompt will change to something like:

```
<userid>@snXX
```
where snXX will denote which node you have logged into.

Load the module:
```shell
module load IGV/2.18.4-Java-17
```
You will see this:

```
WARNING!! Not suitable to be exectuted on COSMOS-SENS front-ends.
```
**You can ignore this**. You are logged into a node because you were smart enough to read these instructions first. Well done you.

Then invoke IGV:
```shell
igv.sh
```

A normal IGV session will open which you use as normal.
