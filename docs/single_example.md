---
layout: default
title: Examples
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Single-variant test
---

### Example 1
* Binary trait (--traitType=binary)
* Fitting the null model using a full GRM that will be calculated on-the-fly using genotypes in the plink file (--plinkFile=)
* Estimating the variance ratio in Step 1, which will be used as input for Step 2
* Using 2 CPUs for step 1 (--nThreads)
* x2 are cateogorical covariats and will be re-write for different levels (--qCovarColList)

```
    Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary \
        --nThreads=2    \
        --IsOverwriteVarianceRatioFile=TRUE


    Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/genotype_100markers_marker_vcf.txt \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt	\
	--is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05 \
        --is_output_moreDetails=TRUE    \
        --LOCO=TRUE

```

### Example 2
* Binary trait (--traitType=binary)
* Fitting the null model using a sparse GRM  (--useSparseGRMtoFitNULL=TRUE, --sparseGRMFile, --sparseGRMSampleIDFile)
* Estimate the variance ratio in Step 1 with markers with MAC >= 20 randomly selected from --plinkFile. Only a small subset of randomly selected markers are needed in the plink file. e.g. **--plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr_random1000**. This file contains 1000 markers from the large plink file containing 100,000s markers for full GRM. This can reduce the memory usage for reading in plink file.
* Only one CPU is used when a sparse GRM is used for fitting the null model and LOCO won't be applied
* When the sparse GRM was used for fitting the null model, set --LOCO=FASLE in Step 2
* Use PLINK input for the genotypes/dosages in Step 2
* The effect sizes of markers with p-value <= pCutoffforFirth will be estimated through the Firth's Bias-Reduced Logistic Regression --is_Firth_beta=TRUE and --pCutoffforFirth=0.01 (NOTE this option is under evaluation) 

```
    Rscript step1_fitNULLGLMM.R     \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --useSparseGRMtoFitNULL=TRUE    \
	--plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr_random1000 \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary_sparseGRM \
	--IsOverwriteVarianceRatioFile=TRUE

    Rscript step2_SPAtests.R        \
        --bedFile=./input/genotype_100markers.bed       \
        --bimFile=./input/genotype_100markers.bim       \
        --famFile=./input/genotype_100markers.fam       \
        --AlleleOrder=alt-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_plink_step1withSparseGRM_Firth.txt \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM.varianceRatio.txt   \
        --is_output_moreDetails=TRUE    \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05  \
        --LOCO=FALSE    \


    ## If the sample size is relatively small, e.g. N < 1000, may consider using sparse GRM in step 2 using 
      --sparseGRMFile, --sparseGRMSampleIDFile, --is_fastTest=TRUE
      For any markers using the variance ratio for no GRM that have p-valu < 0.05, sparse GRM will be used directly for update the p-value
    ## Please make sure to use verion >= 1.1.8 for this scenario  


    Rscript step2_SPAtests.R        \
        --bedFile=./input/genotype_100markers.bed       \
        --bimFile=./input/genotype_100markers.bim       \
        --famFile=./input/genotype_100markers.fam       \
        --AlleleOrder=alt-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_plink_step1withSparseGRM_Firth.txt \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM.varianceRatio.txt   \
        --is_output_moreDetails=TRUE    \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt	\
        --is_Firth_beta=TRUE	\
        --pCutoffforFirth=0.05	\
	--LOCO=FALSE	\
	--is_fastTest=TRUE 

```

### Example 3
* Quantitative trait (--traitType=quantitative) and needs inverse normalization (--invNormalize=TRUE)
* Fitting the null model using a sparse GRM  (--useSparseGRMtoFitNULL=TRUE, --sparseGRMFile, --sparseGRMSampleIDFile)
* Estimate the variance ratio in Step 1 (--plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr) 
* Only one CPU is used when a sparse GRM is used for fitting the null model and LOCO won't be applied
* --chrom needs to be specified for VCF input

```
    Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --useSparseGRMtoFitNULL=TRUE    \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_quantitative \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
        --sampleIDColinphenoFile=IID \
        --traitType=quantitative        \
	--invNormalize=TRUE	\
        --outputPrefix=./output/example_quantitative_sparseGRM


    Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
	--chrom=1	\
        --SAIGEOutputFile=./output/genotype_100markers_marker_vcf_step1withSparseGRM.txt \
        --minMAF=0 \
        --minMAC=20 \
	--LOCO=FALSE	\
        --GMMATmodelFile=./output/example_quantitative_sparseGRM.rda \
        --is_output_moreDetails=TRUE	\
        --varianceRatioFile=./output/example_quantitative_sparseGRM.varianceRatio.txt

```

