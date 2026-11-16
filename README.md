# Yeast RNA-seq Alignment and RNA Biotype Analysis

## Project overview
This project explores alignment and gene-level read assignment for a publicly available *Saccharomyces cerevisiae* sequencing run (SRA: SRR39018106). The run is from a specialized nuclease-resistant disome experiment, not a conventional bulk mRNA-seq experiment. The goal is to practice a reproducible workflow and interpret its limitations, rather than infer differential gene expression.

## Data and reference
- Sequencing run: **SRR39018106**, Illumina NextSeq 500, single-end layout (according to the SRA record examined during the project).
- Working dataset: **9,872 reads** in `SRR39018106_clean.fastq`. This is a small working subset; the full SRA run contains approximately 5.49 million spots.
- Reference: *S. cerevisiae* R64-1-1 genome FASTA.
- Annotation: Ensembl release 115 GTF (`Saccharomyces_cerevisiae.R64-1-1.115.gtf.gz`).
- Tools used: HISAT2, samtools, featureCounts (Subread v2.1.1), awk, and standard shell commands.

## Workflow
1. Inspect sequencing metadata and prepare the FASTQ working file.
2. Prepare the yeast reference genome and HISAT2 index.
3. Align single-end reads using HISAT2 with `--very-sensitive`.
4. Convert the SAM output to BAM, sort and index it with samtools.
5. Check alignment statistics using `samtools flagstat`.
6. Count reads by annotated genes with featureCounts and compare strand settings (`-s 0`, `-s 1`, `-s 2`).
7. Restrict interpretation to primary alignments and summarize gene biotypes and tRNA counts.

## Key results
| Metric | Result |
|---|---:|
| Reads analyzed | 9,872 |
| HISAT2 overall alignment rate | 99.42% |
| Reads aligned exactly once | 5,748 (58.23%) |
| Reads aligned more than once | 4,067 (41.20%) |
| Unaligned reads | 57 (0.58%) |
| Primary mapped reads | 9,815 |
| Gene-assigned counts, primary BAM, selected strand setting | 3,785 |
| Genes with at least one assigned count | 147 |
| Assigned counts attributed to tRNA | 3,764 (99.45%) |
| Assigned counts attributed to protein-coding genes | 19 (0.50%) |
| Other assigned counts | 2 (0.05%) |
| Annotated tRNA genes with assigned reads | 131 / 299 (43.81%) |
| Top 10 tRNA gene counts | 2,686 / 3,764 (71.36%) |

The selected primary-alignment counting result matches the previously tested `-s 1` setting (3,785 assigned). The strand setting should be checked against the experiment's library protocol before treating it as validated.

## Interpretation
Most assigned reads in this working subset fall on tRNA annotations, with the top ten tRNA genes accounting for 71.36% of assigned tRNA counts. This is an observation about **assigned counts**, not proof that these genes are the most highly expressed transcripts in the biological sample. A high alignment rate also does not guarantee a high gene-assignment rate. Multi-mapping, the annotation, library preparation, strand settings, and read-selection methods all affect counts.

## Limitations
- The working dataset is a small subset and may not represent the full experiment.
- This is a specialized disome sequencing library, not standard bulk RNA-seq.
- Approximately 41.20% of reads align to multiple locations; assigning such reads to individual tRNA genes is uncertain.
- Primary-alignment filtering removes secondary BAM records but does **not** resolve multi-mapping ambiguity: primary records can still represent multi-mapping reads. featureCounts default multi-mapping exclusion is important.
- `samtools flagstat` on the unfiltered BAM includes secondary alignment records and therefore reports more records than input reads; use the primary BAM for read-level comparisons.
- No biological replicates, differential-expression analysis, or validated expression normalization were performed.

## Conclusion
The exercise successfully demonstrates reference preparation, HISAT2 alignment, BAM processing, gene-level counting, and RNA biotype interpretation. It also illustrates why alignment percentage and gene-assignment percentage answer different questions. The project ends at descriptive analysis; no further biological conclusions are claimed.

## References and data resources
- NCBI Sequence Read Archive: SRR39018106, https://www.ncbi.nlm.nih.gov/sra/?term=SRR39018106
- Ensembl release 115 yeast annotation, https://ftp.ensembl.org/pub/release-115/gtf/saccharomyces_cerevisiae/
- HISAT2 documentation, https://daehwankimlab.github.io/hisat2/
- featureCounts / Subread documentation, https://subread.sourceforge.net/
- SAMtools documentation, http://www.htslib.org/doc/

## Reproducibility note
Results above were transcribed from the terminal outputs documented during the project. For a fully reproducible release, add the exact commands and software environment to the repository; do not claim the entire 5.49-million-spot run was processed.
