This repository documents an end-to-end bioinformatics pipeline for analyzing SARS-CoV-2 NGS data. The workflow covers raw read processing, genomic alignment, variant calling, and functional annotation to identify viral lineages and high-impact mutations.

🧬 Project Overview
The study focuses on identifying Single Nucleotide Polymorphisms (SNPs) and Insertions/Deletions (Indels) from viral sequencing data. By utilizing the Wuhan-Hu-1 (NC_045512.2) reference genome, this pipeline assesses the mutational burden and determines the specific Pango lineage of the sample.

🛠️ Tech Stack & Tools
OS: Linux (WSL2 / Ubuntu)

Environment: Conda (bioinfo environment)

QC: FastQC, MultiQC

Alignment: BWA-MEM (Burrows-Wheeler Aligner)

Processing: SAMtools, BCFtools

Annotation: SnpEff

Visual Analysis: IGV (Integrative Genomics Viewer) & Nextclade

🚀 Pipeline Workflow
1. Quality Control
Raw FASTQ reads are evaluated for quality scores and adapter contamination.

Bash
fastqc raw_data/*.fastq -o qc/
multiqc qc/ -o multi/

2. Read Mapping & Alignment
Alignment to the NC_045512.2 reference genome using BWA-MEM.

Bash
bwa mem reference.fasta read1.fastq read2.fastq | samtools view -Sb - | samtools sort -o final_sorted.bam
samtools index final_sorted.bam

3. Variant Calling & Filtering
Variants are identified using bcftools and filtered for quality.

Bash
bcftools mpileup -f reference.fasta final_sorted.bam | bcftools call -mv -Oz -o variants.vcf.gz

4. Functional Annotation
Using SnpEff to determine the biological impact (e.g., missense mutations in the Spike protein).

Bash
snpEff NC_045512.2 variants.vcf.gz > variants_annotated.vcf

5. Consensus Generation & Lineage Assignment
Reconstruction of the sample genome to determine the viral clade.

Bash
bcftools consensus -f reference.fasta variants.vcf.gz > consensus.fasta

📁 Directory Structure
Plaintext
.
├── bwamapping/       # Reference genome and BAM alignments
├── raw_data/         # Raw FASTQ sequences (SRA)
├── qc/               # FastQC reports
├── results/          # Annotated VCFs, Consensus FASTA, and plots

📊 Data Visualization
The pipeline includes IGV to visualize the distribution of Indel lengths, highlighting the difference between in-frame and frameshift mutations.
