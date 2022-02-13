---
layout: default
title: Create sparse GRM
nav_order: 6
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
---

## Step 0: create a sparse GRM


### SAIGE and SAIGE-GENE can take sparse GRM for fitting the null model and in association tests

* This sparse GRM only needs to be created once for each data set, e.g. a biobank,  and can be used for all different phenotypes as long as all tested samples are in the sparse GRM.
* Multiple programs can be used to generate a sparse GRM


1. SAIGE provides a script to create a sparse GRM
    *The program will output a file ended with sampleIDs.txt that contains sample IDs for the sparse GRM and a file ended with .sparseGRM.mtx that contains the sparse GRM
    * These two files can be then directly used in the next steps 

    ```
    #For help information
    Rscript createSparseGRM.R --help
    ```


    ``` 
    Rscript createSparseGRM.R       \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly \
        --nThreads=4  \
        --outputPrefix=./output/sparseGRM       \
        --numRandomMarkerforSparseKin=2000      \
        --relatednessCutoff=0.125
    ```
2. [GCTA](https://yanglab.westlake.edu.cn/software/gcta/#MakingaGRM)

    ```
    gcta64 \
        --bfile ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly \
        --out ./output/sparseGRM \
        --make-grm-part 3 1 \
        --maf 0.01 \
        --geno 0.15 \
        --thread-num 2
    ```

3. [KING](https://www.kingrelatedness.com/manual.shtml)
