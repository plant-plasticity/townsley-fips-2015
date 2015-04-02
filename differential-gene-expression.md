# Differential gene expression

<!-- MarkdownTOC -->

- [Down-sampling SAM Files](#down-sampling-sam-files)
    - [Input files for the down-sampler](#input-files-for-the-down-sampler)
    - [About the down-sampling script](#about-the-down-sampling-script)
    - [How we ran the down-sampler](#how-we-ran-the-down-sampler)
    - [Output files from the down-sampler](#output-files-from-the-down-sampler)
- [Calculating counts from SAM files](#calculating-counts-from-sam-files)
    - [Input files for the read counter](#input-files-for-the-read-counter)
    - [About the read counting script](#about-the-read-counting-script)
    - [How we ran the read counter](#how-we-ran-the-read-counter)
    - [Output files from the read counter](#output-files-from-the-read-counter)

<!-- /MarkdownTOC -->

## Down-sampling SAM Files

In order to limit artifacts from comparing samples with significantly different numbers of sequence reads, we started by down-sampling the alignment files to contain roughly equivalent numbers of reads.

### Input files for the down-sampler

In our paper, we ran the down-sampler on sets of SAM alignment files grouped by library preparation method mapped to the appropriate reference sequence:

- HTR library method files (mapped to `ITAG.full-cds+500utr`):

        L_OLD42bp_A4.sam
        L_OLD42bp_B5.sam
        L_OLD42bp_C6.sam
        L_OLD42bp_D7.sam
        S_OLD42bp_A5.sam
        S_OLD42bp_A6.sam
        S_OLD42bp_B6.sam
        S_OLD42bp_B7.sam

- DGE library method files (mapped to `ITAG.500cds+500utr`):

        L_DGE_A4.sam
        L_DGE_B5.sam
        L_DGE_C6.sam
        L_DGE_D7.sam
        L_DGE_E7.sam
        L_DGE_E8.sam
        L_DGE_F1.sam
        S_DGE_A5.sam
        S_DGE_A6.sam
        S_DGE_B7.sam
        S_DGE_C7.sam
        S_DGE_D8.sam
        S_DGE_E1.sam
        S_DGE_F2.sam

### About the down-sampling script

The `down-sample-reads-auto.pl` script can be found in the [Read Counter GitHub repository](https://github.com/mfcovington/read_counter). Given a list of SAM files, this script automatically down-samples reads to match the alignment file with the lowest coverage.

Usage information can be viewed by running the script with the `-h` flag:

```sh
perl down-sample-reads-auto.pl -h
```

>     Usage:
>         perl down-sample-reads-auto.pl [options] <Alignment file(s)>
> 
>     Options and Arguments:
>           -o, --out_dir     Output directory [.]
>           -s, --seed        Random seed for down-sampling [0 aka -1]
>           -v, --verbose     Report current progress
>           -h, -?, --help    Display information about usage, options, and arguments
>           -m, --man         Display man page

The down-sampler uses a [samtools](http://www.htslib.org)-based approach (i.e., `samtools view -s`) to create an output SAM file for each SAM input.

### How we ran the down-sampler

```sh
$BIN_DIR=     # Directory containing down-sample-reads-auto.pl
$HTR_DIR=     # Directory containing HTR alignment files
$DGE_DIR=     # Directory containing DGE alignment files
$DOWN_DIR=    # Directory containing down-sampled alignment files

perl $BIN_DIR/down-sample-reads-auto.pl -o $DOWN_DIR $HTR_DIR/*.sam $DGE_DIR/*.sam
```

### Output files from the down-sampler

In our case, we have 22 output files from two runs of the down-sampler, one for each input file. They are named based on the input file's name and the fraction by which they were down-sampled:

- DGE method

        L_OLD42bp_A4-0.641.sam
        L_OLD42bp_B5-1.000.sam
        L_OLD42bp_C6-0.791.sam
        L_OLD42bp_D7-0.600.sam
        S_OLD42bp_A5-0.786.sam
        S_OLD42bp_A6-0.837.sam
        S_OLD42bp_B6-0.941.sam
        S_OLD42bp_B7-0.716.sam

- HTR method

        L_DGE_A4-0.223.sam
        L_DGE_B5-0.210.sam
        L_DGE_C6-0.225.sam
        L_DGE_D7-0.207.sam
        L_DGE_E7-0.174.sam
        L_DGE_E8-0.160.sam
        L_DGE_F1-0.535.sam
        S_DGE_A5-0.216.sam
        S_DGE_A6-0.211.sam
        S_DGE_B7-0.229.sam
        S_DGE_C7-0.214.sam
        S_DGE_D8-0.201.sam
        S_DGE_E1-0.196.sam
        S_DGE_F2-0.194.sam

## Calculating counts from SAM files

Before we can perform the differential expression analysis, we need to determine how many reads map to each reference sequence for each sample.

### Input files for the read counter

We counted reads from the [SAM files](#output-files-from-the-down-sampler) created by the down-sampler.

### About the read counting script

The `simple_counts.pl` script can be found in the [Read Counter GitHub repository](https://github.com/mfcovington/read_counter). Given a list of SAM/BAM files, this script counts the number of reads mapping to each reference sequence. For each input alignment file, an output counts file is created that contains the number of reads per sequence.

Usage information can be viewed by running the script with the `-h` flag:

```sh
perl simple_couts.pl -h
```

>     Usage:
>         perl simple_counts.pl [options] <Alignment file(s)>
>     
>     Options:
>       -o, --out_dir    Output directory [.]
>       -c, --csv        Output comma-delimited file (Default is tab-delimited)
>       -t, --threads    Number of files to process simultaneously [1]
>       -v, --verbose    Report current progress
>       -h, --help       Display this usage information

### How we ran the read counter

```sh
$BIN_DIR=      # Directory containing simple-counts.pl
$DOWN_DIR=     # Directory containing down-sampled alignment files
$COUNT_DIR=    # Directory containing read counts files

perl $BIN_DIR/simple-counts.pl -o $COUNT_DIR $DOWN_DIR/*.sam
```

### Output files from the read counter

In our case, we have 22 output files from the read counter, one for each down-sampled input file:

- DGE method

        L_OLD42bp_A4-0.641.counts
        L_OLD42bp_B5-1.000.counts
        L_OLD42bp_C6-0.791.counts
        L_OLD42bp_D7-0.600.counts
        S_OLD42bp_A5-0.786.counts
        S_OLD42bp_A6-0.837.counts
        S_OLD42bp_B6-0.941.counts
        S_OLD42bp_B7-0.716.counts

- HTR method

        L_DGE_A4-0.223.counts
        L_DGE_B5-0.210.counts
        L_DGE_C6-0.225.counts
        L_DGE_D7-0.207.counts
        L_DGE_E7-0.174.counts
        L_DGE_E8-0.160.counts
        L_DGE_F1-0.535.counts
        S_DGE_A5-0.216.counts
        S_DGE_A6-0.211.counts
        S_DGE_B7-0.229.counts
        S_DGE_C7-0.214.counts
        S_DGE_D8-0.201.counts
        S_DGE_E1-0.196.counts
        S_DGE_F2-0.194.counts
