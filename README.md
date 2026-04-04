# Beyond ABCB1: A Coordinated Transporter-Centered Transcriptional Program Underlies Acquired Paclitaxel Resistance in Ovarian Cancer

*Homework 3 — Progress Report*

---

## Response to HW2 Feedback

HW2 was positively received for the clarity of its introduction, methodology, and figure explanations. Key feedback items and my responses are summarized below.

**Feedback — Integration of figures:** Peer reviewers suggested integrating actual figures rather than placeholder templates to more effectively bridge the biological questions and analytical results. In HW3, the volcano plots and UMAP plots produced directly from the R script are presented as the preliminary figures for both OVCAR3 and TOV21G comparisons.

**Repository update:** The GitHub repository now includes comprehensive documentation of data acquisition and the DE analysis workflow. The GEO2R-derived R script has been cleaned, annotated, and stored at `script/DE_analysis.R`. Output files have been moved to `output/` to maintain a clean separation from raw inputs in `data/`.

---

## Introduction

Paclitaxel resistance remains one of the most clinically significant obstacles to durable treatment responses in ovarian cancer. A well-characterized mechanism underlying this resistance is the transcriptional upregulation of ATP-binding cassette (ABC) drug efflux transporters, most notably ABCB1 (encoding P-glycoprotein, P-gp), which reduces intracellular accumulation of taxanes and thereby diminishes cytotoxic efficacy (Gottesman et al., 2002). Experimental studies in ovarian cancer cell lines have demonstrated that both forced and acquired ABCB1 overexpression is sufficient to confer paclitaxel resistance, and that pharmacologic or genetic inhibition of P-gp can partially restore drug sensitivity, supporting a causal role for transporter-mediated efflux in resistance phenotypes (Kavallaris et al., 1997; Patch et al., 2015).

Despite extensive focus on ABCB1, an important mechanistic question remains unresolved: does paclitaxel resistance arise solely from the upregulation of ABCB1, or does ABCB1 induction reflect a broader, coordinated transcriptional state involving multiple ABC transporters and inorganic ion transport genes that collectively define the resistant phenotype? Chemotherapeutic stress is known to activate shared transcriptional regulators, including NRF2, YAP/TAZ, and AP-1, which can simultaneously induce drug efflux transporters (e.g., ABCB1, ABCC1, ABCG2) and ion transport systems that influence membrane potential, intracellular pH, and apoptotic threshold. If resistance is driven by such shared upstream control, one would expect to observe coordinated transcriptional activation of transporter and ion transport gene modules — rather than isolated induction of ABCB1 — across resistant samples.

Several primary studies suggest that other ABC transporters (e.g., ABCC1, ABCG2) and ion transport systems can independently influence chemoresistance by modulating drug distribution, membrane electrochemistry, and apoptotic signaling (Borst & Elferink, 2002; Szakács et al., 2006). However, these components are typically studied in isolation, and their coordinated transcriptional regulation in paclitaxel-resistant ovarian cancer has not been systematically evaluated at the transcriptome-wide level.

To address this gap, this project performs a secondary analysis of publicly available RNA sequencing data from paclitaxel-sensitive and paclitaxel-resistant ovarian cancer cell lines. Rather than reproducing figures from the original study, the analysis re-interrogates the dataset to test whether paclitaxel resistance is associated with a coherent, transporter-centered transcriptional program. By integrating ABC transporter and inorganic ion transport gene sets with differential expression, pathway enrichment, and co-expression network analyses, this work aims to move beyond gene lists toward a functional, systems-level characterization of the resistance machinery.

---

## Hypothesis

Acquired paclitaxel resistance in ovarian cancer cells is characterized by a coordinated transcriptional program involving multiple ABC transporters and inorganic ion transport genes, rather than the isolated overexpression of ABCB1 alone. Specifically, I propose that paclitaxel-resistant cells exhibit the joint, co-regulated activation of ATP-dependent drug efflux pathways and ion transport programs — operationalized as correlated expression changes across resistant samples — consistent with shared upstream transcriptional control rather than stochastic, gene-by-gene induction.

---

## Nature of the Data

