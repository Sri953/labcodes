# Using ROSALIND HPC Cluster

This document contains key information on interacting with Rosalind cluster:

1. How to gain access
2. How to connect and disconnect
3. How to use the software modules
4. How to create your own modules 
5. How to run functions
6. How to transfer files between local and remote computer
7. How to run batch jobs

## 1) How to gain access
Fursham (k1768990@kcl.ac.uk) and Eugene (k1330282@kcl.ac.uk) have been granted access into the cluster.  

Local compuer connects to Rosalind by ssh, therefore we would need to create a public key first and inform rosalind-support@kcl.ac.uk. 
To create this, refer to the instructions below or to this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2)
```bash
ssh-keygen -t rsa
```

You would then need to copy the public key and send to rosalind-support@kcl.ac.uk. 
This can be found at the same directory in which you save the key, in a file with a `.pub` extension. 
For example, if you save it in `/home/demo/.ssh/id_rsa`, the public key will be in `/home/demo/.ssh/id_rsa.pub`.

## 2) How to connect and disconnect
Once the support team have confirmed that they public key have been added, you can now connect to Rosalind
using this command:
```bash
ssh k1234567@login.rosalind.kcl.ac.uk
```

Disconnect from the cluster using `exit` command

## 3) How to use the software modules
Software in SLURM are dynamically maintained by Environment Modules. 
This [tutorial](https://rosalind.kcl.ac.uk/hpc/modules/) explains how to list, load and unload pre-compiled modules on Rosalind.  

Below are some commands used to query the cluster:
```bash
# list the available modules on cluster
module avail

# loading java
module load apps/openjdk/12.0.1(default)


# list currently loaded modules
module list

# unloading java
module unload apps/openjdk/12.0.1(default)
```

Rosalind uses a UNIX filesystem, so navigating around the directories will be familiar.  
There are several home directories to our disposal:

1. personal directory (/users/<username>) 
2. personal scratch directory (/scratch/users/<username>)
2. shared scratch directory (/scratch/groups/cdnbc) 

While the first is our personal home directory, the second is for job I/O

## 4) How to create your own modules 
Rosalind currently contain many software that we routinely use, but we may also install additional ones.
The code below contain example steps to install an application to our personal bin directory.

```bash
# installing nextflow

## load java first
module load apps/openjdk/12.0.1

# Make sure that Java v8+ is installed:
java -version

# Install latest version of Nextflow
export NXF_VER=21.03.0-edge
curl -fsSL get.nextflow.io | bash

# Add Nextflow binary to your user's PATH:
mv nextflow ~/users/<username>/bin/

```
From my reading, it seems that you need to include a modulefile to load the module into the environment.
However, moving the app to bin overwrites this dependency for a modulefile.  

If there is a need to use modulefile, refer to this [tutorial](https://cloud.google.com/solutions/installing-apps-slurm-clusters-compute-engine)

## 5) How to run functions
Once modules have been loaded into the environment, it can be run
as it normally would on a local computer.  

I have installed nextflow, a rna-seq QC and alignment pipeline, in my user directory.
This software have successfully executed its testing and is now fully functional.  

To test this pipeline, run:
```bash
module load apps/openjdk/12.0.1  # the only dependency it needs

# run test
nextflow run nf-core/rnaseq -profile test,singularity

```

This pipeline is easy to use. See this [ReadMe](https://emckclac-my.sharepoint.com/:u:/g/personal/k1768990_kcl_ac_uk/EXJV7KtDMEhHkB9zBNasuSYB_6xX-AJ0sWxPg8KzCpwoTg?e=EHXhmx) for instructions.  

## 6) How to transfer files between local and remote computer
To transfer files to/from Rosalind, it is recomended to use `rsync`. Easiest way to download
this software is:

```bash
sudo apt install rsync grsync
```

Then, you may run the following command to upload files:
```bash
rsync -av --progress /path/to/localfile k12345678@login.rosalind.kcl.ac.uk:/path/to/destination
```
To download files from Rosalind, run:
```bash
rsync -av --progress 12345678@login.rosalind.kcl.ac.uk:/path/to/file /path/to/localdirectory
```

## 7) How to run batch jobs
To run batch jobs, which allows you to run multi-threaded processes, you would need to create a shell script 
that contain the following set of lines:

```bash
#!/bin/bash -l
#SBATCH	--job-name=testjob
#SBATCH --output=/scratch/users/%u/%j.out
#SBATCH --time=0-2:00
#SBATCH	-n 30

nextflow run nf-core/rnaseq --max_cpus 25 --outdir /scratch/users/%u/sample/ -profile test,singularity
```

The `--output` parameter specifies the path to the output file where the stdout should print to. 
This is typically directed to the `/scratch` partition. `%u` will print out the username running the job and `%j` will print out the job id. 

`--time` specifies the allocated time for the job (hh:mm). THe maximum run time allowed is 7 days.  

`-n` specifies the number of CPU cores to activate for the job. As of 6th March 2021, I checked that we have a maximum of 24 CPU cores allocated.  

The above script can be run from terminal as such:
```bash
sbatch -p shared <your_script>.sh
```

And the progress of the job can be previewed as such:
```bash
squeue -u <username>
```



