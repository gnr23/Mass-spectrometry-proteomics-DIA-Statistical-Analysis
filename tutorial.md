## _DIA_MSstats_Logbook_

### Overview

### Table of Contents

1.  [Preparation & Setup](#preparation--setup)
    
2.  [Data Acquisition](#data-acquisition)
    
3.  [MSstats Statistical Analysis](#msstats-statistical-analysis)
    
4.  [Reflections & Observations](#reflections--observations)
    
5.  [Conclusion](#conclusion)
    
6.  [References & Further Reading](#references--further-reading)
    

----------

### 1. Preparation & Setup

-   **Date started**: _[Add actual date, e.g., August 21, 2025]_
    
-   **Objective**: Learn how to detect statistically significant differences in protein abundance between spike-in conditions using **MSstats** in a Galaxy workflow.
    
-   **Prerequisites**: Have completed
    
        
    -   _Hands-on: Library Generation for DIA Analysis_, and
        
    -   _Hands-on: DIA Analysis using OpenSwathWorkflow_ 
        

    
-   **Galaxy instances tested**: UseGalaxy.eu, UseGalaxy.org (Main), UseGalaxy.no — all confirmed working [Galaxy Training Network].
    

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

1.  Launched **MSstats tool** (Galaxy version 3.20.1.0) — a Galaxy-implemented version of the popular R package for proteomics statistics [Galaxy Training Network][usegalaxy.org]
    
2.  Configured parameters:
    
    -   **Input source**: `OpenSWATH`
        
    -   **Param file – OpenSWATH_input**: `PyProphet_export`
        
    -   **Param file – OpenSWATH_annotation**: `Sample_annot_MSstats`
        
    -   **Compare Groups?**: Yes
        
    -   **Comparison Matrix**: `Comp_matrix_HEK_Ecoli` 
       
          msstats1.png
          
              **my take**: 

            - MSstats gets the raw tabular input from OpenSWATH / PyProphet outputs -   
            - This tells MSstats how to parse the columns
            - Which column represents protein, peptide, charge, intensity, run, condition, etc.
            - Different tools export slightly different formats, so this setting is crucial.
            - Also asking for  a **contrast specification** (compare groups) so the model will compute log2FC=mean(Spike_in_2)−mean(Spike_in_1)
            - 
              **additional observations**: By setting `Compare Groups = Yes` and giving MSstats my `Comp_matrix_HEK_Ecoli`, I asked it to test whether proteins change between Spike_in_2 and Spike_in_1. The matrix [-1, +1] literally encodes this subtraction: Spike_in_2 minus Spike_in_1. Every protein gets a log2 fold change and a p-value for this comparison.
    comparison.png 
    
3.  **Generated outputs**: 
**MSstats log file** : MSstats took raw peptide intensities → cleaned them → normalized → summarized at protein level → compared between Spike_in_1 and Spike_in_2 → reported significant differences where data allowed.
log.png
**Raw data (PyProphet_export):**  straight from DIA scoring (~1.1M rows).
**Feature-level data (MSstats):** the **filtered dataset** that MSstats uses for quantification (~1.1M rows but after decoy/m_score/uniqueness filtering).
raw.png
featuredleveldata.png


    **ComparisonResult_export_tabular.tsv** with 4953  lines meaning over almost 5000 Proteins were compared between the two different Spike-in conditions.
    finalcomparison.png
    
    
    
## Detailed investigation of Ecoli identifications and quantifications[]

Hands On: Investigating Ecoli proteins in the MSstats comparison results[](https://training.galaxyproject.org/training-material/topics/proteomics/tutorials/DIA_Analysis_MSstats/tutorial.html#hands-on-investigating-ecoli-proteins-in-the-msstats-comparison-results)

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


### 5. Conclusion

-   **Take-home message**: Statistical analysis is the keystone of proteomics — MSstats robustly handles DIA-based comparisons and brings rigor to the conclusions [Galaxy Training Network][PMC]
    
-   **Next steps**:
    
    -   Compare results using other preprocessing workflows (e.g. swath2stats).
        
    -   Try MSstats version 3.22.0.1 (newer Galaxy tool version) [usegalaxy.eu]
   
    -   Explore visualizations: volcano plots, histograms, etc.
        

----------

### 6. References & Further Reading

-   **Tutorial**: _Statistical analysis of DIA data_ — Matthias Fahrner & Melanie Föll (published Dec 2, 2020; last updated Nov 9, 2023) [Galaxy Training Network]
    
-   **Tool implementation**: MSstats Galaxy version details [usegalaxy.org]([usegalaxy.eu]
    
-   **Workflow context**: Galaxy’s full DIA pipeline—covering spectral library generation, DIA identification, and statistical analysis [PMC].