This project uses publicly available bulk RNA sequencing data from the NCBI Gene Expression Omnibus (GEO; Edgar et al., 2002) that profile ovarian cancer cell lines under paclitaxel-sensitive and paclitaxel-resistant conditions. The primary dataset analyzed is GSE172016 (McCorkle et al., 2021), which includes RNA-seq profiles from TOV-21G and OVCAR3 cell lines following repeated paclitaxel exposure to generate stable resistant derivatives. The dataset contains gene-level count matrices and associated metadata describing treatment conditions and biological replicates, making it well suited for secondary differential expression and pathway-level analyses. No additional data collection or patient recruitment is required, and all data are publicly accessible without restriction.

---

## Materials and Methods
 
### Data Acquisition and Differential Expression Analysis
 
I used the publicly available, pre-processed gene-level RNA-seq count data from the Gene Expression Omnibus (GEO) dataset GSE172016 (McCorkle et al., 2021). This dataset focuses on paclitaxel-sensitive (S) and paclitaxel-resistant (R) ovarian cancer cell lines (OVCAR3 and TOV21G) and required no initial raw read alignment or preprocessing.
 
Differential expression (DE) analysis was performed using a custom R script (`script/DE_analysis.R`), adapted and extended from the GEO2R-generated base code. GEO2R was used for exploratory analysis and to generate an initial code framework; this was then modified, documented, and stored as a versioned script. The script implements the DESeq2 package in R/Bioconductor and takes as input the raw gene-level count matrix and annotation file downloaded directly from GEO. We performed two specific comparisons:
 
- TOV21G**S** vs TOV21G**R**
- OVCAR3**S** vs OVCAR3**R**
 
The analysis produced a DE results table stored at `output/GEO2R_processed_count_matrix.tsv` (moved from `data/` per feedback), which contains normalized expression, log₂ fold change (LFC), p-values, and FDR-adjusted p-values. A positive LFC indicates higher gene expression in the paclitaxel-sensitive samples. This table serves as the basis for all downstream visualization and pathway-level analyses.
 
---
 
### Step-by-Step Description
 
#### Data Acquisition
 
RNA-sequencing count data were obtained from the National Center for Biotechnology Information (NCBI) Gene Expression Omnibus (GEO) database under accession GSE172016. All downstream processing is performed in R version 4.2.2 using the packages Biobase 2.58.0, GEOquery 2.66.0, DESeq2, and data.table. The complete, annotated R script is saved at `script/DE_analysis.R` in the repository.
 
The raw gene-level count matrix (Input file 1) and human gene annotation file (Input file 2) corresponding to GRCh38.p13 were downloaded programmatically from GEO:
 
```r
urld <- "https://www.ncbi.nlm.nih.gov/geo/download/?format=file&type=rnaseq_counts"
path <- paste(urld, "acc=GSE172016",
              "file=GSE172016_raw_counts_GRCh38.p13_NCBI.tsv.gz",
              sep = "&")
 
tbl <- as.matrix(data.table::fread(path, header = TRUE,
                                   colClasses = "integer"),
                 rownames = "GeneID")
 
apath <- paste(urld, "type=rnaseq_counts",
               "file=Human.GRCh38.p13.annot.tsv.gz",
               sep = "&")
 
annot <- data.table::fread(apath, header = TRUE, quote = "",
                           stringsAsFactors = FALSE, data.table = FALSE)
rownames(annot) <- annot$GeneID
```
 
#### Sample Selection and Group Assignment
 
Samples used for the analysis were defined using the GEO2R-generated sample membership string. In this string, samples marked with `"X"` were excluded, while `"0"` and `"1"` denote TOV21GS and TOV21GR, respectively.
 
```r
gsms <- "XXXXXX000111XXXXXXX"
sml  <- strsplit(gsms, split = "")[[1]]
 
sel  <- which(sml != "X")
sml  <- sml[sel]
tbl  <- tbl[, sel]
 
gs     <- factor(sml)
groups <- make.names(c("TOV21GS", "TOV21GR"))
levels(gs) <- groups
 
sample_info <- data.frame(Group = gs,
                          row.names = colnames(tbl))
```
 
#### Gene Filtering
 
