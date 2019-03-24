# Racon

[![Latest GitHub release](https://img.shields.io/github/release/isovic/racon.svg)](https://github.com/isovic/racon/releases/latest)
[![Build status for gcc/clang](https://travis-ci.org/isovic/racon.svg?branch=master)](https://travis-ci.org/isovic/racon)
[![Published in Genome Research](https://img.shields.io/badge/published%20in-Genome%20Research-blue.svg)](https://doi.org/10.1101/gr.214270.116)

Consensus module for raw de novo DNA assembly of long uncorrected reads.

## Description
Racon is intended as a standalone consensus module to correct raw contigs generated by rapid assembly methods which do not include a consensus step. The goal of Racon is to generate genomic consensus which is of similar or better quality compared to the output generated by assembly methods which employ both error correction and consensus steps, while providing a speedup of several times compared to those methods. It supports data produced by both Pacific Biosciences and Oxford Nanopore Technologies.

Racon can be used as a polishing tool after the assembly with **either Illumina data or data produced by third generation of sequencing**. The type of data inputed is automatically detected.

Racon takes as input only three files: contigs in FASTA/FASTQ format, reads in FASTA/FASTQ format and overlaps/alignments between the reads and the contigs in MHAP/PAF/SAM format. Output is a set of polished contigs in FASTA format printed to stdout. All input files **can be compressed with gzip**.

Racon can also be used as a read error-correction tool. In this scenario, the MHAP/PAF/SAM file needs to contain pairwise overlaps between reads **including dual overlaps**.

A **wrapper script** is also available to enable easier usage to the end-user for large datasets. It has the same interface as racon but adds two additional features from the outside. Sequences can be **subsampled** to decrease the total execution time (accuracy might be lower) while target sequences can be **split** into smaller chunks and run sequentially to decrease memory consumption. Both features can be run at the same time as well.

## Dependencies
1. gcc 4.8+ or clang 3.4+
2. cmake 3.2+ or meson 0.49+

## Installation
There are two alternative build options which can be used to compile Racon:
1. CMake, and
2. Meson.

### CMake
To compile Racon using CMake, run the following commands:

```bash
git clone --recursive https://github.com/isovic/racon.git racon
cd racon
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

After successful installation, an executable named `racon` will appear in `build/bin`.

Optionally, you can run `sudo make install` to install racon executable to your machine.

***Note***: if you omitted `--recursive` from `git clone`, run `git submodule update --init --recursive` before proceeding with compilation.

To build unit tests add `-Dracon_build_tests=ON` while running `cmake`. After installation, an executable named `racon_test` will be created in `build/bin`.

To build the wrapper script add `-Dracon_build_wrapper=ON` while running `cmake`. After installation, an executable named `racon_wrapper` (python script) will be created in `build/bin`.

### Meson
To compile Racon using Meson, run the following commands:
```bash
git clone --recursive https://github.com/isovic/racon.git racon
cd racon
mkdir build
cd build
meson --buildtype=release -Dc_args=-O3
ninja
```

After successful installation, an executable named `racon` will appear in `build/`.
Tests are built automatically when Meson build is instantiated.

### Wrapping the builds using a Makefile
Alternatively, to achieve the same, we provide Makefile shorthands.  
To run the above commands and build the project using CMake, simply type:  
```
make cmake
```

For the Meson build version, analogously write:
```
make meson
```

(Note: CMake build directory will be `build`, and Meson will be built in `build-meson` to make the two build types distinct.)

## Usage
Usage of `racon` is as following:

    racon [options ...] <sequences> <overlaps> <target sequences>

        <sequences>
            input file in FASTA/FASTQ format (can be compressed with gzip)
            containing sequences used for correction
        <overlaps>
            input file in MHAP/PAF/SAM format (can be compressed with gzip)
            containing overlaps between sequences and target sequences
        <target sequences>
            input file in FASTA/FASTQ format (can be compressed with gzip)
            containing sequences which will be corrected

    options:
        -u, --include-unpolished
            output unpolished target sequences
        -f, --fragment-correction
            perform fragment correction instead of contig polishing (overlaps
            file should contain dual/self overlaps!)
        -w, --window-length <int>
            default: 500
            size of window on which POA is performed
        -q, --quality-threshold <float>
            default: 10.0
            threshold for average base quality of windows used in POA
        -e, --error-threshold <float>
            default: 0.3
            maximum allowed error rate used for filtering overlaps
        -m, --match <int>
            default: 5
            score for matching bases
        -x, --mismatch <int>
            default: -4
            score for mismatching bases
        -g, --gap <int>
            default: -8
            gap penalty (must be negative)
        -t, --threads <int>
            default: 1
            number of threads
        --version
            prints the version number
        -h, --help
            prints the usage

`racon_test` is run without any parameters.

Usage of `racon_wrapper` equals the one of `racon` with two additional parameters:

    ...
    options:
        --split <int>
            split target sequences into chunks of desired size in bytes
        --subsample <int> <int>
            subsample sequences to desired coverage (2nd argument) given the
            reference length (1st argument)
        ...

## Contact information

For additional information, help and bug reports please send an email to one of the following: ivan.sovic@irb.hr, robert.vaser@fer.hr, mile.sikic@fer.hr, nagarajann@gis.a-star.edu.sg

## Acknowledgment

This work has been supported in part by Croatian Science Foundation under the project UIP-11-2013-7353. IS is supported in part by the Croatian Academy of Sciences and Arts under the project "Methods for alignment and assembly of DNA sequences using nanopore sequencing data". NN is supported by funding from A*STAR, Singapore.
