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
* a9 and a10 are cateogorical covariats and will be re-write for different levels (--qCovarColList)

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
        --varianceRatioFile=./output/example_binary.varianceRatio.txt

```

### Example 2
* Binary trait (--traitType=binary)
* Fitting the null model using a sparse GRM  (--useSparseGRMtoFitNULL=TRUE, --sparseGRMFile, --sparseGRMSampleIDFile)
* Do not estimate the variance ratio in Step 1.
* Only one CPU is used when a sparse GRM is used for fitting the null model and LOCO won't be applied
* Use PLINK input for the genotypes/dosages in Step 2
* The effect sizes of markers with p-value <= pCutoffforFirth will be estimated through the Firth's Bias-Reduced Logistic Regression --is_Firth_beta=TRUE and --pCutoffforFirth=0.01 (NOTE this option is under evaluation) 

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
        --outputPrefix=./output/example_binary_sparseGRM

    Rscript step2_SPAtests.R        \
        --bedFile=./input/genotype_100markers.bed       \
        --bimFile=./input/genotype_100markers.bim       \
        --famFile=./input/genotype_100markers.fam       \
        --AlleleOrder=alt-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_plink_step1withSparseGRM_Firth.txt \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary_sparseGRM.rda \
        --is_output_moreDetails=TRUE    \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt	\
        --is_Firth_beta=TRUE	\
        --pCutoffforFirth=0.01 

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
        --covarColList=x1,x2,a9,a10 \
        --qCovarColList=a9,a10  \
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
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly \
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
        --vcfFileIndex=./input/nfam_1000_MAF0.2_nMarker1_nseed200.vcf.gz.tbi \
        --vcfField=GT \
        --chrom=1 \
        --minMAF=0.0001 \
        --minMAC=1 \
        --GMMATmodelFile=./output/example_binary_positive_signal.rda \
        --varianceRatioFile=./output/example_binary_positive_signal.varianceRatio.txt \
        --SAIGEOutputFile=./output/example_binary_positive_signal.assoc.step2.txt
```
