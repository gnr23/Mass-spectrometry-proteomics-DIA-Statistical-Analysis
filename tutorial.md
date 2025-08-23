## _DIA_MSstats_Logbook_
Bioinformatics tools: MSStats, 
### Overview

**Figure 1**:  All-in one workflow for DIA analysis in Galaxy. The statistical analysis using MSstats is highlighted in green and is the third part of my learning 


![workflow](/images/intro1.png)

### Table of Contents

1.  [Preparation & Setup](#preparation--setup)
    
2.  [Data Acquisition](#data-acquisition)
    
3.  [MSstats Statistical Analysis](#msstats-statistical-analysis)
    
4.  [Reflections & Observations](#reflections--observations)
    
5.  [Conclusion](#conclusion)
    
6.  [References & Further Reading](#references--further-reading)
    

----------

### 1. Preparation & Setup

    
-   **Objective**: Learn how to detect statistically significant differences in protein abundance between spike-in conditions using **MSstats** 
  

----------

### 2. Data Acquisition

1.  **Started a new Galaxy history**, titled: _“DIA_MSstats walkthrough”_.
    
2.  **Imported** the following files (via Zenodo links):
    
    -   `PyProphet_export.tabular`
        
    -   `Sample_annot_MSstats.txt`
        
    -   `Comp_matrix_HEK_Ecoli.txt`
        
    -   `PyProphet_msstats_input.tabular` [Galaxy Training Network].
        
3.  **Renamed** each dataset for clarity:
    
    -   e.g., `Sample_annot_MSstats`, `Comp_matrix_HEK_Ecoli`, `PyProphet_export`, `PyProphet_msstats_input`.
        
4.  **comments**: after having generated DIA library and processing it, I imported the results that I had generated described in my other  repos
    

----------

### 3. MSstats Statistical Analysis

1.  Launched **MSstats tool** (Galaxy version 3.20.1.0) — a Galaxy-implemented version of the popular R package for proteomics statistics [Galaxy Training Network]
    
2.  Configured parameters:
    
    -   **Input source**: `OpenSWATH`
        
    -   **Param file – OpenSWATH_input**: `PyProphet_export`
        
    -   **Param file – OpenSWATH_annotation**: `Sample_annot_MSstats`
        
    -   **Compare Groups?**: Yes
        
    -   **Comparison Matrix**: `Comp_matrix_HEK_Ecoli` 
       
![msstats1](/images/msstats1.png)        
  
              **my take**: 

            - MSstats gets the raw tabular input from OpenSWATH / PyProphet outputs -   
            - This tells MSstats how to parse the columns
            - Which column represents protein, peptide, charge, intensity, run, condition, etc.
            - Different tools export slightly different formats, so this setting is crucial.
            - Also asking for  a **contrast specification** (compare groups) so the model will compute log2FC=mean(Spike_in_2)−mean(Spike_in_1)
            - 
              **additional observations**: By setting `Compare Groups = Yes` and giving MSstats my `Comp_matrix_HEK_Ecoli`, I asked it to test whether proteins change between Spike_in_2 and Spike_in_1. The matrix [-1, +1] literally encodes this subtraction: Spike_in_2 minus Spike_in_1. Every protein gets a log2 fold change and a p-value for this comparison.
    
![comparison](/images/comparison.png)            

3.  **Generated outputs**: 
**MSstats log file** : MSstats took raw peptide intensities → cleaned them → normalized → summarized at protein level → compared between Spike_in_1 and Spike_in_2 → reported significant differences where data allowed.

![log](/images/log.png)

**Raw data (PyProphet_export):**  straight from DIA scoring (~1.1M rows).
**Feature-level data (MSstats):** the **filtered dataset** that MSstats uses for quantification (~1.1M rows but after decoy/m_score/uniqueness filtering).

![raw](/images/raw.png)
![featured](/images/featuredleveldata.png)

    **ComparisonResult_export_tabular.tsv** with 4953  lines meaning over almost 5000 Proteins were compared between the two different Spike-in conditions.
    
![comparison](/images/finalcomparison.png)    
    
    
## Detailed investigation of Ecoli identifications and quantifications[]



**Select**  with the following parameters:
   -   param-file  _“Select lines from”_:  `MSstats_ComparisonResult_export_tabular`  (output of  **MSstats**  tool)
     -   _“the pattern”_:  `(ECOLI)|(log2FC)`
2.  **Filter**  with the following parameters:
    -   param-file  _“Filter”_:  `Select_Ecoli`  (output of  **Select**  tool)
     -   _“With following condition”_:  `c7!='NA'`
     -   _“Number of header lines to skip”_:  `1`
  3.  **Histogram**  (  Galaxy version 1.0.4)  with the following parameters:
     -   param-file  _“Dataset”_:  `Filter_Ecoli`  (output of the previous  **Filter**  tool)
     -   _“Numerical column for x axis”_:  `Column: 3`
     -   _“Number of breaks (bars)”_:  `25`
     -   _“Plot title”_:  `Distribution of Ecoli Protein log FC values`
     -   _“Label for x axis”_:  `log2 Fold Change`

**Results**

![select1](/images/select.png)   
![select1](/images/filter.png) 
![select1](/images/histogram.png) 

**Discussion-Comments** 
-   we used the `Select` tool with `(ECOLI)|(log2FC)` to keep **only rows for E. coli proteins** plus the header.

-  we applied the condition `c7!='NA'` → meaning: remove rows where column 7 (`pvalue`) is NA (not available).
    
-   That dropped **104 rows** (797 – 693 = 104). So there are **693 E. coli proteins that have valid log2FC + p-values**.
    
- Now, with histogram we are plotting column 3 (`log2FC`) for those 693 proteins:
X-axis = log2 fold change (difference between Spike_in_2 and Spike_in_1)
Bars = how many proteins fall into each fold-change bin
    
This visualization shows the **distribution of changes** across the E. coli spike-in set.

- We can see a gaussian distribution of the log2FC values around a positive value of 3. Since we compared Spike_in_2 / Spike_in_1 we can directly see that Spike_in_2 contained higher amounts of Ecoli. 

- Furthermore, since the apex of the distribution is around 3 and we compared log2 intensities, we could estimate that Spike_in_2 contained approx. 8-times more Ecoli than Spike_in_1.
    
    -   2.9, 3.16, 3.19, 3.75 … → they cluster around ~3.
        
***-   This means MSstats successfully recovered the expected difference between spike-in groups.***




**Summary :**

  - `Select`: grab only E. coli spike-in proteins (797 lines).
    
-   `Filter`: keep only proteins with valid test results (693 lines).
    
-   `Histogram`: confirm that the E. coli fold changes cluster around the expected value (≈3), showing that my analysis pipeline is working.

# Statistical analysis with  **MSstats**

Performing statistical analysis using  **MSstats**  and the msstats_input from  **PyProphet export**[]

1.  **MSstats**  (  Galaxy version 3.20.1.0)  with the following parameters:
    -   _“input source”_:  `MStats 10 column format`
        -   param-file  _“MSstats 10-column input”_:  `PyProphet_msstats_input`
    -   _“Compare Groups”_:  `Yes`

**Results**

![msstats10](/images/msstatscolumn10.png) 

**Discussion-Comments**  
 `PyProphet_msstats_input` is the pre-formatted table exported from Galaxy’s **PyProphet to MSstats converter**. It transforms raw DIA quant results into the MSstats 10-column schema.

*What happens inside MSstats at this step*

-   **Reads the 10-column input** and checks formatting.
    
-   **Processes** 
    
    -   log2 transformation,
        
    -   normalization (default is equalizing medians),
        
    -   handles missing values.
            
-   **If “Compare Groups = Yes”** → fits contrasts between groups using the annotation file.
    
    

- In my case: computes log2 fold changes between Spike_in_1 vs Spike_in_2 for every protein.

**Summary**- 
“When switching to the 10-column input, I saw that ~693 proteins went into the group comparison. This number can differfrom the earlier run with ‘input source = OpenSWATH’ because in that case Galaxy prepared the input behind the scenes. By explicitly giving MSstats the 10-column table, I can double-check which proteins are included, and this sometimes changes the count.”
        
## Detailed investigation of Ecoli identifications and quantifications
Investigating Ecoli proteins in the MSstats comparison results

1.  **Select**  with the following parameters:
    -   param-file  _“Select lines from”_:  `MSstats_ComparisonResult_msstats_input`  (output of the second  **MSstats**  tool)
    -   _“the pattern”_:  `(ECOLI)|(log2FC)`
2.  **Filter**  with the following parameters:
    -   param-file  _“Filter”_:  `Select_Ecoli`  (output of the second  **Select**  tool)
    -   _“With following condition”_:  `c7!='NA'`
    -   _“Number of header lines to skip”_:  `1`
3.  **Histogram**  (  Galaxy version 1.0.4)  with the following parameters:
    -   param-file  _“Dataset”_:  `Filter_Ecoli`  (output of the previous  **Filter**  tool)
    -   _“Numerical column for x axis”_:  `Column: 3`
    -   _“Number of breaks (bars)”_:  `25`
    -   _“Plot title”_:  `Distribution of Ecoli Protein log FC values`
    -   _“Label for x axis”_:  `log2 Fold Change`

**Results**
![select2](/images/select2.png) 
![select2](/images/filter2.png) 
![select2](/images/histogram2.png.png) 



**Discussion-Comments**  

Distribution of **log2FC** values (second investigation, 10-column input)

   The histogram again shows a **tight cluster of positive log2FC values** for _E. coli_ proteins.
    
-   Most values sit in the **~2.5–4.0 range**, reflecting the expected enrichment of _E. coli_ spike-ins in Spike_in_2 compared to Spike_in_1.
    
-   The shape is unimodal, roughly bell-like but shifted toward higher fold changes.
    
-   Very few proteins are near zero or negative — as expected, since _E. coli_ proteins are the spiked-in ones



**Summary**
By narrowing down to _E. coli_ proteins with valid p-values, I could visualize their fold change distribution after running MSstats with the 10-column input. The histogram again confirms that _E. coli_ proteins are strongly enriched in Spike_in_2. The overall trend matches my earlier OpenSWATH-based analysis, though minor differences in protein counts and spread highlight the impact of input format on MSstats processing.”


# Conclusion

  This tutorial demonstrated how to take DIA quantification results (from OpenSWATH → PyProphet) and perform a **statistical analysis** using MSstats . Through stepwise filtering, normalization, and linear modeling, I learned how to:

-   Convert raw peptide intensities into **protein-level summaries**.
    
-   Apply **statistical contrasts** between experimental conditions (e.g. Spike_in_2 vs Spike_in_1).
    
-   Interpret **log2 fold changes**, **p-values**, and **adjusted p-values**.
    
-   Investigate specific protein groups (here, _E. coli_ spike-ins) to validate experimental expectations.
    
-   Visualize results with histograms to assess the distribution of differential abundances.
    

**Overall, this tutorial reinforced that statistical analysis is the final critical step in DIA proteomics pipelines**: while spectral libraries (Tutorial 1) and DIA quantification (Tutorial 2) generate the measurements, it is only through MSstats (Tutorial 3) that we can rigorously test hypotheses, control error rates, and draw biologically meaningful conclusions.


