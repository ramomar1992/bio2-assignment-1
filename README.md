# Assignment 1 - Quality Control, Trimming and Alignment

## Questions

In this assignment you will work on applying NGS preprocessing steps to a toy dataset example.
Dataset is available on Phi server at the following path:/BioII_Assignments/Assignment1

### 1. Examine the Fastq files available in the directory and answer the following questions

#### a. Is this a single-end or paired end experiment? How did you know?

Answer:- It is a paired end sequencing. I know it because there are two files for the same sequence, the first has the suffix _R2 the other one has the same name with the suffix_R2. Also, the ids of each read are identical in parallel.

#### b. What is the read length?

Answer:- The read length is 76 bp

#### c. How many sequences do we have?

Answer:- There are 10000 sequences for each file.

### 2. Perform quality control on the samples by running fastqc program? What are the potential issues with this dataset? Attach the report to your answer and discuss each point from the fastqc report

Answer: After running the command:

```bash
fastqc -o "./out_fastqc" Sample_R2.fastq Sample_R2.fastq
```

The output is a report with the following information:

- [Sample_R1_fastqc](out_fastqc/Sample_R1_fastqc.html):
  - We can clearly see that there is a contamination with Illumina Adapters which is clearly noticed when we look at the Overrepresented Portion of the analysis.
  - This overrepresentation is presented on the second half of the read e.g. 37 - 65 .
  - Also when we look at the Per base sequence quality, the report indicates a red flag on the box plot it drew. The average quality of the sequence is getting worse as we approach the left portion of the plot which indicates where the sequencing is failing. However, looking at other charts and reports in the analysis we can see that the overall quality is somehow good for other indices for example: per tile quality score and per sequence quality score as well.
  - Per base sequence content gives us a warning because there is some mismatch of the nucleotide content especially in the early and late positions of sequencing.
  - The CG content report also gives a warning, we can notice a fluctuations in the histogram
  - All other reports passes successfully.
- [Sample_R2_fastqc](out_fastqc/Sample_R2_fastqc.html):
  - Shows a better read quality for all metrics.
  - Per base N content shows a warning, where there is a warning. Which might occurs due to some RNA sequences that escapes the wash.
  - Also the adapter content is giving a warning.

### 3. Run TrimGalore program to perform fastqc preprocessing use trim_galore command. Examine the output of the program, what do you notice?

After running the following commands to trim both files in parallel:

```bash
trim_galore --paired --quality 32 --phred33 --fastqc --fastqc_args "--outdir './post_trim/fastqc'" --output_dir "./post_trim" --illumina --dont_gzip --length 25 --max_n 10 "./init_fasta/Sample_R1.fastq" "./init_fasta/Sample_R2.fastq"
```

The summary of the operation after using trim_galore is:

1. [Sample_R1](post_trim/Sample_R1.fastq_trimming_report.txt)
2. [Sample_R2](post_trim/Sample_R2_val_2.fq)

```txt
Sample_R1
Total reads processed:                  10,000
Reads with adapters:                     3,818 (38.2%)
Reads written (passing filters):        10,000 (100.0%)

Total basepairs processed:       760,000 bp
Quality-trimmed:                 169,585 bp (22.3%)
Total written (filtered):        574,614 bp (75.6%)

10000 sequences processed in total
Sequences removed because they became shorter than the length cutoff of 25 bp: 980 (9.8%)
Sequences removed because they contained more Ns than the cutoff of 10: 0 (0.0%)
--------------------------------------------------------

Sample_R2
Total reads processed:                  10,000
Reads with adapters:                     3,874 (38.7%)
Reads written (passing filters):        10,000 (100.0%)

Total basepairs processed:       760,000 bp
Quality-trimmed:                  84,815 bp (11.2%)
Total written (filtered):        653,843 bp (86.0%)

10000 sequences processed in total
Sequences removed because they became shorter than the length cutoff of 25 bp: 909 (9.1%)
Sequences removed because they contained more Ns than the cutoff of 15: 0 (0.0%)
```

### 4. Re-run FastQC and make sure that all issues have been resolved

1. [Sample_R1_trimmed_fastqc](post_trim/fastqc/Sample_R1_val_1_fastqc.html)
   - The quality of the reads is good.
   - All previois warning has gone and a very good base per sequence quality score is shown.
   - A new fail appears for Per pase sequence content, which is due to the cut low-quality bases.
   - A warning appears for the distribution of the sequence length which is due to removing sequences less than 25 bp after trinning.

2. [Sample_R2_trimmed_fastqc](post_trim/fastqc/Sample_R2_val_2_fastqc.html)
   - The quality of the reads is good.
   - All previois warning has gone and a very good base per sequence quality score is still shoing
   - A warning appears for the distribution of the sequence length which is due to removing sequences less than 25 bp, ot have n content more than 15 bp after trinning.
   - Per sequence GC content gives a warning as well. Howerver, this is not a problem as the GC content is not a concern for the analysis.

### 5. Once the sequences are cleaned, it is now time to align sequences to reference. You will use Bowtie2 program to perform the task

#### a. Build an index for the reference genome miniReference.fasta. It is in the same directory assignment. Hint: use bowtie-build command

Using this command I built the index reference:

```bash
bowtie2-build miniReference.fasta sample_index
```

And the output is in the [folded here.](./allignment)

#### b. Align the experiment to the reference genome using bowtie2 command

Using this command we created the `.sam` file that represent the allignment of the reads to the reference genome:

```bash
bowtie2 --no-unal -x sample_index -1 "./post_trim/Sample_R1_val_1.fq" -2 "./post_trim/Sample_R2_val_2.fq" -S alligned_sample.sam
```

The output file is [here.](./allignment/alligned_sample.sam)

### 6. The last part of the assignment is to perform operations on the alignment file. You will use samtools and pysam python package

#### a. Using samtools covert the bowtie output from SAM to BAM format

Using this command we converted the `.sam` file to `.bam` file:

```bash
samtools view -b -o "./allignment/converted_to_bam.bam" "./allignment/alligned_sample.sam"
```

The output file is [here.](./allignment/converted_to_bam.bam)

#### b. Sort and index the result BAM file
