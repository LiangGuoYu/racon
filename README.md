# Racon
Consensus module for raw de novo DNA assembly of long uncorrected reads.  

## Description
Racon is intended as a standalone consensus module to correct raw contigs generated by rapid assembly methods which do not include a consensus step, such as [Miniasm](https://github.com/lh3/miniasm).  
The goal of Racon is to generate genomic consensus which is of similar or better quality compared to the output generated by assembly methods which employ both error correction and consensus steps, while providing a speedup of several times compared to those methods.  
Racon takes as input only three files: the raw contig FASTA, the original raw reads in FASTQ format and an overlap file in PAF or MHAP format containing overlaps between the reads and the contigs.  
Overlaps can be generated quickly using [Minimap](https://github.com/lh3/minimap) and piped through to Racon (see example usage below).  
Racon then reads the overlaps, filters them, and aligns the region defined by each overlap.  

Alternatively, instead of overlaps, alignments (SAM format) of raw reads mapped to the contigs can be provided. We recommend [GraphMap](https://github.com/isovic/graphmap) for generating the alignments.  

Please note: Racon depends on *quality values* - the SAM file needs to have them included.  
There is also a default QV threshold set to 10 (Phred score). If your data is of poorer quality, you can modify this parameter using ```--qv FLOAT```. If ```FLOAT``` is ```-1```, the QV filtering will be turned off.  

## Quick start
Clone and make Racon:
```  
git clone https://github.com/isovic/racon.git  && cd racon && make modules && make tools && make -j  
```
Run an example script:  
```  
./example1-paf-lambda.sh   
```  
Tip: Running Racon iteratively will produce better consensus sequences. (But don't forget to re-run the overlap/alignment of your reads to the consensus sequence from the previous iteration.)  

## Dependencies
1. gcc >= 4.8  
2. Zlib - sudo apt-get install zlib1g-dev  

Optional:  
MUMmer needs to be installed to successfully execute the example scripts (for evaluation purposes).  
On Ubuntu-based systems:  
```  
sudo apt-get install mummer  
```  
Numpy is also required for evaluation purposes:  
```  
sudo apt-get install python-numpy  
```  


## Installation
```  
git clone https://github.com/isovic/racon.git && cd racon && make modules && make tools && make -j  

```  

## Usage
```  
bin/racon [options] <reads.fastq> <overlaps.paf> <raw_contigs.fasta> <out_consensus.fasta>  
```  
Racon depends on quality values - the reads file/SAM file needs to have them included.  
For detailed info on various options, run ```bin/racon``` without arguments.  

Racon can also be run in pipe with other tools: overlaps can be read directly from ```stdin``` if the parameter ```<overlaps.paf>``` is equal to ```-```.  

A concrete example is below.  
Generate overlaps using, e.g., Minimap:  
```  
tools/minimap/minimap test-data/lambda/layout-miniasm.gfa.fasta test-data/lambda/reads.fastq > test-data/lambda/overlaps.paf  
```  

Run Racon on the overlaps:  
```  
bin/racon test-data/lambda/reads.fastq test-data/lambda/overlaps.paf test-data/lambda/layout-miniasm.gfa.fasta test-data/lambda/consensus.fasta  
```  

Conversely, the same can be done using a one-liner:  
```  
tools/minimap/minimap test-data/lambda/layout-miniasm.gfa.fasta test-data/lambda/reads.fastq | bin/racon test-data/lambda/reads.fastq - test-data/lambda/layout-miniasm.gfa.fasta test-data/lambda/consensus.fasta  
```  

## Contact information

For additional information, help and bug reports please send an email to one of the following: ivan.sovic@irb.hr, robert.vaser@fer.hr, mile.sikic@fer.hr, nagarajann@gis.a-star.edu.sg


## Acknowledgment

This work has been supported in part by Croatian Science Foundation under the project UIP-11-2013-7353.  
IS is supported in part by the Croatian Academy of Sciences and Arts under the project "Methods for alignment and assembly of DNA sequences using nanopore sequencing data".  
NN is supported by funding from A*STAR, Singapore.
