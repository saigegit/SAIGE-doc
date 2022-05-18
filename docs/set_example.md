---
layout: default
title: Examples
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Set-based test
---


### Example 1
* Fitting the null model using a sparse GRM (--useSparseGRMtoFitNULL=TRUE) and estimating categorical variance ratios (--isCateVarianceRatio=TRUE)
* Multiple variance ratios need to be estimated based on different minor allele count categories with *--isCateVarianceRatio=TRUE*
    ** Different from Step 1 in SAIGE for single-variant tests in SAIGE, in which only a single variance ratio is estiamted
    ** By default, two variance ratios are estiamted for 10 <= MAC < 20 and MAC >= 20.
    ** Use *--cateVarRatioMinMACVecExclude* and *--cateVarRatioMaxMACVecInclude* to modify the MAC categories
    ** Note that the PLINK file need to contain at least 200 variants whose MAC fall in these categories.
    ** This PLINK file is ONLY used for variance ratio estimation and contains random markers extracted from the PLINK file that is LD pruned and used for GRM and variance ratio estimation
* x2 are cateogorical covariats and will be re-write for different levels (--qCovarColList)
* The same sparse GRM files needs to be used in Step 1 and Step 2 (for variance ratio approach)
* Use multiple masks for each set (gene or region)
    * Three annotation maskes are applied: lof only, missense+lof, and missense+lof+synonymous (--annotation_in_groupTest)
    * Three max MAF cutoffs are applied: 0.0001,0.001,0.01
    * p-values for the 3x3 tests for each set are combined based on Cauchy combination 
* Perform fast tests in which the null variance ratios are used for all groups. When p-value < 0.05, the sparse variance ratios are used (--is_fastTest=TRUE)

    ```
    #(Optional) get ids for 1000 random markers for each MAC category
    ## calcuate allele counts for each marker in the large plink file
    plink2 --bfile ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr --freq count --out ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr

    ## randomly extract IDs for markers falling in the two MAC categories
    cat <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 20 && (2*$6-$5) >= 10) || ($5 < 20 && $5 >= 10) {print $2}' | shuf -n 1000) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk ' $5 >= 20 && (2*$6-$5)>= 20 {print $2}' | shuf -n 1000) > ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forCate_vr.markerid.list


    ## extract markers from the large plink file
    plink2 --bfile ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr --extract ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forCate_vr.markerid.list --make-bed --out ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forCate_vr


    ##Step 1
    Rscript step1_fitNULLGLMM.R     \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --useSparseGRMtoFitNULL=TRUE    \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forCate_vr \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --isCateVarianceRatio=TRUE \
        --phenoCol=y_binary \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary_sparseGRM \
        --IsOverwriteVarianceRatioFile=TRUE
    
    ##Step 2
    Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./output/genotype_100markers_bgen_groupTest_out_sparseGRMforStep1.txt \
        --chrom=1 \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --LOCO=FALSE \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM.varianceRatio.txt \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --groupFile=./input/group_new_chrposa1a2.txt    \
        --annotation_in_groupTest=lof,missense:lof,missense:lof:synonymous        \
        --maxMAF_in_groupTest=0.0001,0.001,0.01 \
        --is_fastTest=TRUE
    ```


### Example 2
* Fitting the null model using a full GRM that will be calculated on-the-fly using genotypes in the plink file (*--plinkFile=*, *--useSparseGRMtoFitNULL=FALSE*)
* x2 are cateogorical covariats and will be re-write for different levels (--qCovarColList)
* Use both a full GRM (to be constrcuted using genotypes in the PLINK file) and a sparse GRM with *--useSparseGRMforVarRatio=TRUE*
    ** Use *--sparseGRMFile* for the file containing the sparse GRM
    ** Use *--sparseGRMSampleIDFile* for the file containing the IDs for samples in the sparse GRM
* Multiple variance ratios need to be estimated based on different minor allele count categories with *--isCateVarianceRatio=TRUE*
    ** By default, two variance ratios are estiamted for 10 <= MAC < 20 and MAC >= 20.
    ** Use *--cateVarRatioMinMACVecExclude* and *--cateVarRatioMaxMACVecInclude* to modify the MAC categories
    ** Note that the PLINK file need to contain at least 1000 variants whose MAC fall in these categories
    ** Different from Step 1 in SAIGE for single-variant tests in SAIGE, in which only a single variance ratio is estiamted
* The same sparse GRM files needs to be used in Step 1 and Step 2 (for variance ratio approach) 
* Output the marker lists for tests --is_output_markerList_in_groupTest=TRUE
* Perform the fast tests (--is_fastTest=TRUE) with leave-one-chromosome-out option (--LOCO=TRUE)

    ```
    Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
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
        --annotation_in_groupTest=lof,missense:lof,missense:lof:synonymous        \
        --maxMAF_in_groupTest=0.0001,0.001,0.01	\
	--is_output_markerList_in_groupTest=TRUE \
        --is_fastTest=TRUE

    ```

### Example 3 (Burden test only)
* Use the same step 1 output from Example 1 (or 2)
* --r.corr=1

    ```

    Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./output/genotype_100markers_bgen_groupTest_out_onlyBURDEN.txt \
        --chrom=1 \
        --LOCO=FALSE    \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM.varianceRatio.txt \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --groupFile=./input/group_new_chrposa1a2.txt    \
        --annotation_in_groupTest="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAF_in_groupTest=0.0001,0.001,0.01 \
        --r.corr=1
    ```
