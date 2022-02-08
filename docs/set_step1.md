---
layout: default
title: Step 1
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Set-based test
---


# Step 1: fitting the null logistic/linear mixed model

Step 1 for set-based tests are the same as for single-variant tests, except that 

1. Multiple variance ratios need to be estimated based on different minor allele count categories with *--isCateVarianceRatio=TRUE*
   * By default, two variance ratios are estiamted for 10 <= MAC < 20 and MAC >= 20. Use *--cateVarRatioMinMACVecExclude* and *--cateVarRatioMaxMACVecInclude* to modify the MAC categories
   * Note that the PLINK file need to contain at least 1000 variants whose MAC fall in these categories     

2.1. When a full GRM is used to fit the null model (--useSparseGRMtoFitNULL=FALSE), variance ratios are estiamted based on both a full GRM (to be constrcuted using genotypes in the PLINK file) and a sparse GRM with *--useSparseGRMforVarRatio=TRUE*
   * Use --sparseGRMFile for the file containing the sparse GRM
   * Use --sparseGRMSampleIDFile for the file containing the IDs for samples in the sparse GRM 

2.2. When a sparse GRM is used to fit the null model (--useSparseGRMtoFitNULL=TRUE), variance ratios are estiamted based on a sparse GRM and NULL (leave *--useSparseGRMforVarRatio=FALSE*)


```
#Estimate categorical variance ratios (*--isCateVarianceRatio=TRUE*)
#Use a full GRM to fit the null model(by default, *--useSparseGRMtoFitNULL=FALSE*)
#Use an additional sparse GRM to estimate the variance ratio (*--useSparseGRMforVarRatio=TRUE*) 
Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --useSparseGRMtoFitNULL=FALSE    \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2,a9,a10 \
        --qCovarColList=a9,a10  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary \
        --nThreads=4    \
        --noEstFixedEff=TRUE    \
        --isCateVarianceRatio=TRUE      \
        --useSparseGRMforVarRatio=TRUE  \
        --IsOverwriteVarianceRatioFile=TRUE
```

```
#Fit the null model using a sparse GRM (*--useSparseGRMtoFitNULL=TRUE*)
#Do not estiamte variance ratios (*--skipVarianceRatioEstimation=TRUE*), so in Step 2, the sparse GRM will be used directly in the tests
 
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
        --outputPrefix=./output/example_binary \
        --nThreads=2    \
        --noEstFixedEff=TRUE    \
        --skipVarianceRatioEstimation=TRUE
```


## Input files

1. (same as step 1 for single-variant assoc tests and step 0) <br/> 
Genotype file for constructing the genetic relationship matrix in the plink format <br/> 

2. a file storing the sparse GRM (optional, output by step 0) 

    ```
    sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx
    ```
The sparse GRM file can be opened using the *readMM* function in the R library *Matrix* 


3. a file storing IDs of the samples in the sparse GRM (optional, output by step 0)
    
    ```
    sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt
    ```

## Output files 
* same as the step 1 output by SAIGE for single-variant association tests <br/>

1. model file <br/>
2. association result file for the subset of randomly selected markers <br/>
3. variance ratio file <br/>

* specific to SAIGE-GENE

4. sparse Sigma file

    ```
    sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseSigma.mtx
    ```
NOTE: the file contains the sparse Sigma matrix, which is ** NOT ** the sparse GRM. The sparse Sigma matrix is computed based on the sparse GRM and the results of step 1. 
