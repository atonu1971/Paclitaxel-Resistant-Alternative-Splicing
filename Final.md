# Beyond ABCB1: A Coordinated Transporter-Centered Transcriptional Program Underlies Acquired Paclitaxel Resistance in Ovarian Cancer

**Atonu Chakrabortty**

---

## 1. Introduction

Paclitaxel resistance remains one of the most clinically significant obstacles to durable treatment responses in ovarian cancer. A well-characterized mechanism underlying this resistance is the transcriptional upregulation of ATP-binding cassette (ABC) drug efflux transporters, most notably ABCB1 (encoding P-glycoprotein, P-gp), which reduces intracellular accumulation of taxanes and thereby diminishes cytotoxic efficacy (Gottesman et al., 2002). Experimental studies in ovarian cancer cell lines have demonstrated that both forced and acquired ABCB1 overexpression is sufficient to confer paclitaxel resistance, and that pharmacologic or genetic inhibition of P-gp can partially restore drug sensitivity, supporting a causal role for transporter-mediated efflux in resistance phenotypes (Kavallaris et al., 1997; Patch et al., 2015).

Despite extensive focus on ABCB1, an important mechanistic question remains unresolved: does paclitaxel resistance arise solely from the upregulation of ABCB1, or does ABCB1 induction reflect a broader, coordinated transcriptional state involving multiple ABC transporters and inorganic ion transport genes that collectively define the resistant phenotype? Chemotherapeutic stress is known to activate shared transcriptional regulators, including NRF2, YAP/TAZ, and AP-1, which can simultaneously induce drug efflux transporters (e.g., ABCB1, ABCC1, ABCG2) and ion transport systems that influence membrane potential, intracellular pH, and apoptotic threshold.

Several primary studies suggest that other ABC transporters (e.g., ABCC1, ABCG2) and ion transport systems can independently influence chemoresistance by modulating drug distribution, membrane electrochemistry, and apoptotic signaling (Borst & Elferink, 2002; Szakacs et al., 2006). However, these components are typically studied in isolation, and their coordinated transcriptional regulation in paclitaxel-resistant ovarian cancer has not been systematically evaluated at the transcriptome-wide level.

To address this gap, this project performs a secondary analysis of publicly available RNA sequencing data from paclitaxel-sensitive and paclitaxel-resistant ovarian cancer cell lines. Rather than reproducing figures from the original study, the analysis re-interrogates the dataset to test whether paclitaxel resistance is associated with a coherent, transporter-centered transcriptional program. By integrating ABC transporter and inorganic ion transport gene sets with differential expression analysis, this work aims to move beyond single gene lists toward a functional, systems-level characterization of the resistance machinery.

---

## 2. Methods

### 2.1 Data Source

RNA sequencing data were obtained from the Gene Expression Omnibus (GEO) under accession GSE172016. This dataset comprises transcriptomic profiles from two independent paclitaxel-sensitive/resistant ovarian cancer cell line pairs: TOV21G (sensitive vs. resistant) and OV3S (sensitive vs. resistant). Raw count data were processed using DESeq2 for differential expression analysis, yielding per-gene log2 fold changes (LFC), Wald test statistics, and Benjamini-Hochberg adjusted p-values (padj). The top-table outputs were used directly as input for all downstream analyses.

### 2.2 Gene Set Definition

Two gene sets were defined based on HGNC gene symbol prefixes. ABC transporters were defined as all genes with Symbol matching the pattern `^ABC`, capturing all subfamily members (A through G). Inorganic ion transport genes were defined using a composite pattern covering solute carrier families (SLC), ion-pumping ATPases (ATP1/2/6/7/8/10), potassium channel subfamilies (KCNQ, KCNJ, KCNH, KCNK, KCNA-D, KCNAB), voltage-gated sodium channels (SCN), calcium channel subunits (CACNA), chloride channels (CLCN), aquaporins (AQP), FXYD domain proteins, HCN channels, TRP channels (TRPM, TRPV, TRPC), and purinergic receptors (P2RX).

### 2.3 Differential Expression and Visualization

LFC values were sign-inverted prior to visualization so that positive values represent up-regulation in resistant cells relative to sensitive cells. Adjusted p-values of zero were replaced with 1e-300 to enable finite log10 transformation. Genes were classified as significant if padj < 0.05 and |LFC| >= 1. Volcano plots were generated in R  using ggplot2 and ggrepel,distinguishing ABC vs. ion transport and significant vs. non-significant calls. The top 10 most significant genes per gene set were labelled.

