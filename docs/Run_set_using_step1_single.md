---
layout: default
title: Run both single-variant and set-based tests
nav_order: 7
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: false
has_toc: false
---

1. If SAIGE has been previously used for single-variants association tests. The null model fitting results from Step 1 can be reused for region/set-based tests

- First, estimate the categorical variance ratios with full and sparse GRMs
    - Use --sparseGRMFile and --sparseGRMSampleIDFile for the sparse GRM and --useSparseGRMforVarRatio=TRUE
    - Use --isCateVarianceRatio=TRUE to esrimate categotical variance ratios
    - Use --skipModelFitting=FASLE to avoid re-fitting the null model. With --outputPrefix=prefix, the prgoram will read in the model from prefix.rda. Using --outputPrefix_varRatio to specify a seperate file prefix for the new variance ratio results if you try to avoid overwrite the previous variance ratio file, otherwise --IsOverwriteVarianceRatioFile=TRUE can be used to overwrite the previous file. 
    - *Note: at least ~200 markers with 10<= MAC < 20 need to be added in the plink file specified with --plinkFile, which will be used for estimating variance ratio for lower MAF categories.* 

 
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
	--skipModelFitting=TRUE	\
        --outputPrefix_varRatio=./output/example_binary_cate \
        --useSparseGRMforVarRatio=TRUE
    ```

2. If no previous SAIGE jobs were run, please follow the step 1 and 2 for set-based tests, Use --is_single_in_groupTest=TRUE to also output single-variant assoc tests


a. (fast) Use sparse GRM to fit the null model, estimate the variance ratios, and run a faster version of the associaiton tests

    ```
    Rscript step1_fitNULLGLMM.R     \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forCate_vr       \
        --useSparseGRMtoFitNULL=TRUE    \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --isCateVarianceRatio=TRUE      \
        --outputPrefix=./output/example_binary_sparseGRM        \
        --IsOverwriteVarianceRatioFile=TRUE


    #Step 2 for single variant tests only

    Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --sampleFile=./input/samplelist.txt \
        --AlleleOrder=ref-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_bgen_Firth.txt    \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary_sparseGRM_vr.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM_vr.varianceRatio.txt  \
        --LOCO=FALSE \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05 \
        --is_output_moreDetails=TRUE \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt \
        --is_fastTest=TRUE    


   #Step 2 for set-based tests and also output single-variant tests results with --is_single_in_groupTest=TRUE

    Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./output/genotype_100markers_bgen_groupTest_out.txt \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM_vr.varianceRatio.txt      \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt  \
        --groupFile=./input/group_new_chrposa1a2.txt    \
        --annotation_in_groupTest="lof,missense:lof,missense:lof:synonymous"        \
        --maxMAF_in_groupTest=0.0001,0.001,0.01 \
        --is_output_markerList_in_groupTest=TRUE \
        --is_single_in_groupTest=TRUE \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05 \
        --LOCO=FALSE \
        --is_fastTest=TRUE

    ```


b. (slower than a) Use full GRM to fit the null model, estimate the variance ratios, and run a faster version of the associaiton tests

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
        --useSparseGRMforVarRatio=TRUE  \
        --IsOverwriteVarianceRatioFile=TRUE

    #Step 2 for single variant tests only
    Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --sampleFile=./input/samplelist.txt \
        --AlleleOrder=ref-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_bgen_fullGRMforNull_with_vr.txt    \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary_fullGRM.rda \
        --varianceRatioFile=./output/example_binary_fullGRM.varianceRatio.txt \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05 \
        --is_output_moreDetails=TRUE    \
        --LOCO=TRUE

     #Step 2 for set-based tests and also output single-variant tests results with --is_single_in_groupTest=TRUE
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
        --varianceRatioFile=./output/example_binary_fullGRM.varianceRatio.txt   \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt  \
        --groupFile=./input/group_new_chrposa1a2.txt    \
        --annotation_in_groupTest="lof,missense:lof,missense:lof:synonymous"        \
        --maxMAF_in_groupTest=0.0001,0.001,0.01 \
        --is_output_markerList_in_groupTest=TRUE \
        --is_single_in_groupTest=TRUE \
        --is_fastTest=TRUE

