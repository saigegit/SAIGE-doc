---
layout: default
title: Step 2
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Set-based test
---


## Step 2: performing the region- or gene-based association tests

* The command line is the same as the step 2 for single-variant assoc tests, except that a group file is specified (--groupFile)
* Each line is for one gene/set of variants. The first element is for gene/set name. 
* The rest of the line is for variant ids included in this gene/set. For vcf/sav, the genetic marker ids are in the format chr:pos_ref/alt. For bgen, the genetic marker ids should match the ids in the bgen file. Each element in the line is separated by tab.
**The marker ids in the group file for vcf/sav need to be sorted by chr and pos.**
* IsSingleVarinGroupTest=TRUE is to perform single-variant association tests as well for markers included in the gene-based tests
* --IsOutputBETASEinBurdenTest is to output effect sizes for burden tests (this option is still under development)
* Same as the single-variant association tests, conditional analysis based summary stats can be performed (--condition) can be performed in step 2 with dosage/genotype input file formats VCF, BGEN and SAV.



* Use *--function_group_test* to list different annotations
* Use *--maxMAFforGroupTest* for different max MAF cutoffs
* By default, SKAT-O, SKAT, and BURDEN tests are performed

    ```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./genotype_100markers_bgen_groupTest_out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_fullGRM.rda \
        --varianceRatioFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt   \
        --numLinesOutput=10     \
        --groupFile=./input/group_new_snpid.txt \
        --sparseSigmaFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseSigma.mtx       \
        --function_group_test="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAFforGroupTest=0.0001,0.001,0.01
    ```


* Specify *--r.corr=1* to only perform BURDEN test

    ```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./output/genotype_100markers_bgen_groupTest_onlyBURDEN.out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_fullGRM.rda \
        --varianceRatioFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt   \
        --numLinesOutput=10     \
        --groupFile=./input/group_new_snpid.txt \
        --sparseSigmaFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseSigma.mtx       \
        --function_group_test="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAFforGroupTest=0.0001,0.001,0.01  \
        --r.corr=1
    ```

* Use --condition= to perform conditioning analysis


    ```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./genotype_100markers_bgen_groupTest_conditional.out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_fullGRM.rda \
        --varianceRatioFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt   \
        --numLinesOutput=10     \
        --groupFile=./input/group_new_snpid.txt \
        --sparseSigmaFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseSigma.mtx       \
        --function_group_test="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAFforGroupTest=0.0001,0.001,0.01  \
        --condition=rs30,rs79
    ```


* PLINK file as input


    ```
Rscript step2_SPAtests.R        \
        --bedFile=./input/genotype_100markers.bed       \
        --bimFile=./input/genotype_100markers.bim       \
        --famFile=./input/genotype_100markers.fam       \
        --SAIGEOutputFile=./output/genotype_100markers_plink_groupTest_out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --AlleleOrder=alt-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_fullGRM.rda \
        --varianceRatioFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt   \
        --numLinesOutput=10     \
        --groupFile=./input/group_new_snpid.txt \
        --sparseSigmaFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseSigma.mtx       \
        --function_group_test="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAFforGroupTest=0.0001,0.001,0.01
    ```

* VCF file as input


    ```
Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/genotype_100markers_vcf_groupTest_out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --AlleleOrder=alt-first \
        --minMAF=0 \
        --minMAC=0.5 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary_fullGRM.rda \
        --varianceRatioFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt   \
        --numLinesOutput=10     \
        --groupFile=./input/group_new_chrposa1a2.txt \
        --sparseSigmaFile=./output/example_binary_fullGRM_sparseGRM_categorical_varRatio.varianceRatio.txt_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseSigma.mtx       \
        --function_group_test="lof,missense;lof,missense;lof;synonymous"        \
        --maxMAFforGroupTest=0.0001,0.001,0.01
    ```