Before differential expression analysis, low-count genes were removed to reduce noise and improve statistical reliability. Genes were retained only if they had a count of at least 10 in a number of samples greater than or equal to the size of the smallest experimental group.
 
```r
keep <- rowSums(tbl >= 10) >= min(table(gs))
tbl  <- tbl[keep, ]
```
 
#### Differential Expression Analysis
 
Differential expression analysis was performed using the DESeq2 package. A `DESeqDataSet` object was first constructed from the filtered count matrix and the sample metadata, with experimental group specified as the design variable. Normalization, dispersion estimation, and model fitting were then carried out using the standard DESeq2 workflow with the Wald test and `poscount` size factor estimation.
 
```r
ds <- DESeqDataSetFromMatrix(countData = tbl,
                             colData   = sample_info,
                             design    = ~Group)
 
ds <- DESeq(ds, test = "Wald", sfType = "poscount")
 
r <- results(ds,
             contrast      = c("Group", groups[1], groups[2]),
             alpha         = 0.05,
             pAdjustMethod = "fdr")
```
 
The top differentially expressed genes were ranked by adjusted p-value, and the top 250 entries were merged with the annotation table to add gene symbols and gene descriptions. The final table was written to `output/`:
 
```r
tT <- r[order(r$padj)[1:250], ]
tT <- merge(as.data.frame(tT), annot, by = 0, sort = FALSE)
 
write.table(tT, file = "output/GEO2R_processed_count_matrix.tsv",
            sep = "\t", row.names = FALSE, quote = FALSE)
```
 
The final output table includes the following columns: `GeneID`, `padj`, `pvalue`, `lfcSE`, `stat`, `log2FoldChange`, `baseMean`, `Symbol`, and `Description`.
 
---
 ### Transporter- and Ion-Focused Gene Set Definition

To test the coordination hypothesis, two a priori gene sets will be defined:

- **ABC transporter set:** All human ABCA–ABCG family members, curated from HGNC and cross-referenced with Reactome pathway R-HSA-382556 (Borst & Elferink, 2002).
- **Inorganic ion transport set:** Genes annotated to Gene Ontology terms GO:0015075 ("ion transmembrane transporter activity") and GO:0098660 ("inorganic ion transmembrane transport"), filtered to remove those also classified as organic solute transporters.

### Planned Pathway Enrichment Analysis

Pathway enrichment analysis will be performed using the `enrichR` package in R (Kuleshov et al., 2016), querying multiple curated gene set libraries simultaneously including GO Biological Process 2023, KEGG 2021 Human, Reactome 2022, and MSigDB Hallmark 2020. The input gene list will be the set of significantly upregulated genes in resistant cells (padj < 0.05, log2FC < −1). Results will be filtered by adjusted p-value < 0.05 and ranked by combined score. This analysis is described in detail in the Next Steps section.

### Planned Network-Level Contextualization

Significantly differentially expressed transporter and ion transport genes will be mapped onto the STRING protein interaction network (Szklarczyk et al., 2021) using a minimum interaction confidence score of 0.7. Hub nodes (high degree centrality) connecting drug efflux and ion transport subnetworks will be flagged as candidate leverage points for future functional validation.

---

## Results
<img width="1125" height="824" alt="image" src="https://github.com/user-attachments/assets/800025ad-231a-4678-a454-6c362484a269" />

**Figure 1. Differential expression landscape and global transcriptomic separation in paclitaxel-sensitive vs. resistant ovarian cancer cell lines.** Left panels: volcano plots for OVCAR3 (top) and TOV21G (bottom). Right panels: UMAP plots for OVCAR3 (top) and TOV21G (bottom). In volcano plots, each point represents one gene; blue = significantly downregulated in resistant (padj < 0.05); red = significantly upregulated in resistant (padj < 0.05); gray = not significant. The x-axis shows log2 fold change (sensitive vs. resistant); the y-axis shows -log10(adjusted p-value, FDR-corrected by Benjamini-Hochberg). In UMAP plots, each point represents one biological replicate (n = 3 per group); green = sensitive, purple = resistant. UMAP was computed on log10(normalized counts + 1) with n_neighbors = 3, random_state = 123.

