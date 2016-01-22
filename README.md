A [Nextflow](http://www.nextflow.io/) pipeline for processing ChIP-seq data.

## Installing Nextflow

Follow Nextflow documentation [here](http://www.nextflow.io/docs/latest/getstarted.html#get-started).

## Running the pipeline

First you need to pull the pipeline using Nextflow:

```bash
$ nextflow pull guigolab/chip-nf
Checking guigolab/chip-nf ...
 downloaded from https://github.com/guigolab/chip-nf.git
```

You can then get the pipeline help:

```bash
$ nextflow run chip-nf --help


N E X T F L O W  ~  version 0.16.2
Launching guigolab/chip-nf

C H I P - N F ~ ChIP-seq Pipeline
=================================
Run ChIP-seq analyses on a set of data.

Usage:
    chipseq-pipeline.nf --index TSV_FILE --genome GENOME_FILE [OPTION]...

Options:
    --help                              Show this message and exit.
    --index TSV_FILE                    Tab separated file containing information about the data.
    --genome GENOME_FILE                Reference genome file.
    --mismatches N_MISMATCHES           Allow max N_MISMATCHES error events for a read (Default: 2).
    --multimaps N_MULTIMAPS             Allow max N_MULTIMAPS mappings for a read (Default: 10).
    --rescale                           Rescale peak scores to conform to the format supported by the
                                        UCSC genome browser (score must be <1000) (Default: false).
```


## Input

The input data and metadata should be specified using a tab separated file and pass it to the pipeline with the option `--index`. The format of this file is the following:

```bash
sample1    sample1_run1     /path/to/sample1_run1.fastq.gz    -           H3
sample1    sample1_run2     /path/to/sample1_run2.fastq.gz    -           H3
sample1    sample1_run3     /path/to/sample1_run3.fastq.gz    -           H3
sample1    sample1_run4     /path/to/sample1_run4.fastq.gz    -           H3
sample2    sample2_run1     /path/to/sample2_run1.fastq.gz    control1    H3K4me2
control1   control1_run1    /path/to/control1_run1.fastq.gz   control1    input
```

1. identifier used for merging the BAM files
2. single run identifier
3. path to the fastq file to be processed
4. identifier of the input or `-` if no control is used
5. mark/histone or `input` if the line refers to a control


## Output

The pipeline will produce the following output data:

- `Alignments`
- `pileupSignal`, pileup signal tracks
- `fcSignal`,  fold enrichment signal tracks
- `pvalueSignal`, **-log10(p-value)** signal tracks
- `narrowPeak`, peak locations with peak summit, pvalue and qvalue (`BED6+4`)
- `broadPeak`, similar to `narrowPeak` (`BED6+3`)
- `gappedPeak`, both narrow and broad peaks (`BED12+3`)

Check [MACS2 output files](https://github.com/taoliu/MACS#output-files) for details.

The output data information is written to a file called `chipseq-pipeline.db` created in the folder from where the pipeline is run. The file has the following format:

```bash
sample1   /path/to/results/peakOut/sample1.pileup_signal.bw    H3         255     pileupSignal
sample1   /path/to/results/peakOut/sample1_peaks.narrowPeak    H3         255     narrowPeak
sample1   /path/to/results/sample1.bam                         H3         255     Alignments
sample1   /path/to/results/peakOut/sample1_peaks.gappedPeak    H3         255     gappedPeak
sample1   /path/to/results/peakOut/sample1_peaks.broadPeak     H3         255     broadPeak
sample2   /path/to/results/peakOut/sample2_peaks.gappedPeak    H3K4me2    200     gappedPeak
sample2   /path/to/results/peakOut/sample2.fc_signal.bw        H3K4me2    200     fcSignal
sample2   /path/to/results/peakOut/sample2.pval_signal.bw      H3K4me2    200     pvalueSignal
sample2   /path/to/results/peakOut/sample2_peaks.broadPeak     H3K4me2    200     broadPeak
sample2   /path/to/results/peakOut/sample2.pileup_signal.bw    H3K4me2    200     pileupSignal
sample2   /path/to/results/peakOut/sample2_peaks.narrowPeak    H3K4me2    200     narrowPeak
sample2   /path/to/results/sample2_GCCAAT_primary.bam          H3K4me2    200     Alignments
```

1. merge identifier
2. path
3. mark/histone
4. estimated fragment length
5. data type
