---
layout: default
title: Examples
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Set-based test
---


### Example 1
* Fitting the null model using a sparse GRM (--useSparseGRMtoFitNULL=TRUE) and no variance ratios are estiamted (--skipVarianceRatioEstimation=TRUE)
* a9 and a10 are cateogorical covariats and will be re-write for different levels (--qCovarColList)
* The same sparse GRM files needs to be used in Step 1 and Step 2 (for variance ratio approach)
* Use multiple maskes for each set (gene or region)
    * Three annotation maskes are applied: lof only, missense+lof, and missense+lof+synonymous (--function_group_test)
    * Three max MAF cutoffs are applied: 0.0001,0.001,0.01
    * p-values for the 3x3 tests for each set are combined based on Cauchy combination 
 
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
        --skipVarianceRatioEstimation=TRUE      \
        --outputPrefix=./output/example_binary_sparseGRM
    
    Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./output/genotype_100markers_bgen_groupTest_out_sparseGRMforStep1.txt \
        --chrom=1 \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --groupFile=./input/group_new_chrposa1a2.txt    \
        --function_group_test="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAFforGroupTest=0.0001,0.001,0.01
    ```


### Example 2
* Fitting the null model using a full GRM that will be calculated on-the-fly using genotypes in the plink file (*--plinkFile=*, *--useSparseGRMtoFitNULL=FALSE*)
* a9 and a10 are cateogorical covariats and will be re-write for different levels (--qCovarColList)
* Use both a full GRM (to be constrcuted using genotypes in the PLINK file) and a sparse GRM with *--useSparseGRMforVarRatio=TRUE*
    ** Use *--sparseGRMFile* for the file containing the sparse GRM
    ** Use *--sparseGRMSampleIDFile* for the file containing the IDs for samples in the sparse GRM
* Multiple variance ratios need to be estimated based on different minor allele count categories with *--isCateVarianceRatio=TRUE*
    ** By default, two variance ratios are estiamted for 10 <= MAC < 20 and MAC >= 20.
    ** Use *--cateVarRatioMinMACVecExclude* and *--cateVarRatioMaxMACVecInclude* to modify the MAC categories
    ** Note that the PLINK file need to contain at least 1000 variants whose MAC fall in these categories
    ** Different from Step 1 in SAIGE for single-variant tests in SAIGE, in which only a single variance ratio is estiamted
* The same sparse GRM files needs to be used in Step 1 and Step 2 (for variance ratio approach) 

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

    Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./output/genotype_100markers_bgen_groupTest_out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_fullGRM.rda \
        --varianceRatioFile=./output/example_binary_cate.varianceRatio.txt      \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --groupFile=./input/group_new_chrposa1a2.txt    \
        --function_group_test="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAFforGroupTest=0.0001,0.001,0.01

```
