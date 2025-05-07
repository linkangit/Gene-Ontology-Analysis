# GO Enrichment Analysis for RNA-seq DEGs
Welcome! This repository contains a Python script that performs Gene Ontology (GO) enrichment analysis on a list of differentially expressed genes (DEGs). The approach leverages the [goatools](https://github.com/tanghaibao/goatools) library to identify significantly enriched GO terms, visualize the results, and summarize insights from your RNA-seq experiment. I have used differentially expressed genes from a previous [RNAseq analysis](https://github.com/linkangit/RNAseq-analysis-PyDESeq2) done on data obtained from one of my published articles [Dash et.al 2023](https://doi.org/10.1093/plphys/kiad465).

![GO_figure](go_enrichment_plot.png)

## Overview
**Goal:** The script is designed to analyze DEGs resulting from a control vs. mutant comparison. If you want to gain deeper insights into the collective biological roles of your significant genes, this pipeline is for you.  

**Method:** Uses the `goatools` library to calculate enrichment of GO terms (biological processes, molecular functions, and cellular components) within your DEG list, relative to a broader set of background genes.

---

## Files and Inputs
1. **DEG.txt**: Contains your DEGs (one gene ID per line).  
2. **all_genes.txt**: The background gene list, typically all genes measured or expressed in the experiment.  
3. **go-basic.obo**: The hierarchical structure of the GO (the ontology file that describes how GO terms relate to each other).  
4. **gene_annotations_tair.gaf**: The GAF (Gene Annotation File) mapping genes to the GO terms they are associated with in Arabidopsis.  

*Tip:* Make sure these files match your species of interest. For other organisms, you’ll need the relevant GAF annotation and background gene list.

---

## Key Steps in the Script
1. **Install `goatools`:** Ensures the required dependency for GO analysis is available.  
2. **Import Python Libraries:**  
   - `pandas`, `numpy` for data organization.  
   - `seaborn`, `matplotlib` for visual artistry.  
   - Various modules from `goatools` for reading the ontology and performing enrichment analysis.  

3. **Load and Parse Gene Lists:**  
   - Reads your DEGs from `DEG.txt` into a Python list.  
   - Reads the background genes from `all_genes.txt` into another list.  
   - Reports how many genes were found in each list, providing a quick sanity check.

4. **Load GO Data:**
   - **go-basic.obo:** The DAG (Directed Acyclic Graph) that includes the relationships among GO terms.  
   - **gene_annotations_tair.gaf:** A GAF specifying which GO terms are associated with which Arabidopsis genes.

5. **Run GO Enrichment:**
   - Creates a `GOEnrichmentStudy` object with your background genes and the GO-to-gene mapping.  
   - Performs statistical tests (propagates counts so parent terms get credit from child terms) and corrects p-values for multiple comparisons (using the FDR-BH method).  
   - Filters to keep only those GO terms that pass your significance threshold (e.g., FDR < 0.05).

6. **Store Results in a DataFrame:**
   - Extracts GO IDs, term names, raw and corrected p-values, fold enrichment, and other useful information.  
   - Computes `neg_log10_p` for friendly plotting of significance values.

7. **Sort and Visualize Top GO Terms:**
   - Sorts by highest fold enrichment to uncover the most striking biological patterns.  
   - Generates a scatter plot of the top 20 GO terms, visually encoding significance, fold enrichment, and the number of genes in each GO term.  
   - Saves the figure to an image file (`go_enrichment_plot.png`), handy for sharing with colleagues or embedding in a paper.

---

## Why Perform GO Enrichment Analysis in RNA-seq?
In an RNA-seq experiment, once you’ve pinpointed genes that behave differently between conditions (e.g., control vs. mutant), the next logical question is what *functions* these genes perform. This is where GO enrichment shines:

1. **Biological Storytelling:** A group of DEGs enriched in “response to oxidative stress” might point you to a protective mechanism in the mutant line.  
2. **Hypothesis Generation:** If you see enrichment of “DNA repair” terms, you might hypothesize that DNA repair pathways are more active or critical under your experimental conditions.  
3. **Prioritizing Follow-up Experiments:** By focusing on the most overrepresented processes, you can more easily decide which genes or pathways to validate in the wet lab.

---

## Running the Script
1. **Installation:**  
   - `pip install goatools pandas seaborn matplotlib`  
2. **Prepare Your Working Directory:**  
   - Place `DEG.txt`, `all_genes.txt`, `go-basic.obo`, and `gene_annotations_tair.gaf` in an accessible directory.  
3. **Execute:**  
   - Run `python go_enrichment_script.py` (or your script’s name) from the command line, a virtual environment, or a Jupyter notebook.

---

## Output and Visualization
After a successful run, you should see:  
- **Print Statements:** Summaries of how many genes are in your DEG list, how many are in the background set, and how many significant GO terms you discovered.  
- **Tabular Results (DataFrame):** A detailed table including all enriched terms, p-values, fold enrichment, etc.  
- **Scatter Plot:** A PNG file (`go_enrichment_plot.png`) showcasing the top 20 GO terms.  
  - X-axis = Fold Enrichment  
  - Y-axis = GO Term name  
  - Dot size = Number of DEGs mapped to that term in your experiment.  
  - Dot color = Significance level (–log10(p-value)).  

By hovering or examining the annotation in the plot, you can see each GO ID placed near its corresponding point for easy reference.

---

## Possible Extensions and Applications
1. **Zoom in on Single Ontology Aspect:** Investigate only *Biological Process* or *Molecular Function* terms by filtering the results.  
2. **Compare Multiple Comparisons/Conditions:** If you have multiple DEG lists (e.g., multiple time points or different mutant alleles), you can run the analysis separately for each set and compare.  
3. **Interact with Other Databases:**  
   - Combine with KEGG pathway analysis or Reactome for a more pathway-centric approach.  
   - Integrate protein-protein interaction data for network-based insights.  
4. **Other Organisms or Data Types:**  
   - Swap out `gene_annotations_tair.gaf` for the GAF file of your organism of interest.  
   - Use custom gene annotations if you work on a non-model species with a draft genome.

---

## Summary
Following DE analysis in an RNA-seq experiment, *what’s next* is often the deeper biological interpretation of your gene list. GO enrichment serves as a powerful spotlight to highlight the functions and processes most relevant to your condition of interest. With a few Python scripts, standard input files, and the `goatools` library, you can quickly build a comprehensive picture of the biological machinery at play.

By meticulously tracing your DEGs back to their GO terms, you assemble a clearer biological narrative. This can guide you toward practical follow-up experiments, such as pinpointing genes for CRISPR knockouts or overexpression lines, and can even spark entirely new research questions.

In short, GO enrichment is an excellent tool for turning lists of DEGs into vivid stories that reveal underlying biology—helping you share compelling findings with your lab mates, reviewers, or at your next conference presentation!
