Strawberry source code current version: 0.9.1. 
==================

Important Notes
==================
**If you download Strawberry 0.9.1 at 12/04/2017. There is a serious bug in terms of the output. The assembled_transcripts.gtf will be empty if you use default number of threads which is 1. This bug has been fixed at 12/05/2017. Please update your repo.** 

News
==================
#### Strawberry 0.9.1 released.
1. Fix a bug when the reference sequence names (e.g., contig or chromosome names) are long. This bug affects assembled_transcripts.gtf and sometimes make the first column unreadable. 
2. Add TPM (Transcript Per Million mapped reads) to the output. 

#### Strawberry first release: 0.9.0.

What is Strawberry?
==================
Strawberry is a C++ program for fast and accurate ab initio transcript reconstruction and quantification from RNA-seq data. It is written in C++11 and is available as open source software. Strawberry leverages the speed and accuracy of transcript assembly and quantification in such a way that processing 10 million simulated reads (after alignment) requires only 2 minutes using a single thread while achieving over 92% correlation with the ground truth, making it the state-of-the-art method.

Strawberry is a genome-guided transcript-level assembler and quantification tool. It takes aligned RNA-Seq data in BAM format and outputs a gene annotation files in gff format with estimated transcripts abundances. Using alignment file as input allows Strawberry to take advantages of the latest reference genome (if possible, a finished and high-quality one) and stat-of-the-art splice-awareness aligners. The application of a fast flow network algorithm, for assembly speeds up the construction of transcript-based models. The resulting reduced data representation improves quantification of the different isoforms. Strawberry is also able to account for various sequencing bias that is intrinsic to the RNA-seq experiment. For paired-end RNA-Seq, Strawberry empirically infers the insert length distribution from the reads that are mapped uniquely and concordantly. If half-mapped reads exist, Strawberry generates the other ends based on the mapped orientations and the insert length distribution. 

Strawberry consists of two modules: assembly module and quantification module. The two modules work in a sequential manner. The assembly module parses aligned reads into splicing graphs, and it uses network flow algorithms to selected the most likely transcripts. The assembly module can take an existing gene annotation file as a guide and this practice usually leads to better assembly result. The quantification module uses a statistical model, more accurately a Latent Class Model, to assign ambiguous reads to transcripts. Strawberry simultaneous estimates the transcript abundances and corrects for sequencing bias through the EM steps. The quantification model can be executed along without doing an assembly. This requires a user to provide an additional gene annotation file in gff3 or gtf format.

Documentation
===================
Strawberry is now published in [PLoS Computational Biology](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005851).
<hr />

Latest Linux x86_64 Binary release
====================
https://github.com/ruolin/strawberry/releases/download/0.9.1/strawberry

Prerequisites if you want to install Strawberry from scratch
===================
* A C++14 conformant compiler (currently tested with GCC>=5.3).

  * Here is wiki about how to install GCC, https://gcc.gnu.org/wiki/InstallingGCC.
  * And for ubuntu user, please refer to the post for how to install gcc 5+,
https://askubuntu.com/questions/618474/how-to-install-the-latest-gcurrently-5-1-in-ubuntucurrently-14-04.

* Strawberry uses [CMake](https://cmake.org/)(3.5+) build system to check and install dependencies and to compile and install Strawberry.
* [Samtools](http://samtools.sourceforge.net/). Strawberry uses an older version of Samtools(v0.1.19) as a dependent library.   
  * If a different version of Samtools (currently Samtools split into 3 projects now and has been updated to v1.3) has been installed, or your system does not have Samtools installed at all, please let Strawberry automatically download and install Samtools for you. You *DONOT* need to do anything.
  * If you have installed Samtools v0.1.19 and want to use it, please make sure that *libbam.a* is in the Samtools source directory and set an environmental variable SAMTOOLS_ROOT to that location. 
    
    * If you use csh or tcsh, at the shell prompt, enter 
      
     `setenv SAMTOOLS_ROOT /path/to/samtools-source-dir`.
    * If you use bash, at the shell promet, enter 
    
     `export SAMTOOLS_ROOT /path/to/samtools-source-dir`.

Download
========

`git clone --recursive https://github.com/ruolin/Strawberry.git`

If you miss --recusive when you clone, you can do 

`git submodule init`

and then 

`git submodule update`

Note: Precompiled binary is available on the [release page] (https://github.com/ruolin/Strawberry/releases) for the latest version. 

Installation
============
1. `sh cmake.sh`
2. `cd build`
3. `make`

WARNING: using parallel build (-j option) might cause errors. 
The executable file will be found in the `Strawberry/bin` directory. 
You can add this directory to your PATH variable to complete the installation by

`export PATH=/path/to/your/intallation/bin/:$PATH`

Running Strawberry
==================

Running Strawberry is relatively easy. You need to have an alignment file in BAM format. This step can be done using any splice-awareness aligner, e.g. Tophat 2, GSNAP, HISAT 2. The BAM file needs to be sorted according to the genomic positions. If you use Tophat, the default output is already sorted. For other software, you might have to sort their outputs before running Strawberry. This can be done using Samtools command `samtools sort`.

I provide a toy bam file for testing the instalation. 
Under the root directory of your installation, type  `bin/strawberry examples/geuvadis_300/sample_01.sorted.bam` to run the program on the default parameters. This bam files contains read from 300 genes. And if everything is fine, Strawberry will finish in seconds. 

User Manual
===================
Strawberry can be run in three different modes. The default mode is to do assembly and quantification without reference annotation. 

`bin/strawberry examples/geuvadis_300/sample_01.sorted.bam -o output_dir -p 8`

You can also run strawberry with the help of reference annotation. In this mode, Strawberry uses the known gene models to guide assembly. Novel isoform discovery are still allowed. If you have a good quality reference annotation but also want to detect novel isoforms, this is the ideal mode. 

`bin/strawberry examples/geuvadis_300/sample_01.sorted.bam -o output_dir -g reference.gtf -p 8`

If you truth your gene annotation completely and do not want to assembly any new isoforms, you can skip the assembly step and just do quantification against provided gene models.

`bin/strawberry examples/geuvadis_300/sample_01.sorted.bam -o output_dir -g reference.gtf -r -p 8`

Good luck!

For the choice of parameters and their meanings type `strawberry` without any argument for help information. 







