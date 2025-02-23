# Conda On the Cluster

## Summary
This document provides a comprehensive technical overview of Conda, focusing on its installation, configuration, package management, and environment management, particularly in an HPC (High-Performance Computing) cluster environment.

---

## Prerequisites
  * Basic Linux command-line concepts and cluster-specific considerations for using Conda
  * Unix/Linux command line is case sensitive
  * The dollar sign (`$`) indicates the beginning of a command
  * The hash/pound sign (`#`) indicates end of a command the start of a comment
  * the notation `<SOME_VARIABLE>` refers to variables and file names that need to be specified by the user. The symbols `<` and `>` need to be excluded

***Remember to***:
  - Get an interactive session on the cluster, before  
  - Loading Miniconda3, and  
  - Remember to replace `conda activate` with **`source activate`**  
  - Only create one conda environment for each version of each piece of software, if possible, and  
  - You are responsible for managing your own conda environments (environments and packages are stored in `~/.conda` 

---

### Quick Start	

*Tip: It is recommended to create a new environment for any new project or workflow*
- Verify conda install/config/solver/envs/version/etc. etc.  
  `conda info`  

- Get info for any package with the `--info` flag  
  `conda search <package> --info`  

- Get help for any command with the `--help` flag  
  `conda create --help`  

- Create a new environment (*name your environment descriptively*)  
  `conda create -n <package>`  

- Activate environment - we substitute `source` for `conda` in our `activate` command (*do this before installing additional packages*)  
  `source activate <package>`  

- Install packages in activated environment  
  `conda install <package> --yes`  

- Examine conda configuration  
  `conda config --show`	

- Remove all unused files  
  `conda clean --all`  

- Deactivate current environment  
  `conda deactivate`  

---

### Channels and Packages

*Tip: Package dependencies and platform specifics are automatically resolved when using conda*  

- List installed packages within active environment  
  `conda list`  

- List installed packages with source info  
  `conda list <package> --show-channel-urls`  

- Update all packages  
  `conda update --all`  

- Install a package from specific channel  
  `conda install -c <channel> <package_1> <package_2>`  

- Install version *3.1.4* of package  
  `conda install <package>=3.1.4`  

- Install a package from specific channel, using `::`  
  `conda install <channel>::<package>`

- Install package with AND logic  
  `conda install “<package>>2.5,<3.2”`  

- Install package with OR logic  
  `conda install “<package> [version=’2.5|3.2’]”`  

- Uninstall package  
  `conda uninstall <package>`  

- View channel sources  
  `conda config --show-sources`  

- Add channel  
  `conda config --add channels <channel>`  

- Set default channel for pkg fetching (targets first channel in channel sources)   
  `conda config --set channel_priority strict`  

---

### Environments and Environment Management

*Tip: List environments at the beginning of your session. Environments with an asterisk are active*  

- List all environments and locations  
  `conda env list`  

- List all packages + source channels  
  `conda list -n <environment> --show-channel-urls`  

- Install packages in environment  
  `conda install -n <environment> <package_1> <package_2>`  

- Remove package from environment  
  `conda uninstall <package> -n <environment>`  

- Update all packages in environment  
  `conda update --all -n <environment>`  
  
*Tip: Specifying the environment name confines conda commands to that environment*  

- Create environment with Python version *3.10*  
  `conda create -n <environment> python=3.10`  

- Clone environment  
  `conda create --clone <source_environment> -n <target_environment>`  

- Rename environment  
  `conda rename -n <old_environment> <new_environment>`  

- Delete environment by name  
  `conda remove -n <environment> --all`  

- List revisions made to environment  
  `conda list -n <environment> --revisions`  

- Restore environment to a revision  
  `conda install -n <environment> --revision <number>`  

- Uninstall package from specific channel  
  `conda remove -n <environment> -c <channel> <package>`  
  
### Exporting Environments

*Recommendation: Name the export file “environment.” Environment name will be preserved*  

- Cross-platform compatible  
  `conda env export --from-history><environment>.yml`  

- Platform + package specific  
  `conda env export <environment>><environment>.yml`  

- Platform + package + channel specific  
  `conda list --explicit><environment>.txt`  
  
### Importing Environments

*Tip: When importing an environment, conda resolves platform and package specifics*  

- From a `.yml` file  
  `conda env create -n <environment> --file <environment>.yml`  

- From a `.txt` file  
  `conda create -n <environment> --file <environment>.txt`  

---

### Use Cases

#### TBProfiler v6.2.0
```bash
# Launch an interactive job on the cluster/load miniconda3/create conda env with relevant channels

[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n tbprofiler-6.2.0 -c bioconda -c conda-forge

  . . . many lines omitted . . .

  environment location: /path/to/home/user/.conda/envs/tbprofiler-6.2.0

  . . . many lines omitted . . .

#
# To activate this environment, use
#
#     $ conda activate tbprofiler-6.2.0 (this command won’t work in our env, so we’ll substitute the one below)
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate tbprofiler-6.2.0

# Note the context change

(tbprofiler-6.2.0) [user@cpu_node ~]$ conda install -c bioconda tb-profiler=6.2.0

  . . . many lines omitted . . .

For Linux 64, Open MPI is built with CUDA awareness but this support is disabled by default.
To enable it, please set the environment variable OMPI_MCA_opal_cuda_support=true before
launching your MPI processes. Equivalently, you can set the MCA parameter in the command line:
mpiexec --mca opal_cuda_support 1 ...

In addition, the UCX support is also built but disabled by default.
To enable it, first install UCX (conda install -c conda-forge ucx). Then, set the environment
variables OMPI_MCA_pml="ucx" OMPI_MCA_osc="ucx" before launching your MPI processes.
Equivalently, you can set the MCA parameters in the command line:
mpiexec --mca pml ucx --mca osc ucx ...
Note that you might also need to set UCX_MEMTYPE_CACHE=n for CUDA awareness via UCX.
Please consult UCX's documentation for detail.

(tbprofiler-6.2.0) [user@cpu_node ~]$ tb-profiler --version
tb-profiler version 6.2.0

(tbprofiler-6.2.0) [user@cpu_node ~]$ tb-profiler
Usage: tb-profiler [--version] [--logging {DEBUG,INFO,WARNING,ERROR,CRITICAL}] {profile,lineage,spoligotype,collate,reformat,create_db,load_library,update_tbdb,batch,list_db,version} ...

tb-profiler: a tool to predict drug resistance and infer lineages

(tbprofiler-6.2.0) [user@cpu_node ~]$ conda deactivate
[user@cpu_node ~]$
```

---

#### RSeQC v5.0.3
```bash
[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n rseqc-test

## Package Plan ##

. . . many lines omitted . . .

  environment location: /path/to/home/user/.conda/envs/rseqc-test

. . . many lines omitted . . .

#
# To activate this environment, use
#
#     $ conda activate rseqc-test (this command won’t work in our env, so we’ll substitute the one below)
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate rseqc-test

# Note the context change

(rseqc-test) [user@cpu_node ~]$ pip3 install RSeQC
Defaulting to user installation because normal site-packages is not writeable
Collecting RseQC
  Downloading RseQC-5.0.3-py3-none-any.whl.metadata (834 bytes)

Successfully installed RseQC-5.0.3 . . .

. . . many lines omitted . . .

# The library of python scripts included in this package is quite extensive ( https://rseqc.sourceforge.net/#usage-information  ) - I tested a random sample - and all functioned as expected:

(rseqc-test) [user@cpu_node ~]$ bam2fq.py --version
bam2fq.py 5.0.2
(rseqc-test) [user@cpu_node ~]$ bam2wig.py --version
bam2wig.py 5.0.2
(rseqc-test) [user@cpu_node ~]$ bam_stat.py --version
bam_stat.py 5.0.2
(rseqc-test) [user@cpu_node ~]$ infer_experiment.py --version
infer_experiment.py 5.0.2
(rseqc-test) [user@cpu_node ~]$ mismatch_profile.py --version
mismatch_profile.py 5.0.2
(rseqc-test) [user@cpu_node ~]$ sc_bamStat.py --version
sc_bamStat.py 5.0.2
(rseqc-test) [user@cpu_node ~]$ tin.py --version
tin.py 5.0.2
```

---

#### vamb 4.1.3
```bash
user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n vamb-test
Retrieving notices: ...working... done

## Package Plan ##

  environment location: /path/to/home/user/.conda/envs/vamb-test

#
# To activate this environment, use
#
#     $ conda activate vamb-test
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate vamb-test
(vamb-test) [user@cpu_node ~]$ pip install vamb
Collecting vamb

(vamb-test) [user@cpu_node ~]$ vamb -h
usage: vamb outdir tnf_input rpkm_input [options]

Vamb: Variational autoencoders for metagenomic binning.

    Version: 3.0.9

    Default use, good for most datasets:
    vamb --outdir out --fasta my_contigs.fna --bamfiles *.bam

    For advanced use and extensions of Vamb, check documentation of the package
    at https://github.com/RasmussenLab/vamb.
```

---

#### ResMiCo 1.2.2
```bash
# I had several build errors when creating this environment, but these directions below worked:
https://github.com/leylabmpi/ResMiCo?tab=readme-ov-file#from-source-using-pip

[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3

[user@cpu_node ~]$ git clone https://github.com/leylabmpi/resmico
Cloning into 'resmico'...

[user@cpu_node ~]$ conda env create -n resmico -f resmico/environment.yml
done

#
# To activate this environment, use
#
#     $ conda activate resmico
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate resmico
(resmico) [user@cpu_node ~]$ pip install resmico
Collecting resmico

. . . many lines omitted . . .

Successfully installed cmake-3.29.2 protobuf-3.20.0 resmico-1.2.2
(resmico) [user@cpu_node ~]$ conda install pytest pytest-console-scripts

. . . many lines omitted . . .

(resmico) [user@cpu_node resmico]$ resmico -h
usage: resmico [-h] {bam2feat,train,evaluate,filter} ...

ResMiCo: mis-assembly detection with deep learning

    Example: resmico train -h

    For general info, see https://github.com/leylabmpi/resmico/

(resmico) [user@cpu_node resmico]$ resmico train -h
usage: resmico train [-h] [--early-stop] [--net-type NET_TYPE] [--num-blocks NUM_BLOCKS] [--filters FILTERS] [--ker-size KER_SIZE] [--n-hid N_HID] [--n-conv N_CONV] [--n-fc N_FC]

Train a new model using resmico

optional arguments:
  -h, --help            show this help message and exit

Training-specific arguments:

. . . many lines omitted . . .

  --stats-file STATS_FILE
                        File containing the feature means/stdevs of the training set.
                        Set to an empty string when training on a new data set.
                        (default: /path/to/home/user/.conda/envs/resmico/lib/python3.9/site-packages/resmico/model/stats_cov.json)


(resmico) [user@cpu_node resmico]$ pytest -s --script-launch-mode=subprocess ./resmico/tests/
=============================================================================== test session starts ================================================================================
platform linux -- Python 3.9.15, pytest-7.4.0, pluggy-1.5.0
rootdir: /path/to/home/user/resmico/resmico
plugins: console-scripts-1.4.1
collected 10 items / 2 errors

(resmico) [user@cpu_node resmico]$ resmico train --gpu-eval-mem-gb 128
2024-04-29 08:40:49,612 - Building Tensorflow model...

. . . many lines omitted . . .

2024-04-29 08:42:02,400 - Validation scores after 50 epochs: aucPR: -0.0 - recall1: 0.0 - recall0: 0.9166666666666666 - mean: 0.08040689180294673
2024-04-29 08:42:02,400 - Validation done in 0s
2024-04-29 08:42:02,400 - Saving trained model...
2024-04-29 08:42:02,750 - Latest model written to: ./resmico_-0.0_e50.h5
```

---

#### MetaPhlAn 4.1.0
```bash
[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n metaphlan-test

## Package Plan ##

  environment location: /path/to/home/user/.conda/envs/metaphlan-test

#
# To activate this environment, use
#
#     $ conda activate metaphlan-test
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate metaphlan-test
(metaphlan-test) [user@cpu_node ~]$ pip install MetaPhlAn

Successfully installed MetaPhlAn-4.1.0 . . . many packages omitted . . .

(metaphlan-test) [user@cpu_node ~]$ metaphlan
usage: metaphlan --input_type {fastq,fasta,bowtie2out,sam} [--force] [--bowtie2db METAPHLAN_BOWTIE2_DB] [-x INDEX] [--bt2_ps BowTie2 presets] [--bowtie2_exe BOWTIE2_EXE]
                 
(metaphlan-test) [user@cpu_node ~]$ metaphlan --version
MetaPhlAn version 4.1.0 (23 Aug 2023)
```

---

#### humann v3.9
```bash
[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n humann-test

## Package Plan ##

  environment location: /path/to/home/user/.conda/envs/humann-test

#
# To activate this environment, use
#
#     $ conda activate humann-test
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate humann-test
(humann-test) [user@cpu_node ~]$ pip install humann
Defaulting to user installation because normal site-packages is not writeable
Collecting humann
Successfully installed humann-3.9

(humann-test) [user@cpu_node ~]$ humann
usage: humann [-h] -i <input.fastq> -o <output> [--threads <1>] [--version] [-r] [--bypass-nucleotide-index] [--bypass-nucleotide-search] [--bypass-prescreen]
              
(humann-test) [user@cpu_node ~]$ humann --version
humann v3.9
```

---

#### fmap v0.1
```bash
[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n fmap-test

## Package Plan ##

  environment location: /path/to/home/user/.conda/envs/fmap-test

#
# To activate this environment, use
#
#     $ conda activate fmap-test
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate fmap-test

(fmap-test) [user@cpu_node ~]$ pip install -U fmap
Defaulting to user installation because normal site-packages is not writeable
Collecting fmap
  Downloading fmap-0.1-py2.py3-none-any.whl.metadata (6.7 kB)
Requirement already satisfied: six in ./.local/lib/python3.12/site-packages (from fmap) (1.16.0)
Downloading fmap-0.1-py2.py3-none-any.whl (8.5 kB)
Installing collected packages: fmap
Successfully installed fmap-0.1

(fmap-test) [user@cpu_node ~]$ fmap -h
usage: fmap [-h] [-p] [-v] [-d] [-l] [-b] [-z MAX_DEPTH] [-x PATTERN] [-r DIR] CMD [PATTERN ...]

Recusively apply a command to a filesystem tree.
```

---

#### DRAM-bio v1.5.0
```bash
[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n DRAM-bio

## Package Plan ##

  environment location: /path/to/home/user/.conda/envs/DRAM-bio

      #
# To activate this environment, use
#
#     $ conda activate DRAM-bio
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate DRAM-bio
(DRAM-bio) [user@cpu_node ~]$ pip install DRAM-bio
Defaulting to user installation because normal site-packages is not writeable
Collecting DRAM-bio
Successfully installed Cython-3.0.10 DRAM-bio-1.5.0 . . . many packages omitted . . .
(DRAM-bio) [user@cpu_node ~]$ DRAM.py -h
usage: DRAM.py [-h] {annotate,annotate_genes,distill,strainer,neighborhoods,merge_annotations} ...
```

---

#### MACS2 v2.2.9.1
```bash
[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n MACS2-test python=3.10

. . . many lines omitted . . .

  environment location: /path/to/home/user/.conda/envs/MACS2-test

. . . many lines omitted . . .

#
# To activate this environment, use
#
#     $ conda activate MACS2-test (this command won’t work in our env, so we’ll substitute the one below)
#
# To deactivate an active environment, use
#
#     $ conda deactivate
#

[user@cpu_node ~]$ source activate MACS2-test

	Note the context change

(MACS2-test) [user@cpu_node ~]$ pip install MACS2
Collecting MACS2

. . . many lines omitted . . .

Successfully installed Cython-0.29.37 MACS2-2.2.9.1 . . .

(MACS2-test) [user@cpu_node ~]$ macs2 --version
macs2 2.2.9.1
(MACS2-test) [user@cpu_node ~]$ macs2 -h
usage: macs2 [-h] [--version] {callpeak,bdgpeakcall,bdgbroadcall,bdgcmp,bdgopt,cmbreps,bdgdiff,filterdup,predictd,pileup,randsample,refinepeak} ...

macs2 -- Model-based Analysis for ChIP-Sequencing
```

---

#### magCalEM v1.3.6
```bash
[user@hgpu_node ~]$ wget https://www.mrc-lmb.cam.ac.uk/crusso/magCalEM/Tutorial_data.tar.gz
[user@hgpu_node ~]$ gunzip Tutorial_data.tar.gz; tar xvf Tutorial_data.tar 

# Process Begin
[user@hgpu_node ~]$ ml miniconda3/24.1.2-ioj76md
[user@hgpu_node ~]$ conda create -n magCalEM python=3.9
. . . many lines omitted . . .
environment location: /path/to/home/user/.conda/envs/magCalEM
#
# To activate this environment, use
#
#     $ conda activate magCalEM (this command won’t work in our env, so we’ll substitute the one below)
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@hgpu_node ~]$ source activate magCalEM
. . . many lines omitted . . .

Note the context change

(magCalEM) [user@hgpu_node ~]$ pip install magCalEM
. . . many lines omitted . . .

(magCalEM) [user@hgpu_node ~]$ python -m magCalibration.magCalib

![image](https://github.com/user-attachments/assets/b1104296-9d6c-4c51-b046-91dcc7d21596)

![image](https://github.com/user-attachments/assets/e0319c7c-5c7f-4ab2-a28f-792cab112049)

![image](https://github.com/user-attachments/assets/c236bebf-a457-48ee-bced-8cd32e819f5e)

I replaced /full/path/to/data in the “Data Directory” text-box with the path to my Tutorial Data from earlier, appending the Foil_images to be analyzed - i.e. - /path/to/home/user/user_guide_external/Foil_images - and was able to reproduce the steps from the user-guide (attached):

![image](https://github.com/user-attachments/assets/7b1c8ece-9e69-476d-966e-453b5ec8d938)
```

---

#### Cogent NGS Analysis Pipeline v2.0.1
```bash
# Get an interactive shell on BigSky
[user@scheduler_node ~]$ srun -c 48 --mem 128G --pty bash

# Load the miniconda3 module
[user@cpu_node ~]$ ml gcc/11.4.0-zp3cjbh && ml miniconda3/24.5.0-i3ittdj

# Begin installation per vendor documentation (STEP #4) – first, make the vendor requested changes to your ~/.bash_profile (I added the full Spack path to the conda binary (as the instructions assume miniconda3 is installed in your /home directory)
[user@cpu_node ~]$ vi .bash_profile
. . .
export=PATH="/path/to/apps/user/spack-0.21.2/software/spack/linux-centos7-x86_64_v3/gcc-11.4.0/miniconda3-24.5.0-i3ittdjgin2e4m3oj4rij7tzygxpxdwa/bin:$PATH"
. . .
[user@cpu_node ~]$ source .bash_profile
      
      # Begin carving out space for the installation
[user@cpu_node ~]$ cd dev/ && mkdir cogent && cd cogent/

# The only mechanism that worked for me to get the software package was to fill-out the online form, and download the package via web browser, stage it on /path/to/Scratch/ - then copy it over – reach out and I can stage the .zip file somewhere for you
[user@cpu_node cogent]$ cp /path/to/Scratch/bell/cogent/Cogent_NGS_Analysis_Pipeline_v2.0.1.zip .

# Unzip Cogent and change the name to “CogentAP”
[user@cpu_node cogent]$ unzip Cogent_NGS_Analysis_Pipeline_v2.0.1.zip && mv Cogent_NGS_Analysis_Pipeline_v2.0.1 CogentAP

# Change into the new directory and install CogentAP using the provided setup script
[user@cpu_node cogent]$ cd CogentAP/ && bash CogentAP_setup.sh install
Installation of CogentAP in progress...
conda 24.5.0
Success: conda found
Creating a new environment in conda...
Channels:
- bioconda
- r
- conda-forge
- defaults
Platform: linux-64
Collecting package metadata (repodata.json): done
Solving environment: done

## Package Plan ##

environment location: /path/to/home/user/dev/cogent/CogentAP/CogentAP_tools

Successfully installed CogentAP pipeline and dependencies.
Please setup genomes next.

# This took approximately 2 hours to download
[user@cpu_node CogentAP]$ bash CogentAP_setup.sh genome_install hg38
Start installing genome: hg38
Downloading...
SSHPASS searching for password prompt using match "assword"
SSHPASS read: cogentap@fileshare.takarabio.com's password:
SSHPASS detected prompt. Sending password.
SSHPASS read:

Connected to fileshare.takarabio.com.

      ### For any remaining genomes NOTE: If other genomes need to be loaded, please refer to Section V.E of the User Manual

      - - -

*You should be able to run Cogent from the command-line at this point – however, if you’d like a GUI, logon to rmlsbatch3 via NoMachine, open a terminal session, load the miniconda3 module, and run the CogentAP_launcher:

- - -

[user@rmlsbatch3 ~]$ ml gcc/11.4.0-zp3cjbh && ml miniconda3/24.5.0-i3ittdj
[user@rmlsbatch3 ~]$ chmod u+x dev/cogent/CogentAP/CogentAP_launcher
[user@rmlsbatch3 ~]$ ./dev/cogent/CogentAP/CogentAP_launcher
```

#### maSigPro v1.76
```bash
# Get an interactive shell on BigSky
[user@scheduler_node ~]$ srun -c 32 --mem 128G --pty bash

# Load the Miniconda3 module
[user@cpu_node ~]$ ml gcc/11.4.0-zp3cjbh && ml miniconda3/24.5.0-i3ittdj

# Create a conda environment for maSigPro & activate the environment
[user@cpu_node ~]$ conda create -n maSigPro conda
[user@cpu_node ~]$ source activate maSigPro

# Install mamba & set it in your conda
(maSigPro) [user@cpu_node ~]$ conda install -n masigpro -c conda-forge mamba
(maSigPro) [user@cpu_node ~]$ conda config --set solver libmamba

# Install R v4.4.0 and start R
(maSigPro) [user@cpu_node ~]$ mamba install r-base=4.4.0
(maSigPro) [user@cpu_node ~]$ R
R version 4.4.0 (2024-04-24) -- "Puppy Cup"

. . . many lines omitted . . .

# Install BioConductor Manager & maSigPro in R
> if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("maSigPro")

Bioconductor version 3.19 (BiocManager 1.30.25), R 4.4.0 (2024-04-24)

> library(maSigPro)
> help(package="maSigPro")
> quit()
Save workspace image? [y/n/c]: n

(masigpro) [user@cpu_node ~]$ conda deactivate
```

---

#### DVGfinder v3.0
```bash
# Get an interactive shell on the cluster
[user@scheduler_node ~]$ srun -c 16 –-mem 128G –-pty bash

# Module load miniconda3
[user@cpu_node ~]$ ml miniconda3

# Clone the DVGfinder repository, change into the new directory, modify permissions on downloaded scripts to make them executable
[user@cpu_node ~]$ git clone https://github.com/MJmaolu/DVGfinder.git && cd DVGfinder;chmod -R +x .

# Create your conda environment using the provided dvgfinder_env.yaml file
[user@cpu_node DVGfinder]$ conda env create -f dvgfinder_env.yaml

. . . many lines omitted . . .

#
# To activate this environment, use
#
#     $ conda activate dvgfinder_env (this command won’t work in our env, so we’ll substitute the one below)
#
# To deactivate an active environment, use
#
#     $ conda deactivate
#

# Activate the new conda environment
[user@cpu_node DVGfinder]$ source activate dvgfinder_env

# Test
(dvgfinder_env) [user@cpu_node DVGfinder]$ python3 DVGfinder_v3.1.py -fq tumvas72_N100K_l100/tumvas72_N100K_l100.fq -r ExternalNeeds/references/TuMV-AS.fasta -n 4

-------------------------------------------------------------------------------------------
ViReMa Version 0.23 - Command-Line version - written by Andrew Routh
Last modified Mar 2021
-------------------------------------------------------------------------------------------
=================================
Program: DI-tector
Version: 0.6 - Last modified 25/05/2018
=================================

. . . many lines omitted . . .
```

#### VirTools/VirVarSeq
```bash
# Get an interactive shell on the cluster
[user@scheduler_node ~]$ srun -c 16 –-mem 128G –-pty bash

# Download the application, I chose the package with bundled test-data, which is significantly larger, and purposefully named the saved file (-O <name>)
[user@cpu_node ~]$ wget https://sourceforge.net/projects/virtools/files/VirVarSeq.tar.gz/download -O VirVarSeq.tar.gz

# Load Miniconda3
[user@cpu_node ~]$ ml miniconda3

# Create a new conda environment, with the r-base version at “3”
[user@cpu_node ~]$ conda create -n virtools-test -c conda-forge r-base=3

# Use “source” rather than “conda” here
[user@cpu_node ~]$ source activate virtools-test

# Start an R session
(virtools-test) [user@cpu_node VirVarSeq]$ R --no-save --no-restore

R version 3.6.3 (2020-02-29) -- "Holding the Windsock"

. . . many lines omitted . . .

# Install necessary R packages per vendor doc, be sure to change the paths to reflect the location of your VirVarSeq files
> install.packages("/path/to/home/user/VirVarSeq/R/packages/rmgt_0.9.001.tar.gz",repos=NULL,lib="/path/to/home/user/VirVarSeq/R/lib")
* installing *source* package ‘rmgt’ ...

. . . many lines omitted . . .

* DONE (rmgt)

# Quit out of the R session
> quit()

# Export paths per vendor doc
(virtools-test) [user@cpu_node VirVarSeq]$ export PERL5LIB=/path/to/home/user/VirVarSeq/lib
(virtools-test) [user@cpu_node VirVarSeq]$ export R_LIBS_USER=/path/to/home/user/VirVarSeq/lib

# Run the pipeline script that ties together the various functions
(virtools-test) [user@cpu_node VirVarSeq]$ ./run.sh
      
The output of the run.sh script is stored in : <VirVarSeq>/testdata/results

(virtools-test) [user@cpu_node VirVarSeq]$ ls testdata/results/
codon_table/      consensus/        map_vs_consensus/ map_vs_ref/       mixture_model/
(virtools-test) [user@cpu_node VirVarSeq]$ ls testdata/results/consensus/110901_6_12_random_
110901_6_12_random_1_consensus.fa      110901_6_12_random_1_consensus.fa.pac  110901_6_12_random_2_consensus.fa.ann
110901_6_12_random_1_consensus.fa.amb  110901_6_12_random_1_consensus.fa.sa   110901_6_12_random_2_consensus.fa.bwt
110901_6_12_random_1_consensus.fa.ann  110901_6_12_random_2_consensus.fa      110901_6_12_random_2_consensus.fa.pac
110901_6_12_random_1_consensus.fa.bwt  110901_6_12_random_2_consensus.fa.amb  110901_6_12_random_2_consensus.fa.sa
```
---

#### TreeTime v0.11.3
```bash
# Launch an interactive job on the cluster/load miniconda3/create conda env with relevant channels

[user@scheduler_node ~]$ srun -c 16 --mem 128G --pty bash
[user@cpu_node ~]$ ml miniconda3
[user@cpu_node ~]$ conda create -n treetime-0.11.3 -c bioconda

## Package Plan ##

  environment location: /path/to/home/user/.conda/envs/treetime-0.11.3

#
# To activate this environment, use
#
#     $ conda activate treetime-0.11.3 (this command won’t work in our env, so we’ll substitute the one below)
#
# To deactivate an active environment, use
#
#     $ conda deactivate

[user@cpu_node ~]$ source activate treetime-0.11.3

# Note the context change

(treetime-0.11.3) [user@cpu_node ~]$ conda install treetime

(treetime-0.11.3) [user@cpu_node ~]$ treetime --version
treetime 0.11.3

(treetime-0.11.3) [user@cpu_node ~]$ conda deactivate
[user@cpu_node ~]$
```

---

### See Also

**From the Anaconda Project**:
- Conda Home - https://docs.conda.io/en/latest/  
- Conda User-Guide - https://conda.io/projects/conda/en/latest/user-guide/index.html
- Installing Conda packages - https://docs.anaconda.com/working-with-conda/packages/install-packages/  
- Using R language with Anaconda - https://docs.anaconda.com/working-with-conda/packages/using-r-language/  
- Working with GPU packages - https://docs.anaconda.com/working-with-conda/packages/gpu-packages/  
- Managing Python - https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-python.html
- Conda Commands - https://docs.conda.io/projects/conda/en/stable/commands/index.html
- Managing Environments: https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html
- Conda TensorFlow: https://docs.anaconda.com/working-with-conda/applications/tensorflow/
- Anaconda Repo: https://anaconda.org/anaconda/repo

---

**Emphasis on Data Science**:
- Datascience on Cluster with Conda: https://rabernat.medium.com/custom-conda-environments-for-data-science-on-hpc-clusters-32d58c63aa95
- Introduction to Conda for (Data) Scientists:
  - https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/  
  - https://conda.io/projects/conda/en/latest/user-guide/concepts/data-science.html
 
---

**External**:
- Conda Cheat Sheet - https://www.datacamp.com/cheat-sheet/conda-cheat-sheet  
- Conda Tips n Tricks - https://towardsdatascience.com/conda-essential-concepts-and-tricks-e478ed53b5b  
- Intro Environment Management: https://www.anaconda.com/blog/getting-started-with-conda-environments
- Anaconda Environments: https://docs.anaconda.com/working-with-conda/environments/
- Miniconda3 Docker: https://hub.docker.com/r/continuumio/miniconda3
- Conda Working with Environment Files: https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually
- Conda with Containers: https://portal.osg-htc.org/documentation/software_examples/other_languages_tools/conda-container/
- Conda and Singularity: https://portal.osg-htc.org/documentation/htc_workloads/using_software/containers-singularity/
- Conda on the Cluster: https://kb.hlrs.de/platforms/index.php/How_to_use_Conda_environments_on_the_clusters

**University**:
- ISU Conda: https://www.hpc.iastate.edu/guides/virtual-environments/conda-environments
- SDSU Conda: https://help.sdstate.edu/TDClient/2744/Portal/KB/ArticleDet?ID=160260  
- Yale Miniconda3 Module: https://docs.ycrc.yale.edu/clusters-at-yale/guides/conda/  
- Stanford Conda: https://srcc.stanford.edu/events/lunch-learn/conda-clusters
- Northeastern Conda: https://rc-docs.northeastern.edu/en/latest/software/packagemanagers/conda.html

---
