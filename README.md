# Beyond ABCB1: A Coordinated Transporter-Centered Transcriptional Program Underlies Acquired Paclitaxel Resistance in Ovarian Cancer

---

## Introduction

Paclitaxel resistance remains one of the most clinically significant obstacles to durable treatment responses in ovarian cancer. A well-characterized mechanism underlying this resistance is the transcriptional upregulation of ATP-binding cassette (ABC) drug efflux transporters, most notably *ABCB1* (encoding P-glycoprotein, P-gp), which reduces intracellular accumulation of taxanes and thereby diminishes cytotoxic efficacy (Gottesman et al., 2002). Experimental studies in ovarian cancer cell lines have demonstrated that both forced and acquired *ABCB1* overexpression is sufficient to confer paclitaxel resistance, and that pharmacologic or genetic inhibition of P-gp can partially restore drug sensitivity, supporting a causal role for transporter mediated efflux in resistance phenotypes (Kavallaris et al., 1997; Patch et al., 2015).

Despite extensive focus on *ABCB1*, an important mechanistic question remains unresolved: does paclitaxel resistance arise solely from the upregulation of *ABCB1*, or does *ABCB1* induction reflect a broader, coordinated transcriptional state involving multiple ABC transporters and inorganic ion transport genes that collectively define the resistant phenotype? Chemotherapeutic stress is known to activate shared transcriptional regulators, including NRF2, YAP/TAZ, and AP-1, which can simultaneously induce drug efflux transporters (e.g., *ABCB1*, *ABCC1*, *ABCG2*) and ion transport systems that influence membrane potential, intracellular pH, and apoptotic threshold. If resistance is driven by such shared upstream control, one would expect to observe coordinated transcriptional activation of transporter and ion transport gene modules—rather than isolated induction of *ABCB1*—across resistant samples.

Several primary studies suggest that other ABC transporters (e.g., *ABCC1*, *ABCG2*) and ion transport systems can independently influence chemoresistance by modulating drug distribution, membrane electrochemistry, and apoptotic signaling (Borst & Elferink, 2002; Szakács et al., 2006). However, these components are typically studied in isolation, and their coordinated transcriptional regulation in paclitaxel resistant ovarian cancer has not been systematically evaluated at the transcriptome-wide level.

To address this gap, this project performs a secondary analysis of publicly available RNA sequencing data from paclitaxel-sensitive and paclitaxel-resistant ovarian cancer cell lines. Rather than reproducing figures from the original study, the analysis re-interrogates the dataset to test whether paclitaxel resistance is associated with a coherent, transporter centered transcriptional program. By integrating ABC transporter and inorganic ion transport gene sets with differential expression, pathway enrichment, and co-expression network analyses, this work aims to move beyond gene lists toward a functional, systems level characterization of the resistance machinery.

---

## Hypothesis

Acquired paclitaxel resistance in ovarian cancer cells is characterized by a *coordinated* transcriptional program involving multiple ABC transporters and inorganic ion transport genes, rather than the isolated overexpression of *ABCB1* alone. Specifically, I propose that paclitaxel-resistant cells exhibit the joint, co-regulated activation of ATP-dependent drug efflux pathways and ion transport programs — operationalized as correlated expression changes across resistant samples — consistent with shared upstream transcriptional control rather than stochastic, gene by gene induction.

---

## Nature of the Data

This project uses publicly available bulk RNA sequencing data from the NCBI Gene Expression Omnibus (GEO; Edgar et al., 2002) that profile ovarian cancer cell lines under paclitaxel-sensitive and paclitaxel-resistant conditions. The primary dataset analyzed is **GSE172016** (McCorkle et al.,2021), which includes RNA-seq profiles from TOV-21G and OVCAR3 cell lines following repeated paclitaxel exposure to generate stable resistant derivatives. The dataset contains gene-level count matrices and associated metadata describing treatment conditions and biological replicates, making it well suited for secondary differential expression and pathway-level analyses. No additional data collection or patient recruitment is required, and all data are publicly accessible without restriction.

---
## Materials and Methods

### Data Acquisition

Gene-level count matrices and sample metadata will be downloaded directly from GEO (accession GSE172016) (McCorkle et al.,2021) Initial inspection confirms that the data are formatted for downstream analysis without requiring raw read alignment. Sample metadata will be verified to ensure correct assignment of sensitive vs. resistant labels prior to analysis.

