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

Compare the different basecalling methods methods on the subset of fast5 files. 


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



