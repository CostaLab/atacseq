# PIPELINE & DOCS UNDER CONSTRUCTION. ETA OCTOBER 2018.

# nf-core/atacseq
# ![nfcore/atacseq](docs/images/nfcore-atacseq_logo.png)
**ATACSeq peak-calling and differential analysis pipeline.**

[![Build Status](https://travis-ci.org/nf-core/atacseq.svg?branch=master)](https://travis-ci.org/nf-core/atacseq)
[![Nextflow](https://img.shields.io/badge/nextflow-%E2%89%A50.30.0-brightgreen.svg)](https://www.nextflow.io/)

[![install with bioconda](https://img.shields.io/badge/install%20with-bioconda-brightgreen.svg)](http://bioconda.github.io/)
[![Docker](https://img.shields.io/docker/automated/nfcore/atacseq.svg)](https://hub.docker.com/r/nfcore/atacseq)
![Singularity Container available](
https://img.shields.io/badge/singularity-available-7E4C74.svg)

### Introduction

**nfcore/atacseq** is a bioinformatics analysis pipeline used for ATAC-seq data.

The pipeline is built using [Nextflow](https://www.nextflow.io), a workflow tool to run tasks across multiple compute infrastructures in a very portable manner. It comes with docker / singularity containers making installation trivial and results highly reproducible.

### Pipeline summary

1. Raw read QC ([`FastQC`](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), [`Fastq Screen`](https://www.bioinformatics.babraham.ac.uk/projects/fastq_screen/))
2. Adapter trimming ([`cutadapt`](http://cutadapt.readthedocs.io/en/stable/installation.html))
3. Alignment ([`BWA`](https://sourceforge.net/projects/bio-bwa/files/))
4. Mark duplicates ([`picard`](https://broadinstitute.github.io/picard/))
5. Filtering to remove:
    * reads mapping to mitochondrial DNA ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/))
    * reads mapping to blacklisted regions ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/), [`BEDTools`](https://github.com/arq5x/bedtools2/))
    * reads that are marked as duplicates ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/))
    * reads that arent marked as primary alignments ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/))
    * reads that are unmapped ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/))
    * reads that map to multiple locations ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/))
    * reads containing > 3 mismatches in either read of the pair ([`BAMTools`](https://github.com/pezmaster31/bamtools))
    * reads that have an insert size > 2kb ([`BAMTools`](https://github.com/pezmaster31/bamtools))
    * reads that are soft-clipped ([`BAMTools`](https://github.com/pezmaster31/bamtools))
    * reads that map to different chromosomes ([`Pysam`](http://pysam.readthedocs.io/en/latest/installation.html))
    * reads that arent in FR orientation ([`Pysam`](http://pysam.readthedocs.io/en/latest/installation.html))
    * reads where only one read of the pair fails the above criteria ([`Pysam`](http://pysam.readthedocs.io/en/latest/installation.html))
6. Merge alignments at replicate and sample level ([`picard`](https://broadinstitute.github.io/picard/))
    * Re-mark duplicates ([`picard`](https://broadinstitute.github.io/picard/))
    * Remove duplicate reads ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/))
    * Create normalised bigWig files scaled to 1 million mapped read pairs ([`BEDTools`](https://github.com/arq5x/bedtools2/), [`wigToBigWig`](http://hgdownload.soe.ucsc.edu/admin/exe/))
    * Call broad peaks ([`MACS2`](https://github.com/taoliu/MACS))
    * Annotate peaks relative to gene features ([`HOMER`](http://homer.ucsd.edu/homer/download.html))
    * Merge peaks across all samples and create tabular file to aid in the filtering of the data ([`BEDTools`](https://github.com/arq5x/bedtools2/))
    * Count reads in merged peaks from replicate-level alignments ([`featureCounts`](http://bioinf.wehi.edu.au/featureCounts/))
    * Differential binding analysis, PCA and clustering ([`R`](https://www.r-project.org/), [`DESeq2`](https://bioconductor.org/packages/release/bioc/html/DESeq2.html))
7. Create IGV session file containing bigWig tracks, peaks and differential sites for data visualisation ([`IGV`](https://software.broadinstitute.org/software/igv/)).
8. Collect and present QC at the raw read, alignment and peak-level ([`MultiQC`](http://multiqc.info/), [`R`](https://www.r-project.org/))

### Documentation
The nf-core/atacseq pipeline comes with documentation about the pipeline, found in the `docs/` directory:

1. [Installation](docs/installation.md)
2. Pipeline configuration
    * [Local installation](docs/configuration/local.md)
    * [Adding your own system](docs/configuration/adding_your_own.md)
3. [Running the pipeline](docs/usage.md)
4. [Output and how to interpret the results](docs/output.md)
5. [Troubleshooting](docs/troubleshooting.md)

### Credits

The pipeline was originally written by the [The Bioinformatics & Biostatistics Group](https://www.crick.ac.uk/research/science-technology-platforms/bioinformatics-and-biostatistics/) for use at [The Francis Crick Institute](https://www.crick.ac.uk/), London.

The pipeline was developed by [Harshil Patel](mailto:harshil.patel@crick.ac.uk), [Philip East](mailto:philip.east@crick.ac.uk) and [Nourdine Bah](mailto:nourdine.bah@crick.ac.uk).

The [ngi-core/rnaseq](https://github.com/nf-core/rnaseq) pipeline developed by Phil Ewels was used a template for this pipeline. Many thanks to Phil and the team at SciLifeLab.

Many thanks to other who have helped out along the way too, including (but not limited to):
[@pditommaso](https://github.com/pditommaso),
[@apeltzer](https://github.com/apeltzer).