### Global Transcriptomic Separation Between Sensitive and Resistant Cells (UMAP)

To assess whether the transcriptomes of sensitive and resistant cells are globally distinguishable, UMAP dimensionality reduction was applied to log10-normalized count data across all expressed genes for both cell line comparisons.

In both the OVCAR3 and TOV21G comparisons, the UMAP plots show complete, non-overlapping separation between the sensitive and resistant sample groups. Each condition forms a tight cluster of three replicates, indicating high within-group reproducibility. In the OVCAR3 comparison, sensitive samples (OV3_S) cluster in the upper-left region while resistant samples (OV3_R) cluster in the lower-right, with separation along both UMAP dimensions. The TOV21G comparison shows an equally striking separation: sensitive replicates (tov_21gS) group tightly in the lower-left while resistant replicates (tov21g_R) cluster in the upper-right corner of the UMAP space.

This clear inter-group separation in both cell lines confirms that acquired paclitaxel resistance is associated with a broad, reproducible, genome-wide transcriptional shift rather than a subtle or stochastic perturbation. Critically, the consistency of this pattern across two independently derived resistant lines supports the existence of a shared, convergent transcriptional resistance state — precisely the kind of structured subprogram this project aims to characterize.

### Differential Expression Landscape Reveals Extensive, Asymmetric Transcriptional Remodeling (Volcano Plots)

The volcano plots reveal the genome-wide landscape of differential expression in both comparisons and highlight several important features of the resistance transcriptome.

In the **OVCAR3** comparison (top left), the distribution of significant genes (padj < 0.05) is notably asymmetric. A large cloud of blue (downregulated in resistant) genes extends to large negative log2FC values (approximately −6 to −7), while red (upregulated in resistant) genes reach moderately large positive values (up to approximately +6). The -log10(padj) axis exceeds 250 for the most significant genes, indicating extremely high statistical confidence in a subset of strongly regulated loci. This pattern suggests that resistance in OVCAR3 involves both broad transcriptional repression and targeted transcriptional activation.

In the **TOV21G** comparison (bottom left), the volcano plot reveals an even wider dynamic range of fold changes, with significant genes spanning log2FC values from approximately −10 to +12. The upregulated (red) genes in TOV21G extend further to the right than in OVCAR3, suggesting that the TOV21G resistant line may have acquired more extreme transcriptional activation of specific target genes, possibly including ABCB1 and related transporters. Both comparisons share a dense core of significant genes near the center flanked by extreme outliers representing the most strongly and specifically dysregulated genes.

Taken together, these volcano plots demonstrate that paclitaxel resistance involves large-scale transcriptional remodeling across both cell lines, well beyond a single-gene or single-pathway effect. The next analytical step — overlaying ABC transporter and inorganic ion transport gene sets onto these plots — will determine whether transporter genes are systematically enriched among the most strongly upregulated genes in the resistant condition.



---

## Next Steps

### 1. Pathway Enrichment Analysis with Enrichr in R

The most important and immediate next step is pathway enrichment analysis using the `enrichR` R package (Kuleshov et al., 2016). While the volcano plots clearly demonstrate the scale of transcriptional remodeling associated with resistance, they do not by themselves reveal which biological processes, molecular pathways, or upstream regulators are driving these changes. Enrichr addresses this directly by asking: among all the genes significantly upregulated in resistant cells, which annotated biological pathways are statistically over-represented beyond what would be expected by chance?

This analysis is central to the project for several interconnected reasons. First, if the coordination hypothesis is correct — that resistance is driven by a coherent transporter-centered transcriptional program — then enrichment analysis should return significant hits for ABC transporter pathways, drug efflux gene sets, and ion transport biology at the top of the ranked results. The presence of these terms would constitute the first pathway-level evidence that the observed transcriptional remodeling is not random but structured around a functionally coherent resistance module. Second, Enrichr simultaneously queries dozens of curated gene set libraries (GO Biological Process, KEGG, Reactome, MSigDB Hallmarks, and others), enabling the resistance transcriptome to be interpreted across multiple biological contexts in a single analysis. This is particularly valuable for detecting shared upstream regulators — such as NRF2, YAP/TAZ, or AP-1 — whose gene signatures may appear in the enrichment results even if those transcription factors are not themselves differentially expressed. Third, running Enrichr independently on the OVCAR3 and TOV21G upregulated gene lists and then comparing the resulting enriched pathway sets will reveal whether a common resistance-associated transcriptional signature is conserved across cell lines, which would greatly strengthen the generalizability and biological relevance of the findings.

