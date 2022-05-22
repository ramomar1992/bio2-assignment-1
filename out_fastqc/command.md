# commands

```bash
trim_galore --quality 32 --phred33 --fastqc --fastqc_args "--outdir './post_trim/fastqc'" --output_dir "./post_trim" --illumina --dont_gzip --length 25 --max_n 10 "./init_fasta/Sample_R1.fastq" 
```

```bash
trim_galore --quality 30 --phred33 --fastqc --fastqc_args "--outdir './post_trim/fastqc'" --output_dir "./post_trim" --illumina --dont_gzip --length 25 --max_n 15 "./init_fasta/Sample_R2.fastq" 
```
