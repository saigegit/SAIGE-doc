---
layout: default
title: Step 2
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Single-variant test
---

# Step 2: performing single-variant association tests

* For **binary traits**, saddle point approximation is used to account for case-control imbalance. 
* File formats for dosages/genotypes of genetic variants to be tested can be used: PLINK, VCF, [BGEN](https://bitbucket.org/gavinband/bgen/overview), [SAV](https://github.com/statgen/savvy)
* Conditional analysis based summary stats can be performed (--condition) can be performed in Step 2 
* To query and test a subset of markers 
* * both variant IDs and range of chromosome positions can be specified for BGEN input (--idstoIncludeFile, --rangestoIncludeFile)
* If LOCO=TRUE, --chrom MUST be specified 
* For VCF/BCF/SAV input, --vcfField=DS to test dosages and  --vcfField=GT to test genotypes
* Samples with missing genotypes/dosages can be imputed with the mean dosages (--impute_method=mean) or as minor allele homozygotes (--impute_method=minor). Note that currently dropping samples with missing genotypes/dosages is not supported
* --sampleFile is used specify a file with sample IDs for bgen file. Please **DO NOT** include a header in the file. 


```
#check the help info for step 2
Rscript step2_SPAtests.R --help
```

## Single-variant association tests

* For binary traits, use *--is_output_moreDetails=TRUE* to output heterozygous and homozygous counts in cases and controls 

* Using *--bgenFile, --bgenFileIndex, --AlleleOrder, --sampleFile* for bgen input

```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --sampleFile=./input/samplelist.txt \
        --AlleleOrder=ref-first \
        --SAIGEOutputFile=./genotype_100markers_single_out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --numLinesOutput=10	\
	
```

* Using *--bedFile, --bimFile, --famFile, --AlleleOrder* for PLINK input

``` 
Rscript step2_SPAtests.R        \
        --bedFile=./input/genotype_100markers.bed       \
        --bimFile=./input/genotype_100markers.bim       \
        --famFile=./input/genotype_100markers.fam       \
        --AlleleOrder=alt-first \
        --SAIGEOutputFile=./output/new_single_plink.txt \
        --chrom=1       \
        --LOCO=TRUE     \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --is_output_moreDetails=TRUE    \
```


* Using *--vcfFile， --vcfFileIndex， --vcfField* for VCF, BCF, and SAV input

```
Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/new_single_vcf.txt \
        --chrom=1       \
        --LOCO=TRUE     \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --is_output_moreDetails=TRUE
```

## Conditional analysis

* --condition = Genetic marker ids (**chr:pos_ref/alt for VCF,BCF,SAV or marker id for PLINK and BGEN**) separated by comma. e.g.chr3:101651171_C/T,chr3:101651186_G/A

```
Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/new_single_vcf.txt \
        --chrom=1       \
        --LOCO=TRUE     \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --is_output_moreDetails=TRUE    \
        --condition=1:13_A/C,1:79_A/C
```


```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --SAIGEOutputFile=./genotype_100markers_single_out.txt \
        --chrom=1 \
        --LOCO=TRUE    \
        --AlleleOrder=ref-first \
        --minMAF=0 \
        --minMAC=1 \
        --sampleFile=./input/samplelist.txt \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --numLinesOutput=10     \
        --condition=rs13,rs79
```



## Input files

1. Dosage file
SAIGE supports different formats for dosages: PLINK, VCF, BCF, [BGEN](http://www.well.ox.ac.uk/~gav/bgen_format/bgen_format_v1.2.html) and [SAV](https://github.com/statgen/savvy).\
* PLINK

    ```
    ./input/genotype_100markers.bed
    ./input/genotype_100markers.bim
    ./input/genotype_100markers.fam
    ```

* BGEN

    ```
    genotype_100markers.bgen
    genotype_100markers.bgen.bgi 
    ```

* VCF containing genotypes

    ```
    zcat genotype_10markers.vcf.gz | less -S
    genotype_10markers.vcf.gz.tbi
    ```

* VCF containing dosages

    ```
    zcat dosage_10markers.vcf.gz | less -S
    dosage_10markers.vcf.gz.tbi
    ```

* SAV

    ```
    dosage_10markers.sav
    dosage_10markers.sav.s1r
    ```

2. Sample file <br\>
This file contains one column for sample IDs corresponding to the sample order in the dosage file. **No header is included.** 
The option was originally for BGEN file that does not contain sample information. 


    ```
    less -S sampleIDindosage.txt
    ```

3. Model file from step 1

    ```
    ./output/example.rda
    ```

4. Variance ratio file from step 1

    ```
    ./output/example.varianceRatio.txt
    ```

## Output file
A file with association test results

    ```
    less -S ./output/example.SAIGE.vcf.dosage.txt
    ```
NOTE:
* Association results are with regard to Allele2. 
* For binary traits, the header of the output file: 
``` CHR POS SNPID Allele1 Allele2 AC_Allele2 AF_Allele2 imputationInfo N BETA SE Tstat p.value p.value.NA Is.SPA.converge varT varTstar AF.Cases AF.Controls ```
* For quantitative traits, the header of the output file: 
``` CHR POS SNPID Allele1 Allele2 AC_Allele2 AF_Allele2 imputationInfo N BETA SE Tstat p.value varT varTstar```

```
CHR: chromosome
POS: genome position 
SNPID: variant ID
Allele1: allele 1
Allele2: allele 2
AC_Allele2: allele count of allele 2
AF_Allele2: allele frequency of allele 2
imputationInfo: imputation info. If not in dosage/genotype input file, will output 1
N: sample size
BETA: effect size of allele 2
SE: standard error of BETA
Tstat: score statistic of allele 2
p.value: p value (with SPA applied for binary traits)
p.value.NA: p value when SPA is not applied (only for binary traits)
Is.SPA.converge: whether SPA is converged or not (only for binary traits)
varT: estimated variance of score statistic with sample relatedness incorporated
varTstar: variance of score statistic without sample relatedness incorporated
AF.Cases: allele frequency of allele 2 in cases (only for binary traits and if --IsOutputAFinCaseCtrl=TRUE)
AF.Controls: allele frequency of allele 2 in controls (only for binary traits and if --IsOutputAFinCaseCtrl=TRUE)
Tstat_cond, p.value_cond, varT_cond, BETA_cond, SE_cond: summary stats for conditional analysis
```

#### An example with a signal
* p.value in Step 2 for the marker is ~4.16 x 10^-7

```
Rscript step1_fitNULLGLMM.R     \
        --plinkFile=./input/nfam_100_nindep_0_step1_includeMoreRareVariants_poly \
        --phenoFile=./input/Prev_0.1_nfam_1000.pheno_positive_pheno.txt \
        --phenoCol=y \
        --covarColList=x1,x2 \
        --sampleIDColinphenoFile=IID \
        --traitType=binary        \
        --outputPrefix=./output/example_binary_positive_signal \
        --nThreads=4    \
        --LOCO=FALSE    \
        --minMAFforGRM=0.01

Rscript step2_SPAtests.R        \
        --vcfFile=./input/nfam_1000_MAF0.2_nMarker1_nseed200.vcf.gz \
        --vcfFileIndex=./input/nfam_1000_MAF0.2_nMarker1_nseed200.vcf.gz.tbi \
        --vcfField=GT \
        --chrom=1 \
        --minMAF=0.0001 \
        --minMAC=1 \
        --GMMATmodelFile=./output/example_binary_positive_signal.rda \
        --varianceRatioFile=./output/example_binary_positive_signal.varianceRatio.txt \
        --SAIGEOutputFile=./output/example_binary_positive_signal.assoc.step2.txt \
        --numLinesOutput=2 \
        --IsOutputAFinCaseCtrl=TRUE     \
```