### Differential Expression Analysis

Differential gene expression between paclitaxel-sensitive and paclitaxel-resistant samples will be performed using **DESeq2** (Love et al., 2014) in R. Genes will be pre-filtered to remove those with fewer than 10 counts across all samples. Library size normalization will follow DESeq2's default median-of-ratios method. Statistical significance will be assessed using Wald tests with Benjamini-Hochberg false discovery rate (FDR) correction; genes with FDR < 0.05 and |log₂ fold change| > 1 will be considered differentially expressed.

### Transporter- and Ion-Focused Gene Set Definition

To test the coordination hypothesis, two *a priori* gene sets will be defined:

1. **ABC transporter set:** All human ABCA–ABCG family members, curated from HGNC and cross-referenced with Reactome pathway R-HSA-382556 (Borst & Elferink, 2002).
2. **Inorganic ion transport set:** Genes annotated to Gene Ontology term GO:0015075 ("ion transmembrane transporter activity") and GO:0098660 ("inorganic ion transmembrane transport"), filtered to remove those also classified as organic solute transporters, to maintain specificity for ion transport biology.

Differential expression results will be intersected with these sets to identify which transporter genes are significantly dysregulated in resistance, and in which direction.

### Pathway and Coordination Analysis

**Gene Set Enrichment Analysis (GSEA)** (Subramanian et al., 2005) will be applied using the full pre-ranked gene list (ranked by DESeq2 Wald statistic) against the two gene sets defined above. A positive normalized enrichment score (NES) with FDR < 0.25 in resistant vs. sensitive comparisons will be interpreted as evidence for pathway-level activation. To assess *joint* activation of both pathways, sample-level gene set scoring via **ssGSEA** (Barbie et al., 2009) will be computed, and Spearman correlation between ABC transporter and ion transport scores across samples will be reported. Significant positive correlation would support the interpretation that these two programs are co-activated as part of a shared resistance state, rather than independently induced.

### Network-Level Contextualization

Significantly differentially expressed transporter and ion transport genes will be mapped onto the **STRING** protein interaction network (Szklarczyk et al., 2021) using a minimum interaction confidence score of 0.7. The resulting subnetwork will be analyzed for hub nodes (high degree centrality) and densely connected communities. Genes that appear as hubs within the resistance-associated module — particularly those connecting the drug efflux and ion transport subnetworks — will be flagged as candidate leverage points for future functional validation.

---

## Planned Figure: Multi Panel Transporter-Centered Transcriptional Summary

This project will generate a new multi panel figure summarizing transporter-centered transcriptional changes associated with paclitaxel resistance. This figure is not reproduced from the original publication and represents a secondary, hypothesis driven analysis of the dataset.

**Figure A — Global transcriptomic context (PCA):** A principal component analysis (PCA) plot demonstrating global separation between paclitaxel-sensitive and paclitaxel-resistant samples. This panel establishes that the resistant phenotype is associated with a broad, reproducible transcriptional shift — motivating the search for a structured subprogram within it.

*Sample Figure:*

<img width="1342" height="1451" alt="image" src="https://github.com/user-attachments/assets/b052979f-dcc6-44ba-860c-22dab6c0f324" />

**Figure B — Differential expression landscape (Volcano plot):** A volcano plot highlighting all differentially expressed genes, with ABC transporters and inorganic ion transport genes overlaid in distinct colors. This panel shows where transporter genes sit within the genome-wide expression landscape — whether they are among the most strongly or specifically dysregulated genes, or scattered across the distribution.

*Sample Figure:*

<img width="646" height="498" alt="Screenshot 2026-02-20 at 9 54 03 PM" src="https://github.com/user-attachments/assets/cd0a5a68-1601-401a-88fa-ac2125568656" />

**Figure C — Transporter module co-regulation (Heatmap):** A heatmap restricted to differentially expressed ABC transporter and ion transport genes, ordered by hierarchical clustering across samples. If the hypothesis is correct, resistant samples should cluster together with a coherent block of upregulated transporter genes, providing visual evidence of coordinated, rather than scattered, regulation.

*Sample Figure:*

<img width="756" height="594" alt="Screenshot 2026-02-20 at 9 55 43 PM" src="https://github.com/user-attachments/assets/ffc64ad0-25fc-47bc-b53a-d93938c5f9ab" />