Results will be filtered by adjusted p-value < 0.05 and ranked by Enrichr combined score (−log p-value × z-score of deviation from expected rank). The top enriched terms from each database will be visualized as a dot plot or bar chart displaying pathway name, gene count, combined score, and adjusted p-value, and saved to `output/`.


---

## Repository Structure

The repository is hosted at `atonu-chakrabortty-biol4386` and is organized as follows, reflecting the actual current state of the GitLab repository:

```
atonu-chakrabortty-biol4386/
│
├── data/                                         # Raw and processed input data
│   ├── .gitkeep
│   ├── GSE172016_OV3S_R.top.table.tsv           # GEO2R DE results: OVCAR3 Sensitive vs Resistant
│   └── GSE172016tov21gS_R.top.table.tsv         # GEO2R DE results: TOV21G Sensitive vs Resistant
│
├── figures/                                      # Output figures for report
│   ├── .gitkeep
│   ├── OV3_SvsOV3_R_PCA.jpg                    # UMAP/PCA plot: OVCAR3 S vs R
│   ├── OV3_SvsOV3_R_VolcanoPlot.jpg            # Volcano plot: OVCAR3 S vs R
│   ├── tov21gSvsR_PCA.jpg                       # UMAP/PCA plot: TOV21G S vs R
│   └── tov21gSvsR_VolcanoPlot.jpg              # Volcano plot: TOV21G S vs R
│
├── output/                                       # Analysis output tables
│   ├── GSE172016_OV3S_R.top.table.tsv          # Top DE genes: OVCAR3 comparison
│   └── GSE172016tov21gS_R.top.table.tsv        # Top DE genes: TOV21G comparison
│
└── script/                                       # Analysis scripts
    └── DE_analysis.R                            # Full DESeq2 + visualization script
```

### Data Directory Details

The `data/` folder contains the processed differential expression count matrices generated via GEO2R for both cell line comparisons. These files were produced using the online GEO2R tool provided by NCBI, which applies DESeq2 to the deposited raw gene-level count data from GSE172016. Each file is a tab-separated table where each row corresponds to a gene and columns include:

| Column | Description |
|--------|-------------|
| `GeneID` | NCBI Entrez Gene ID |
| `Symbol` | HGNC gene symbol |
| `Description` | Gene description |
| `baseMean` | Mean normalized expression across all samples |
| `log2FoldChange` | Log2 fold change (positive = higher in sensitive) |
| `lfcSE` | Standard error of the log2 fold change |
| `stat` | Wald test statistic |
| `pvalue` | Nominal p-value |
| `padj` | Benjamini-Hochberg FDR-adjusted p-value |

- **`GSE172016_OV3S_R.top.table.tsv`** — DE results for OVCAR3 paclitaxel-sensitive (OV3_S) vs. OVCAR3 paclitaxel-resistant (OV3_R). Negative log2FoldChange values indicate genes upregulated in the resistant condition.
- **`GSE172016tov21gS_R.top.table.tsv`** — DE results for TOV21G paclitaxel-sensitive (tov_21gS) vs. TOV21G paclitaxel-resistant (tov21g_R). Same column structure and LFC directionality convention.

### Figures Directory Details

The `figures/` folder contains the four preliminary output plots generated from `script/DE_analysis.R`, presented as the preliminary results in this report:

- **`OV3_SvsOV3_R_VolcanoPlot.jpg`** — Volcano plot for the OVCAR3 comparison, colored by significance and direction (padj < 0.05).
- **`OV3_SvsOV3_R_PCA.jpg`** — UMAP dimensionality reduction plot for OVCAR3, showing sample-level separation between sensitive and resistant groups.
- **`tov21gSvsR_VolcanoPlot.jpg`** — Volcano plot for the TOV21G comparison.
- **`tov21gSvsR_PCA.jpg`** — UMAP plot for TOV21G, confirming tight within-group clustering and complete inter-group separation.