### Example 4
* Binary trait (--traitType=binary)
* Fitting the null model using a full GRM that will be calculated on-the-fly using genotypes in the plink file (--plinkFile=)
* Estimating the variance ratio in Step 1, which will be used as input for Step 2
* Overwrite the existing step 1 output --IsOverwriteVarianceRatioFile=TRUE
* p.value in Step 2 for the marker is ~3.74 x 10^-7

```
Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr	\
        --phenoFile=./input/Prev_0.1_nfam_1000.pheno_positive_pheno.txt \
        --phenoCol=y \
        --covarColList=x1,x2 \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary_positive_signal \
        --nThreads=4	\
	--IsOverwriteVarianceRatioFile=TRUE

Rscript step2_SPAtests.R        \
        --vcfFile=./input/nfam_1000_MAF0.2_nMarker1_nseed200.vcf.gz \
        --vcfFileIndex=./input/nfam_1000_MAF0.2_nMarker1_nseed200.vcf.gz.csi \
        --vcfField=GT \
        --chrom=1 \
        --minMAF=0.0001 \
        --minMAC=1 \
        --GMMATmodelFile=./output/example_binary_positive_signal.rda \
        --varianceRatioFile=./output/example_binary_positive_signal.varianceRatio.txt \
        --SAIGEOutputFile=./output/example_binary_positive_signal.assoc.step2.txt \
	--is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05  \
        --LOCO=TRUE
```


### Example 5: include rarer variants in the single-variant assoc tests (--minMAC=0.5)
* Binary trait (--traitType=binary)

#### Example 5.1: Fitting the null model using a full GRM

* Fitting the null model using a full GRM that will be calculated on-the-fly using genotypes in the plink file (--plinkFile=). 
* Estimating the variance ratios for multiple MAC groups in Step 1. e.g., 0.5 < MAC <= 1.5, 1.5 < MAC <= 2.5, 2.5 < MAC <= 3.5, 3.5 < MAC <= 4.5, 4.5 < MAC <= 5.5, 5.5 < MAC <= 10.5, 10.5 < MAC <= 20.5, and MAC > 20.5, which will be used as input for Step 2 
```
     --isCateVarianceRatio=TRUE
     --cateVarRatioMaxMACVecInclude=1.5,2.5,3.5,4.5,5.5,10.5,20.5
     --cateVarRatioMinMACVecExclude=0.5,1.5,2.5,3.5,4.5,5.5,10.5,20.5
```
** Note that the PLINK file need to contain at least 200 variants whose MAC fall in these categories.

* Using 2 CPUs for step 1 (--nThreads)
* x2 are cateogorical covariates and will be re-write for different levels (--qCovarColList)

* In Step 2, any markers with MAC <= 4 will be tested using efficient resampling because SPA does not work for MAC <= 4 (by default, --max_MAC_for_ER=4). 
* Use PLINK input for the genotypes/dosages in Step 2
* The effect sizes of markers with p-value <= pCutoffforFirth will be estimated through the Firth's Bias-Reduced Logistic Regression --is_Firth_beta=TRUE and --pCutoffforFirth=0.05

```
    Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr  \
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary \
        --nThreads=2    \
        --IsOverwriteVarianceRatioFile=TRUE \
	--isCateVarianceRatio=TRUE \
	--cateVarRatioMaxMACVecInclude=1.5,2.5,3.5,4.5,5.5,10.5,20.5 \
	--cateVarRatioMinMACVecExclude=0.5,1.5,2.5,3.5,4.5,5.5,10.5,20.5


    Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/genotype_100markers_marker_vcf.txt \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=0.5 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05 \
        --is_output_moreDetails=TRUE    \
        --LOCO=TRUE	\
	  --cateVarRatioMaxMACVecInclude=1.5,2.5,3.5,4.5,5.5,10.5,20.5 \
        --cateVarRatioMinMACVecExclude=0.5,1.5,2.5,3.5,4.5,5.5,10.5,20.5

```