**Figure D — Pathway-level activation (GSEA):** Gene set enrichment analysis testing whether curated gene sets for "ATP-dependent drug efflux" and "inorganic ion transport" are jointly enriched in resistant samples. This panel moves from individual genes to pathway-level inference, and is the most direct test of the coordination hypothesis.

*Sample Figure:*

<img width="2603" height="1913" alt="image" src="https://github.com/user-attachments/assets/42d81036-e8e5-4c67-aba7-64ced0a13946" />

---

## References

Barbie, D. A., Tamayo, P., Boehm, J. S., Kim, S. Y., Moody, S. E., Dunn, I. F., ... & Hahn, W. C. (2009). Systematic RNA interference reveals that oncogenic KRAS-driven cancers require TBK1. *Nature*, 462(7269), 108–112. https://doi.org/10.1038/nature08460

Borst, P., & Elferink, R. O. (2002). Mammalian ABC transporters in health and disease. *Annual Review of Biochemistry*, 71, 537–592. https://doi.org/10.1146/annurev.biochem.71.102301.093055

Edgar, R., Domrachev, M., & Lash, A. E. (2002). Gene Expression Omnibus: NCBI gene expression and hybridization array data repository. *Nucleic Acids Research*, 30(1), 207–210. https://doi.org/10.1093/nar/30.1.207

Gottesman, M. M., Fojo, T., & Bates, S. E. (2002). Multidrug resistance in cancer: role of ATP-dependent transporters. *Nature Reviews Cancer*, 2(1), 48–58. https://doi.org/10.1038/nrc706

Kavallaris, M., Kuo, D. Y., Burkhart, C. A., Regl, D. L., Norris, M. D., Haber, M., & Bhalla, K. N. (1997). Taxol-resistant epithelial ovarian tumors are associated with altered expression of specific beta-tubulin isotypes. *Journal of Clinical Investigation*, 100(5), 1282–1293. https://doi.org/10.1172/JCI119642

Love, M. I., Huber, W., & Anders, S. (2014). Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. *Genome Biology*, 15(12), 550. https://doi.org/10.1186/s13059-014-0550-8

McCorkle JR, Gorski JW, Liu J, Riggs MB, McDowell AB, et al. (2021). Lapatinib and poziotinib overcome ABCB1-mediated paclitaxel resistance in ovarian cancer. **PLOS ONE**, 16(8): e0254205.  
https://doi.org/10.1371/journal.pone.0254205

Patch, A. M., Christie, E. L., Etemadmoghadam, D., Garsed, D. W., George, J., Fereday, S., ... & Bowtell, D. D. (2015). Whole-genome characterization of chemoresistant ovarian cancer. *Nature*, 521(7553), 489–494. https://doi.org/10.1038/nature14410

Shen, H., Xu, W., & Luo, W. (2012). Upregulation of the NRF2–ARE pathway as a mechanism for paclitaxel resistance in cancer cells. *Cancer Letters*, 312(2), 216–224.

Sissung, T. M., Baum, C. E., Deeken, J., Price, D. K., Bhatt, A., Gardner, E. R., ... & Figg, W. D. (2010). ABCB1 genotype influences the transporter's impact on pharmacodynamics of a novel taxane. *Clinical Cancer Research*, 16(19), 4893–4903. https://doi.org/10.1158/1078-0432.CCR-10-0623

Subramanian, A., Tamayo, P., Mootha, V. K., Mukherjee, S., Ebert, B. L., Gillette, M. A., ... & Mesirov, J. P. (2005). Gene set enrichment analysis: A knowledge-based approach for interpreting genome-wide expression profiles. *Proceedings of the National Academy of Sciences*, 102(43), 15545–15550. https://doi.org/10.1073/pnas.0506580102

Szakács, G., Paterson, J. K., Ludwig, J. A., Booth-Genthe, C., & Gottesman, M. M. (2006). Targeting multidrug resistance in cancer. *Nature Reviews Drug Discovery*, 5(3), 219–234. https://doi.org/10.1038/nrd1984

Szklarczyk, D., Gable, A. L., Nastou, K. C., Lyon, D., Kirsch, R., Pyysalo, S., ... & Jensen, L. J. (2021). The STRING database in 2021: customizable protein-protein networks, and functional characterization of user-uploaded gene/measurement sets. *Nucleic Acids Research*, 49(D1), D605–D612. https://doi.org/10.1093/nar/gkaa1074
