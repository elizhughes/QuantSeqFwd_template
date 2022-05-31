# QuantSeqFwd_template

Template repository for QuantSeq Fwd analysis, for RNA-seq data.

This contains a nextflow pipeline to process from reads to counts-per-mRNA, and a directory structure for some quality control using R/Rmarkdown.

EDIT THIS README FILE to describe the dataset that you are analysing


# Contents

- quantseqfwd.nf - nextflow script for read processing and counting
- Rmd - directory R markdown scripts for data processing after the 
- input - directory for input
  - input/annotation - genome or transcriptome annotation used for read alignment and counting.
  - input/experiment - other input files including sample sheet
- results - directory for output data and results
    - results/counts - count data and analyses of those.
    - results/xxx - create specific subdirectories for other kinds of results


## Test with subsampled data

It is strongly recommended to do a test run on small yet real data - subsampled data - before running on a large dataset.
An example of subsampling fastq files to get the first 100,000 reads is:

```
gzip -dkc data_RENAME/R1_001.fastq.gz \
  | head -n 400000 | gzip > data_subsampled_RENAME/R1_001_init100000.fastq.gz
```


## How to run the pipeline

This is a nextflow (DSL 1.0) pipeline that does read QC, trimming, alignment, and counting per transcript.


## Before starting, prepare your annotation

Before starting, you have to choose and pick an annotation relevant to the species in question.  Make sure it includes 3' ends of mRNAs because QuantSeq is 3'-targeted. This annotation requires:

- a fasta file of your genome (or transcriptome)
- a hisat2 index built from your fasta file
- a gff describing positions and strands of the mRNAs within the fasta file


## Set the parameters in the nextflow file

Then edit the parameters in `quantseqfwd.nf` appropriate to your analysis:

- `params.input_fq_dir = 'EXPERIMENT_RENAME_fastq'` points to an input directory containing all of your fastq files 
- `params.output_dir = 'results/counts'` points to the output directory where you would like all the outputs stored.
- `params.index_dir = 'input/annotation/index'` points to the directory with input annotation in it
- `params.index_prefix = 'ANNID_RENAME_hisat2'` name of hisat2 index
- `params.mRNAgff = 'input/annotation/ANNID_RENAME_mRNAonly.gff'` name of gff file describing mRNA locations
- `params.featuretype = 'mRNA'` in the mRNA gff file Type column, the feature corresponding to mRNA that you want to count
- `params.featurename = 'Name'` in the mRNA gff file attributes column, the field that contains the name of the feature to use in the counts file
- `params.num_processes = 4` number of processes to use for parallelising adapter trimming and alignment. Increasing this can speed up running the pipeline on larger computers
- `params.adapters = 'AAAAAAAAAAAA'` is the sequencing adapter to remove, keep this as poly(A) for QuantSeq Fwd
- `params.hisat2_other = '-k 2 --pen-cansplice 4 --pen-noncansplice 12 --min-intronlen 40  --max-intronlen 200' other hisat2 parameters, these are specialised for aligning intron-poor yeast genomes, see hisat2 manual for details


## Run the pipeline

Then run the command:

```
nextflow run src/quantseqfwd.nf -with-dag flowchart.png -with-report nextflow_report.html
```

The options `-with-dag` and `-with-report` are just to tell you a little more about the output, they are not strictly needed in order to run.

Remember that it is best to test & run carefully with subsampled data first, to find problems quickly.


## Inspect the results

TBA


## Check the reproducibility of results using the R markdown file

TBA
