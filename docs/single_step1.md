---
layout: default
title: Step 1
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Single-variant test
---



# Step 1: fitting the null logistic/linear mixed model

* For **binary traits**, a null logistic mixed model will be fitted (*--traitType=binary*).  <br/>
* For **quantitative traits**, a null linear mixed model will be fitted (*--traitType=quantitative*) and needs to be inverse normalized (*--invNormalize=TRUE*) <br/>

```
#check the help info for step 1
Rscript step1_fitNULLGLMM.R --help
```

## Fit the null model using a *full GRM*

### For **binary traits**, a null logistic mixed model will be fitted (*--traitType=binary*).  <br/>
* Use *--qCovarColList* to list categorical covariates. Dummy variables will be created for difference levels.  
* Use *--nThreads* for number of CPUs to use
* Use *--noEstFixedEff=TRUE* to set all fixed effects as offset. The step 1 will be faster.

```
Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2,a9,a10 \
        --qCovarColList=a9,a10  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary \
        --nThreads=2	\
        --noEstFixedEff=TRUE
```

###  For **quantitative traits**, a null linear mixed model will be fitted (*--traitType=quantitative*) and needs to be inverse normalized (*--invNormalize=TRUE*) <br/>

```
Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --useSparseGRMtoFitNULL=FALSE    \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_quantitative \
        --covarColList=x1,x2,a9,a10 \
        --qCovarColList=a9,a10  \
        --sampleIDColinphenoFile=IID \
        --invNormalize=TRUE     \
        --traitType=quantitative        \
        --outputPrefix=./output/example_quantitative \
        --nThreads=2    \
        --noEstFixedEff=TRUE    \
```


## Fit the null model using a sparse GRM
* Use --useSparseGRMtoFitNULL=TRUE
* Use --sparseGRMFile for the file containing the sparse GRM
* Use --sparseGRMSampleIDFile for the file containing the IDs for samples in the sparse GRM
Note: When a sparse GRM is used for fitting the null model, the markers in the plink file will be used for estimating the variance ratio and not for  constructing a full GRM.  


```
Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
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
        --noEstFixedEff=TRUE
```

## Input files
1. **(Required)** Genotype file for constructing the full genetic relationship matrix (GRM) and estimating the variance ratio<br/> 
SAIGE takes the PLINK binary file for the genotypes and assumes the file prefix is the same one for .bed, .bim. and .fam


```
./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly.bed
./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly.bim
./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly.fam
```

2. **(Required)** Phenotype file (contains covariates if any, such as gender and age)
The file can be either space or tab-delimited with a header. It is required that the file contains one column for sample IDs and one column for the phenotype. It may contain columns for covariates.


```
less -S ./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt
```


3. **(Optional)** sparse GRM file and the sample ID file for the sparse GRM


```
./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx
./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt
```

## Output files

1. model file (**input for Step 2**)


    ```
    ./output/example_quantitative.rda
    
    #load the model file in R
    load("./output/example_quantitative.rda")
    names(modglmm)
    modglmm$theta

    #theta: a vector of length 2. The first element is the dispersion parameter estimate and the second one is the variance component parameter estimate
    #coefficients: fixed effect parameter estimates
    #linear.predictors: a vector of length N (N is the sample size) containing linear predictors
    #fitted.values: a vector of length N (N is the sample size) containing fitted mean values on the original scale
    #Y: a vector of length N (N is the sample size) containing final working vector
    #residuals: a vector of length N (N is the sample size) containing residuals on the original scale
    #sampleID: a vector of length N (N is the sample size) containing sample IDs used to fit the null model

    ```

2. variance ratio file (**input for Step 2**)

    ```
    less -S ./output/example_quantitative.varianceRatio.txt
    ```


3. association result file for the subset of randomly selected markers

    ```
    less -S ./output/example_quantitative_30markers.SAIGE.results.txt
    ```
