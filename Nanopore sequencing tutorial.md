# EBAME 6 2021: Nanopore sequencing workshop

## Long read metagenomics of the human gut microbiome


### Introduction
Today we aim to use real nanopore derived long read sequences to examen the community composition of two mock communities constructed of representitive members of the human gut microbiome.

### Laboratory methods

### Setup

### Data 

## Tutorial
### Basecalling
Nanopore sequencing results in fast5 files that contain raw signal data termed "squiggles". This signal needs to be processed into the `.fastq` format for onward analysis. This is undertaken through a process called 'basecalling'. The current program released for use by Oxford Nanopore is called `Guppy` and can be implemented in both GPU and CPU modes. Three forms of basecalling are available, 'fast', 'high-accuracy' (HAC) and 'super high accuracy' (SUP-HAC). The differing basecalling approaches can be undertaken directly during a sequencing run or in post by using CPU or GPU based clusters. HAC and SUP-HAC basecalling algorithms are highly computationally intensive and thus slower than the fast basecalling method. While devices such as the GridION and PromethION can basecall using these algorithms in real time due to their on-board GPU configuration, thus permitting adaptive sequencing (read-until), the MinION device relies on the computational power of the attached system on which it is running. Guppy basecaller is also able to demultiplex barcoded reads both in real time and in post processing.


Get the fast5 reads into a dir on our /mnt directories:

```
mkdir /mnt/Projects

ln -s /mnt/Projects ~

mkdir ~/Projects/LongReads

cd Projects/LongReads

cp path/to/fast5_subset.tar.gz (currently ~rob/HU_mock/EBAME6_workshop/fast5_subset.tar.gz)

tar -xvzf fast5_subset.tar.gz

rm fast5_subset.tar.gz
```
|Flag / command            | Description               | 
| -------------------------|:-------------------------:| 
| `mkdir`                  |make a new directory       | 
| `ln -s`                  |create system link to dir  | 
| `cd`                     |change directory           |
| `cp`                     |copy                       |
| `tar`                    |tar and uncompress         |
| `-x`                     |uncompress archive         |
| `-v`                     |verbose progress           |
| `-z`                     |using gzip                 |
| `-f`                     |file name                  |
| `rm`                     |remove                     |



Compare the different basecalling methods methods on the subset of fast5 files. Only try fast and high quality, SUP-HAC is very slow on CPUs.

```
Usage:

With config file:
  guppy_basecaller -i <input path> -s <save path> -c <config file> [options]

With flowcell and kit name:
  guppy_basecaller -i <input path> -s <save path> --flowcell <flowcell name>
    --kit <kit name>

List supported flowcells and kits:
  guppy_basecaller --print_workflows

```

Try `guppy_basecaller -h` for help. 

Samples were sequenced with LSK-109 kit (ligation sequencing kit).

Reads are barcoded using EXP-NBD104 kit if you wish to try demultiplexing reads (optional).

When working with post processing basecalling it is usefull to use the `screen` command. This allows you to run the command in the background by detaching from the current process. To detach from a screen us `ctrl + A D`. To resume a screen use the command `screen -r`. To close a screen use `exit` within the screen environment.

(optional) Once detached from a screen running 'guppy_basecaller', you can count the number of reads being written in real time by changing to the `pass` directory where the fastq files are being written and implementing the following bash one-liner.

```
watch -n 10 'find . -name "*.fastq" -exec grep 'read=' -c {} \; | paste -sd+ | bc'
```

<details><summary>SPOILER: Click for basecalling code reveal </summary>
<p>

### Guppy fast basecalling

```
guppy_basecaller -r --input_path fast5_raw --save_path raw_fastq --min_qscore 7 --cpu_threads_per_caller 4 --num_callers 2 --flowcell FLO-MIN106 --kit SQK-LSK109 -q 10 
```
|Flag / command            | Description               | 
| -------------------------|:-------------------------:| 
| `guppy_basecaller`       |call guppy.                | 
| `-r`                     |recursive                  | 
| `-input_path`            |path/to/fast5/dir          |
| `--save_path`            |path/to/fastq/output/dir   |
| `--min_qscore`           |mean min quality score     |
| `-cpu_threads_per_caller`|cpu threads                |
| `-num_callers`           |parallisation              |
| `-flowcell`              |flow cell type             |
| `-kit`                   |kit used                   |
| `-q`                     |reads per fastq file       |
  
### Guppy high accuracy basecalling
  
```
guppy_basecaller -r --input_path fast5_raw --save_path raw_fastq_HQ --config dna_r9.4.1_450bps_hac.cfg --min_qscore 7 --cpu_threads_per_caller 4 --num_callers 2 --flowcell FLO-MIN106 --kit SQK-LSK109 -q 10 
```
|Flag / command            | Description               | 
| -------------------------|:-------------------------:| 
| `--config`               |High accuracy config file  |
  
  
</p>
</details>

### Observations

How long did the different basecalling methods take to run?  
(optional) How do the identities differ at the individual read level when using a simple blast search of NCBI databases?  

## Read QC  

Before starting any analysis it is often advised to check the number of reads and quality of your run. You can start by using a simple bash one liner to count all reads in `pass/`.

Count the number of fastq reads in the Guppy pass dir using `grep` and / or `wc -l`.

<details><summary>SPOILER: Click for read counting code reveal </summary>
<p>

```
cat pass/*.fastq | grep 'read=' - -c
```
|Flag                         | Description                                                            | 
| ----------------------------|:----------------------------------------------------------------------:| 
| `cat`                       |display content                                                         | 
| `pass/*.fastq`              |of all files in `pass` dir ending in .fastq                             | 
| `\|`                         |pipe output of cat to grep                                              |
| `grep`                      |call grep search                                                        |
| `"read="`                   |look for lines with "read=" in                                          |
| `-`                         |target the output from `cat`                                            |
| `-c`                        |count                                                                   |

or

```
echo $(cat pass/*.fastq.temp |wc -l)/4|bc
```

|Flag                         | Description                                                            | 
| ----------------------------|:----------------------------------------------------------------------:| 
| `echo`                      |write to standard output (the screen)                                   | 
| `$(cat pass/*.fastq.temp`   |of all files in `pass` dir ending in .fastq.temp                        | 
| `\|`                         |pipe output of cat to wc                                                |
| `wc`                        |word count.                                                             |
| `-l`.                       |lines                                                                   |
| `/4`                        |devide number of lines by 4 (4 lines per fastq read)                    |
| `bc`                        |output via basic calculator                                             |

</details>



Once basecalling has completed you can create a single `.fastq` file for onward analysis by piping outputs from `cat pass/*.fastq | gzip - > workshop_fastq.gz`. However, due to the time constraints with basecalling, we have prepared a set of sanitised and down sampled fastq files for onward analysis. These reads have been base called using the super high accuracy basecaller on `Guppy5`.

Copy the fastq file into a dir on our /mnt directories:

```
cd ~/Projects/LongReads

cp path/to/fast5_subset.tar.gz . (currently ~rob/HU_mock/EBAME6_workshop/GutMocks.tar.gz)