### 2.4 Enrichment Analysis

Differentially expressed genes were ranked by fold change, and the top 200 upregulated and top 200 downregulated genes were selected for each cell line (TOV21G and OVCAR3 resistant vs. parental). Gene ontology enrichment analysis was performed using Enrichr (Kuleshov et al., 2016; Chen et al., 2013), with the GO Biological Process 2025 gene set library. The complete expressed gene list was used as the background gene set to ensure specificity of the enrichment results. The top 10 enriched biological process terms were selected based on combined score, which is computed by Enrichr as the product of the log p-value from the Fisher's exact test and the z-score of the expected rank. Results were visualized as horizontal bar plots generated in R using ggplot2, where bar length represents the combined score and fill color indicates −log₁₀(p-value).

---

## 3. Results and Discussion

### 3.1 Overview of ABC and Ion Transport Gene Dysregulation

Volcano plots integrating ABC transporter and inorganic ion transport gene expression are shown in Figure 1. Across both cell line comparisons, a substantial proportion of both gene sets reached statistical significance, indicating that paclitaxel resistance is not limited to a single transporter gene but is associated with broad transcriptional remodeling of both programs.
<img width="1592" height="605" alt="Screenshot 2026-04-29 at 7 56 00 AM" src="https://github.com/user-attachments/assets/71fb71f2-6bfe-4544-bdff-b59fe381364a" />

> **Figure 1.** Volcano plot of ABC transporter (red) and inorganic ion transport (blue) gene expression in TOV21G & OVCAR3 paclitaxel-sensitive vs. resistant cells. Dark shading = significant (padj < 0.05, |LFC| >= 1). Positive x-axis = up-regulation in resistant cells. Top 10 most significant genes per set are labelled.

### 3.2 ABCB1 Is Consistently and Strongly Up-regulated in Both Resistant Lines

The most striking finding in both datasets is the strong and highly significant up-regulation of ABCB1 in resistant cells. In TOV21G, ABCB1 shows the largest positive LFC among all ABC genes, and in OV3S it similarly reaches the rightmost position on the volcano. This is consistent with the established role of P-glycoprotein in taxane efflux and strongly supports the biological validity of the resistance model used in GSE172016.

### 3.3 Additional ABC Transporters Are Dysregulated

Beyond ABCB1, several other ABC family members show significant dysregulation. In TOV21G, ABCA5 is significantly up-regulated in resistant cells, while ABCC2 and ABCC3 show more modest but significant changes. In OV3S, ABCA4, ABCB4, ABCC6P1, and ABCG2 are significantly up-regulated in resistant cells, with ABCG2 being a known multidrug resistance transporter capable of effluxing taxanes and other chemotherapeutics. The involvement of multiple ABC subfamily members suggests that resistance is not mediated by a single transporter but may involve parallel or redundant efflux mechanisms.

### 3.4 Inorganic Ion Transport Genes Are Extensively Remodeled

The ion transport gene set shows even broader dysregulation than the ABC transporters, with over 240 significant genes in each cell line. In TOV21G, the most significantly altered ion transport genes include SLC2A3 and SLC16A6 (strongly up in resistant), and ATP8B1, SLC4A4, SLC16A7, and KCNJ15 (strongly up in sensitive/down in resistant). In OV3S, ATP6V0D2, ATP10A, and KCNH7 are among the most significantly altered. The extensive dysregulation of SLC-family transporters is particularly noteworthy, as several SLC members regulate intracellular pH, osmotic balance, and metabolite flux, all of which can influence drug uptake and apoptotic threshold.

### 3.5 Enrichment Analysis Reveals Pathways Linked to Acquired Resistance

Gene ontology enrichment analysis (Figure 2) of the top 200 differentially expressed genes in TOV21G and OVCAR3 resistant cell lines revealed distinct but convergent biological processes associated with acquired drug resistance. In TOV21G resistant cells, upregulated genes were significantly enriched for neuronal and developmental programs, including axon guidance, embryonic eye morphogenesis, neuron differentiation, and nervous system development, suggesting activation of embryonic progenitor-like transcriptional states that have been increasingly linked to cancer stem cell phenotypes and therapy resistance. Concurrently, downregulated genes in TOV21G resistant cells were enriched for immune regulatory processes, particularly negative regulation of macrophage activation, TNF superfamily cytokine production, and type II interferon production, indicating that resistant cells may actively suppress anti-tumor immune signaling to facilitate immune evasion. In OVCAR3 resistant cells, the most prominently upregulated biological processes included regulation and positive regulation of gap junction assembly, positive regulation of smooth muscle contraction, and wound healing-associated epithelial proliferation, collectively pointing toward intercellular communication remodeling through connexin-mediated gap junctions and increased cytoskeletal contractility as mechanisms of multicellular drug resistance. Downregulated pathways in OVCAR3 resistant cells encompassed epithelial cell development, regulation of Wnt signaling, and GPCR-mediated cAMP signaling, consistent with an epithelial-to-mesenchymal transition-like state and possible constitutive activation of Wnt signaling through loss of its negative regulators.

