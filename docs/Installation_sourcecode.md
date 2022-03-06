---
layout: default
title: Source code
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Installation
---

### Intall SAIGE using source code

1. Install dependcies 

     * R >= 3.6.1, gcc >= 5.4.0, cmake 3.14.1,
    
2. Download SAIGE from github

     ```
       src_branch=main
       repo_src_url=https://github.com/saigegit/SAIGE
       git clone --depth 1 -b $src_branch $repo_src_url	
     ```

3. Install dependencies: R packages


     ```
	Rscript ./SAIGE/extdata/install_packages.R
        
     ```

4. Install SAIGE R package

     * To install SAIGE to the root directory storing all R libraries
     ```
        R CMD INSTALL SAIGE
     ```

     * **--library=path_to_final_SAIGE_library** can be used for specifying the directory where SAIGE is installed 
     ```
	R CMD INSTALL --library=path_to_final_SAIGE_library SAIGE

     ```

5. Run SAIGE
     * If SAIGE was not installed in the root R lib path, change 

     ```
        library(SAIGE)
     ```

     to 
      
     ```
        library(SAIGE, lib.loc="path_to_final_SAIGE_library")
     ```

     in the following scripts

    ```
    SAIGE/extdata/step1_fitNULLGLMM.R
    SAIGE/extdata/step2_SPAtests.R
    SAIGE/extdata/createSparseGRM.R
    ```