### Output Directory Details

The `output/` folder mirrors the DE results tables from `data/` and will accumulate additional downstream analysis outputs in future steps, including Enrichr pathway enrichment results and VST-normalized per-sample count matrices (planned).

### Script Directory Details

- **`script/DE_analysis.R`** — The full, annotated DESeq2 analysis script adapted from the GEO2R-generated base code. Takes as input the raw count matrix and annotation file downloaded directly from GEO (GSE172016), performs sample selection, gene filtering, differential expression analysis, and generates the volcano plots, UMAP plots, boxplots, and dispersion diagnostic plots. Outputs are written to `output/` and `figures/`.

---

## AI Use Disclosure

I utilized Perplexity AI to gain a technical understanding of the GEO2R-generated R script, specifically focusing on how it integrates designated sample groups and formatting the markdown document. 
---

## References

Barbie, D. A., Tamayo, P., Boehm, J. S., Kim, S. Y., Moody, S. E., Dunn, I. F., ... & Hahn, W. C. (2009). Systematic RNA interference reveals that oncogenic KRAS-driven cancers require TBK1. *Nature*, 462(7269), 108–112.

Borst, P., & Elferink, R. O. (2002). Mammalian ABC transporters in health and disease. *Annual Review of Biochemistry*, 71, 537–592.

Edgar, R., Domrachev, M., & Lash, A. E. (2002). Gene Expression Omnibus: NCBI gene expression and hybridization array data repository. *Nucleic Acids Research*, 30(1), 207–210.

Gottesman, M. M., Fojo, T., & Bates, S. E. (2002). Multidrug resistance in cancer: role of ATP-dependent transporters. *Nature Reviews Cancer*, 2(1), 48–58.

Kavallaris, M., Kuo, D. Y., Burkhart, C. A., Regl, D. L., Norris, M. D., Haber, M., & Bhalla, K. N. (1997). Taxol-resistant epithelial ovarian tumors are associated with altered expression of specific beta-tubulin isotypes. *Journal of Clinical Investigation*, 100(5), 1282–1293.

Kuleshov, M. V., Jones, M. R., Rouillard, A. D., Fernandez, N. F., Duan, Q., Wang, Z., ... & Ma'ayan, A. (2016). Enrichr: a comprehensive gene set enrichment analysis web server 2016 update. *Nucleic Acids Research*, 44(W1), W90–W97.

Love, M. I., Huber, W., & Anders, S. (2014). Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. *Genome Biology*, 15(12), 550.

McCorkle, J. R., Gorski, J. W., Liu, J., Riggs, M. B., McDowell, A. B., et al. (2021). Lapatinib and poziotinib overcome ABCB1-mediated paclitaxel resistance in ovarian cancer. *PLOS ONE*, 16(8): e0254205.

Patch, A. M., Christie, E. L., Etemadmoghadam, D., Garsed, D. W., George, J., Fereday, S., ... & Bowtell, D. D. (2015). Whole-genome characterization of chemoresistant ovarian cancer. *Nature*, 521(7553), 489–494.

Subramanian, A., Tamayo, P., Mootha, V. K., Mukherjee, S., Ebert, B. L., Gillette, M. A., ... & Mesirov, J. P. (2005). Gene set enrichment analysis: A knowledge-based approach for interpreting genome-wide expression profiles. *Proceedings of the National Academy of Sciences*, 102(43), 15545–15550.

Szakács, G., Paterson, J. K., Ludwig, J. A., Booth-Genthe, C., & Gottesman, M. M. (2006). Targeting multidrug resistance in cancer. *Nature Reviews Drug Discovery*, 5(3), 219–234.

Szklarczyk, D., Gable, A. L., Nastou, K. C., Lyon, D., Kirsch, R., Pyysalo, S., ... & Jensen, L. J. (2021). The STRING database in 2021: customizable protein-protein networks, and functional characterization of user-uploaded gene/measurement sets. *Nucleic Acids Research*, 49(D1), D605–D612.