<img width="1679" height="891" alt="Screenshot 2026-04-29 at 7 48 02 AM" src="https://github.com/user-attachments/assets/b1357b70-48e8-4641-9310-3fdded874c76" />

> **Figure 2.** EnrichR gene set enrichment analysis
---

## 4. Conclusion

This analysis demonstrates that paclitaxel resistance in ovarian cancer cell lines is associated with broad transcriptional remodeling of both the ABC transporter family and the inorganic ion transport program, rather than isolated induction of ABCB1 alone. ABCB1 is the most consistently and strongly up-regulated gene across both resistant models, validating its central role. However, the co-dysregulation of ABCG2, multiple ABCC members, and hundreds of SLC and ion channel genes suggests that resistance involves a systems-level reorganization of membrane transport capacity.

Besides, further enrichment analysis reveals that acquired resistance in both ovarian cancer cell lines involves a convergent reprogramming toward embryonic and mesenchymal transcriptional states, cytoskeletal remodeling, and immune evasion.

---

## 6. Reflection

This project taught me a lot about the gap between "the script runs" and "the script runs correctly." The most frustrating issue was the LFC direction: the original DESeq2 table encoded fold changes as Sensitive/Resistant, so everything was flipped. I only caught this when ABCB1, which should be up in resistant cells, appeared on the left (down) side of the volcano. Fixing it was a one-line change but finding it required actually thinking about the biology rather than just running the code.

File naming was another reproducibility headache. The two files had slightly different naming conventions (one used an underscore, the other a dot; one had uppercase S, one lowercase s), and R is case-sensitive on macOS. Hardcoding exact filenames and using `file.exists()` checks helped catch this early rather than getting cryptic errors mid-run.

In terms of practices from this course: I kept raw data untouched in its own folder, wrote a README explaining the file structure and how to run the scripts, and made sure all figure-generating scripts are self-contained. The most rewarding part was seeing the volcano plots come together and being able to immediately read a biological story from them. ABCB1 sitting far to the right, isolated, while a cloud of blue ion transport genes surrounds it: that visual immediately suggested the broader transcriptional program this project set out to test.

---

## 7. AI Use Disclosure

I utilized Perplexity AI to better understand the technical structure of the GEO2R-generated R script, specifically how designated sample groups were incorporated and how the markdown document was formatted. I also used Perplexity AI for troubleshooting coding errors and resolving bugs encountered while running the code. In addition, I used Grammarly to improve grammar, clarity, and paraphrasing in portions of my writing. All final analyses, interpretations, and submitted work were reviewed and completed by me.

---

## References

Borst, P., & Elferink, R. O. (2002). Mammalian ABC transporters in health and disease. *Annual Review of Biochemistry*, 71, 537–592.

Chen EY, et al. (2013). Enrichr: interactive and collaborative HTML5 gene list enrichment analysis tool. *BMC Bioinformatics*, 14, 128.

Gottesman, M. M., Fojo, T., & Bates, S. E. (2002). Multidrug resistance in cancer: role of ATP-dependent transporters. *Nature Reviews Cancer*, 2(1), 48–58.

Kavallaris, M., Kuo, D. Y., Burkhart, C. A., et al. (1997). Taxol-resistant epithelial ovarian tumors are associated with altered expression of specific beta-tubulin isotypes. *Journal of Clinical Investigation*, 100(5), 1282–1293.

Kuleshov MV, et al. (2016). Enrichr: a comprehensive gene set enrichment analysis web server 2016 update. *Nucleic Acids Research*, 44(W1), W90–W97.

Patch, A. M., Christie, E. L., Etemadmoghadam, D., et al. (2015). Whole-genome characterization of chemoresistant ovarian cancer. *Nature*, 521(7553), 489–494.

Szakacs, G., Paterson, J. K., Ludwig, J. A., Booth-Genthe, C., & Gottesman, M. M. (2006). Targeting multidrug resistance in cancer. *Nature Reviews Drug Discovery*, 5(3), 219–234.