#### Example 5.2: Fitting the null model using a sparse GRM
* Fitting the null model using a sparse GRM  (--useSparseGRMtoFitNULL=TRUE, --sparseGRMFile, --sparseGRMSampleIDFile)
* Estimating the variance ratios for multiple MAC groups in Step 1. e.g., 0.5 < MAC <= 1.5, 1.5 < MAC <= 2.5, 2.5 < MAC <= 3.5, 3.5 < MAC <= 4.5, 4.5 < MAC <= 5.5, 5.5 < MAC <= 10.5, 10.5 < MAC <= 20.5, and MAC > 20.5, which will be used as input for Step 2
```
     --isCateVarianceRatio=TRUE
     --cateVarRatioMaxMACVecInclude=1.5,2.5,3.5,4.5,5.5,10.5,20.5
     --cateVarRatioMinMACVecExclude=0.5,1.5,2.5,3.5,4.5,5.5,10.5,20.5
```
** Note that the PLINK file need to contain at least 200 variants whose MAC fall in these categories.
** This file may only contain a subset of random markers for estimating variance ratios from the large plink file that contains markers for the full GRM. This can reduce the memory usage for reading in plink file (--plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forCate_vr)
* Only one CPU is used when a sparse GRM is used for fitting the null model and LOCO won't be applied


#(Optional) get ids for 200 random markers for each MAC category.
    ## calcuate allele counts for each marker in the large plink file
    plink2 --bfile ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr --freq counts --out ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr

    ## randomly extract IDs for markers falling in the two MAC categories
    cat <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 1.5 && (2*$6-$5) >= 0.5) || ($5 < 1.5 && $5 >= 0.5) {print $2}' | shuf -n 200) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 2.5 && (2*$6-$5) >= 1.5) || ($5 < 2.5 && $5 >= 1.5) {print $2}' | shuf -n 200) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 3.5 && (2*$6-$5) >= 2.5) || ($5 < 3.5 && $5 >= 2.5) {print $2}' | shuf -n 200) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 4.5 && (2*$6-$5) >= 3.5) || ($5 < 4.5 && $5 >= 3.5) {print $2}' | shuf -n 200) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 5.5 && (2*$6-$5) >= 4.5) || ($5 < 5.5 && $5 >= 4.5) {print $2}' | shuf -n 200) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 10.5 && (2*$6-$5) >= 5.5) || ($5 < 10.5 && $5 >= 5.5) {print $2}' | shuf -n 200) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk '((2*$6-$5) < 20.5 && (2*$6-$5) >= 10.5) || ($5 < 20.5 && $5 >= 10.5) {print $2}' | shuf -n 200) \
    <(tail -n +2 ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.acount | awk ' $5 >= 20 && (2*$6-$5)>= 20.5 {print $2}' | shuf -n 200) > ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forMoreCate_vr.markerid.list


    ## extract markers from the large plink file
    plink2 --bfile ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr --extract ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forMoreCate_vr.markerid.list --make-bed --out ./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forMoreCate_vr





* When the sparse GRM was used for fitting the null model, set --LOCO=FASLE in Step 2
* Use PLINK input for the genotypes/dosages in Step 2
* The effect sizes of markers with p-value <= pCutoffforFirth will be estimated through the Firth's Bias-Reduced Logistic Regression --is_Firth_beta=TRUE and --pCutoffforFirth=0.01 (NOTE this option is under evaluation)

```
    Rscript step1_fitNULLGLMM.R     \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt     \
        --useSparseGRMtoFitNULL=TRUE    \
	--plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly_22chr.forMoreCate_vr	\
        --phenoFile=./input/pheno_1000samples.txt_withdosages_withBothTraitTypes.txt \
        --phenoCol=y_binary \
        --covarColList=x1,x2 \
        --qCovarColList=x2  \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary_sparseGRM \
        --IsOverwriteVarianceRatioFile=TRUE	\
	     --isCateVarianceRatio=TRUE	\
     --cateVarRatioMaxMACVecInclude=1.5,2.5,3.5,4.5,5.5,10.5,20.5	\
     --cateVarRatioMinMACVecExclude=0.5,1.5,2.5,3.5,4.5,5.5,10.5,20.5


    Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/genotype_100markers_marker_vcf.txt \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=0.5 \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM.varianceRatio.txt   \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05 \
        --is_output_moreDetails=TRUE    \
        --LOCO=FALSE     \
          --cateVarRatioMaxMACVecInclude=1.5,2.5,3.5,4.5,5.5,10.5,20.5 \
        --cateVarRatioMinMACVecExclude=0.5,1.5,2.5,3.5,4.5,5.5,10.5,20.5 



```
