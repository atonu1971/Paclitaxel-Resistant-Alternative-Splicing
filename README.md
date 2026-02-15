## Introduction

Ovarian cancer remains the most lethal gynecologic malignancy due to the frequent emergence of resistance to taxane–platinum chemotherapy. Paclitaxel resistance is commonly attributed to transcriptional upregulation of *ABCB1* and alterations in beta-tubulin isoforms. However, bulk RNA sequencing analyses typically measure total gene expression and overlook alternative splicing (AS), which can generate functionally distinct isoforms without changing overall transcript abundance.

Cytotoxic stress from chronic paclitaxel exposure may disrupt spliceosome activity, leading to exon skipping (SE) and intron retention (IR). Although *ABCB1* overexpression has been documented in the GSE172016 dataset (McCorkle et al., 2021), the contribution of coordinated alternative splicing programs to resistance has not been systematically evaluated.

This project will quantify differential gene expression using Sleuth and identify alternative splicing events using rMATS ($\Delta \Psi \geq 10\%$, FDR < 0.05). I will test whether resistant cells show enrichment of specific splicing event types, particularly within ABC transporters and cytoskeletal regulators, to distinguish transcriptional upregulation from isoform-level remodeling in paclitaxel resistance.

---

## Materials and Methods

McCorkle et al. generated paclitaxel-resistant ovarian cancer models by repeatedly treating ovarian cancer cell lines (TOV-21G and OVCAR3) with 48-hour paclitaxel cycles until stable resistance emerged. For transcriptomic profiling, they performed bulk RNA-seq on triplicate samples per cell line condition.

**Article Source:**  
McCorkle JR, Gorski JW, Liu J, Riggs MB, McDowell AB, et al. (2021)  
*Lapatinib and poziotinib overcome ABCB1-mediated paclitaxel resistance in ovarian cancer.*  
PLOS ONE 16(8): e0254205.  
https://doi.org/10.1371/journal.pone.0254205

**Primary Data Source:**  
NCBI GEO accession **GSE172016** (RNA-seq for control vs paclitaxel-resistant ovarian cancer models).  
The paper states that the RNA-seq data are deposited in GEO.

**Expected Data Types:**
- FASTQ files (raw sequencing reads), and/or  
- Processed gene count tables and metadata (GEO series matrix or supplementary files)

---

## Expected Outcomes

This workflow should reproduce gene-expression-based separation of resistant and sensitive samples (PCA, volcano plot, heatmap) and extend the analysis by identifying resistance-associated alternative splicing programs (event summary, splicing volcano, and sashimi validation). If strong *ABCB1* differential alternative splicing event is confirmed, the project will test whether splicing changes occur in ABC transporters, cytoskeletal genes, or RNA-processing regulators as an additional layer of adaptation. 

A preliminary feasibility check indicates that all required software packages and pipelines (Sleuth, rMATS, STAR, FastQC, MultiQC, Trimmomatic) are readily available and well-documented. The latest stable versions of each software will be used.

---

## Workflow

- Project setup and metadata curation  
- Data download (GEO / SRA)  
- Quality control (FastQC + MultiQC)  
- Optional read trimming (Trimmomatic)  
- STAR alignment → BAM files  
- Branch 1: Gene quantification → Sleuth → PCA / Volcano / Heatmap  
- Branch 2: rMATS → Significant AS events → Splicing volcano → Sashimi plot  



