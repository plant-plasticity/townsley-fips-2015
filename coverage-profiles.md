# Coverage profiles

<!-- MarkdownTOC -->

- [Extracting coverage data for coding and 3'-UTR sequences](#extracting-coverage-data-for-coding-and-3-utr-sequences)
    - [About the coverage profiler script](#about-the-coverage-profiler-script)
    - [Input files for the coverage profiler](#input-files-for-the-coverage-profiler)
    - [How we ran the coverage profiler](#how-we-ran-the-coverage-profiler)
    - [Output files from the coverage profiler](#output-files-from-the-coverage-profiler)
- [Plotting coverage profiles](#plotting-coverage-profiles)
    - [Choosing and manipulating coverage files to use for plotting](#choosing-and-manipulating-coverage-files-to-use-for-plotting)
    - [Plotting in R](#plotting-in-r)

<!-- /MarkdownTOC -->

## Extracting coverage data for coding and 3'-UTR sequences

### About the coverage profiler script

The `coverage-profiler.pl` script can be found in the [Coverage Profiler GitHub repository](https://github.com/mfcovington/coverage-profiler). The coverage profiler analyzes BAM alignment files to determine the relative coverage across the average gene in a transcriptome.

Usage information can be viewed by running the script with the `-h` flag:

```sh
perl coverage-profiler.pl -h
```

>     Usage: bin/coverage-profiler.pl [options] --id <Sample ID> <bam file(s)>
>     
>     Options:
>       -i, --id              Sample ID (required)
>       -l, --length_3_UTR    Length of 3'-UTR following CDS in reference [500]
>       -o, --out_dir         Output directory [.]
>       -h, --help            Display this usage information

The coverage profiler creates five files containing average coverage per position relative to total coverage (`<Sample ID>` in filename is determined using `--id`):

- `<Sample ID>.cds-abs.cov`

    > Coverage across the average coding sequence. The sequences are aligned such that the last position of each CDS corresponds to position 0.

- `<Sample ID>.utr-abs.cov`

    > Coverage across the average 3'-UTR. The 3'-UTR sequence positions are numbered from 1 to whatever value was specified by `--length_3_UTR`. In our case, 500.

- `<Sample ID>.cds-rel.cov`

    > Coverage across the average coding sequence where the CDS positions have been scaled to a value from -99 to 0.

- `<Sample ID>.utr-rel.cov`

    > Coverage across the average 3'-UTR sequence where the UTR positions have been scaled to a value from 1 to 100.

- `<Sample ID>.cds-scaled.cov`

    > Coverage across the average coding sequence where the CDS positions have been scaled to the mean CDS length (e.g., in our case, from -1,176 to 0).

### Input files for the coverage profiler

In our paper, we ran the coverage profiler on sets of BAM alignment files grouped by library preparation method:

- DGE method

        L_DGE_A4.srt.bam
        L_DGE_B5.srt.bam
        L_DGE_C6.srt.bam
        L_DGE_D7.srt.bam
        L_DGE_E7.srt.bam
        L_DGE_E8.srt.bam
        L_DGE_F1.srt.bam
        S_DGE_A5.srt.bam
        S_DGE_A6.srt.bam
        S_DGE_B7.srt.bam
        S_DGE_C7.srt.bam
        S_DGE_D8.srt.bam
        S_DGE_E1.srt.bam
        S_DGE_F2.srt.bam

- HTR method

        L_HTR_A4.srt.bam
        L_HTR_B5.srt.bam
        L_HTR_C6.srt.bam
        L_HTR_D7.srt.bam
        S_HTR_A5.srt.bam
        S_HTR_A6.srt.bam
        S_HTR_B6.srt.bam
        S_HTR_B7.srt.bam

### How we ran the coverage profiler

We used the default 3'-UTR length (500 bp), which can be customized using the `--length_3_UTR` parameter.

```sh
BIN_DIR=    # Directory containing coverage-profiler.pl
BAM_DIR=    # Directory containing BAM alignment files
OUT_DIR=    # Output directory

for METHOD in DGE HTR; do
    perl $BIN_DIR/coverage-profiler.pl --out $OUT_DIR --id $METHOD $BAM_DIR/*$METHOD*.bam
done
```

### Output files from the coverage profiler

In our case, we have ten output files from two runs of the coverage profiler. Five for the set of DGE samples and five for the set of HTR samples:

- DGE method

        DGE.cds-abs.cov
        DGE.utr-abs.cov
        DGE.cds-rel.cov
        DGE.utr-rel.cov
        DGE.cds-scaled.cov

- HTR method

        DGE.cds-abs.cov
        DGE.utr-abs.cov
        DGE.cds-rel.cov
        DGE.utr-rel.cov
        DGE.cds-scaled.cov

## Plotting coverage profiles

### Choosing and manipulating coverage files to use for plotting

### Plotting in R
