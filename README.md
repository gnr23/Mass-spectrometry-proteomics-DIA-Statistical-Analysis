# Mass-spectrometry-proteomics-DIA-Statistical-Analysis
Using MSstats to find significantly dysregulated proteins in a Spike-in dataset
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
    
    -   _Introduction to Galaxy Analyses_,
        
    -   _Hands-on: Library Generation for DIA Analysis_, and
        
    -   _Hands-on: DIA Analysis using OpenSwathWorkflow_ [Galaxy Training Network].
        
-   **Expected duration**: ~1 hour [Galaxy Training Network].
    
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
        
4.  **First impressions**: the split between data files felt clear. I’m especially curious to see how MSstats handles the annotation and comparison matrix step.
    

----------

### 3. MSstats Statistical Analysis

1.  Launched **MSstats tool** (Galaxy version 3.20.1.0) — a Galaxy-implemented version of the popular R package for proteomics statistics [Galaxy Training Network][usegalaxy.org]
    
2.  Configured parameters:
    
    -   **Input source**: `OpenSWATH`
        
    -   **Param file – OpenSWATH_input**: `PyProphet_export`
        
    -   **Param file – OpenSWATH_annotation**: `Sample_annot_MSstats`
        
    -   **Compare Groups?**: Yes
        
    -   **Comparison Matrix**: `Comp_matrix_HEK_Ecoli` [Galaxy Training Network]
        
3.  **Ran the tool**, waiting for Galaxy to complete the job.
    
4.  **Generated outputs**: differential expression results, statistics, potentially visualization files.
    
5.  **Initial observations**: MSstats takes structured inputs and computes statistica inspect which proteins emerge as significantly regulated.
    

----------

### 4. Reflections & Observations

-   **Insight**: MSstats enriches the analysis phase — not only producing numbers but tying them to meaningful comparisons between spike-in conditions.
    
-   **Challenges**: Navigating Galaxy’s interface for specifying multiple inputs felt a bit intricate; being methodical with names and structure is key.
    
-   **Improvements**: I might experiment with alternative preprocessing workflows (e.g. swath2stats) to see how they affect sensitivity and specificity [PMC][Galaxy Training Network]
    

----------

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