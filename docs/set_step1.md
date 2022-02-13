---
layout: default
title: Step 1
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Set-based test
---


## Step 1: fitting the null logistic/linear mixed model

1.  When a sparse GRM is used to fit the null model (--useSparseGRMtoFitNULL=TRUE) and no variance ratios are estiamted (--skipVarianceRatioEstimation=TRUE), Step 1 for set-based tests are the same as for single-variant tests in SAIGE


    ```
    Rscript step1_fitNULLGLMM.R     \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --useSparseGRMtoFitNULL=TRUE    \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2,a9,a10 \
        --qCovarColList=a9,a10  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --skipVarianceRatioEstimation=TRUE	\
        --outputPrefix=./output/example_binary_sparseGRM
    ```

2. When a full GRM is used to fit the null model (GRM is constructed on-the-fly using genotypes in the PLINK file, **--plinkfile=**)
   
   * Use a full GRM to fit the null model(by default, *--useSparseGRMtoFitNULL=FALSE*)
   * Use both a full GRM (to be constrcuted using genotypes in the PLINK file) and a sparse GRM with *--useSparseGRMforVarRatio=TRUE*
       ** Use *--sparseGRMFile* for the file containing the sparse GRM
       ** Use *--sparseGRMSampleIDFile* for the file containing the IDs for samples in the sparse GRM
   * Multiple variance ratios need to be estimated based on different minor allele count categories with *--isCateVarianceRatio=TRUE*
       ** By default, two variance ratios are estiamted for 10 <= MAC < 20 and MAC >= 20.
       ** Use *--cateVarRatioMinMACVecExclude* and *--cateVarRatioMaxMACVecInclude* to modify the MAC categories
       ** Note that the PLINK file need to contain at least 1000 variants whose MAC fall in these categories
       ** Different from Step 1 in SAIGE for single-variant tests in SAIGE, in which only a single variance ratio is estiamted

    ```
    Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2,a9,a10 \
        --qCovarColList=a9,a10  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary_fullGRM \
        --nThreads=64    \
        --useSparseGRMtoFitNULL=FALSE    \
        --isCateVarianceRatio=TRUE      \
        --outputPrefix_varRatio=./output/example_binary_cate \
        --useSparseGRMforVarRatio=TRUE  \
        --IsOverwriteVarianceRatioFile=TRUE
    ```


### Input files (Same as input in SAIGE Step 1)

* Same as the input in Step 1 by SAIGE for single-variant association tests <br/>
* Specific to SAIGE-GENE+ for rare variants, the PLINK file --plinkFile= needs to contain rare variants with MAC low to 10. 


1. **(Required)** Phenotype file (contains covariates if any, such as gender and age)
The file can be either space or tab-delimited with a header. It is required that the file contains one column for sample IDs and one column for the phenotype. It may contain columns for covariates. <br/>

<img src="/assets/img/pheno_head.png" width="300">

    ```
    less -S ./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt
    ```

2. **(Optional)** Genotype file for constructing the full genetic relationship matrix (GRM) and estimating the variance ratio<br/>
SAIGE takes the PLINK binary file for the genotypes and assumes the file prefix is the same one for .bed, .bim. and .fam

    ```
    ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly.bed
    ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly.bim
    ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly.fam
    ```


3. **(Optional)** sparse GRM file and the sample ID file for the sparse GRM. Please see Step 0 for more details.

    ```
    ./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx
    ./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt

    ##the sparse matrix can be read and viewed using the R library Matrix
    library(Matrix)
    sparseGRM = readMM("./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx")

    ```

### Output files ((Same as output in SAIGE Step 1 for single-variant association tests))
* Same as the output in Step 1 by SAIGE for single-variant association tests <br/>
* Specific to SAIGE-GENE+ for rare variants, the variance ratio file contains multiple variance ratios instead a single variance ratio  


1. model file (**input for Step 2**)


    ```
    ./output/example_binary.rda

    #load the model file in R
    load("./output/example_binary.rda")
    names(modglmm)
    modglmm$theta

    ```
<img src="/assets/img/SAIGE-step1-output.png" width="500">

2. variance ratio file (**if categorical variance ratios are estiamted in Step 1, this will be input for Step 2**)

    ```
    less -S ./output/example_binary_cate.varianceRatio.txt
    ```


3. association result file for the subset of randomly selected markers (**This file will be generated if variance ratio is estiamted in Step 1. It is an intermediate file and won't be needed for next steps.**)

    ```
    less -S ./output/example_binary_30markers.SAIGE.results.txt
    ```
