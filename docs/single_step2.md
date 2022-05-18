---
layout: default
title: Step 2
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Single-variant test
---

## Step 2: performing single-variant association tests

* For **binary traits**, saddle point approximation is used to account for case-control imbalance. 
* File formats for dosages/genotypes of genetic variants to be tested can be used: PLINK, VCF, [BGEN](https://bitbucket.org/gavinband/bgen/overview), [SAV](https://github.com/statgen/savvy)
* Conditional analysis based summary stats can be performed (--condition) can be performed in Step 2 
* To query and test a subset of markers 
* * both variant IDs (chr:pos:ref:alt) and range of chromosome positions (chr start end) can be specified for BGEN input (--idstoIncludeFile, --rangestoIncludeFile)
* --markers_per_chunk can be used to specify the number of markers to test and output as one chunk. default=10000. Note that a small number may slow down the job. It is required that this number is >= 1000.  
* If LOCO=TRUE (by default), --chrom MUST be specified, so genotype/dosage file should only contain one chromosome 
* For VCF/BCF/SAV input, --vcfField=DS to test dosages and  --vcfField=GT to test genotypes
* By default, missing genotypes/dosages will be imputed as the best guessed gentoypes/dosages (as round(2*freq) with --impute_method=best_guess). Note that currently dropping samples with missing genotypes/dosages is not supported
* --sampleFile is used specify a file with sample IDs for bgen file. 


```
#check the help info for step 2
Rscript step2_SPAtests.R --help
```
* For binary traits, use *--is_output_moreDetails=TRUE* to output heterozygous and homozygous counts as well as allele frequencies in cases and controls
* For binary traits,, effect sizes can be estimated more accurately through the Firth's Bias-Reduced Logistic Regression by setting
* --is_Firth_beta=TRUE and --pCutoffforFirth=0.05. The effect sizes of markers with p-value <= pCutoffforFirth will be estimated through the Firth's Bias-Reduced Logistic Regression. 


### full GRM was used for fitting the model in Step 1. Variance ratio MUST be estimated.

* --LOCO=TRUE is highly recommended to avoid proximal contamination
* Using **--bgenFile, --bgenFileIndex, --AlleleOrder, --sampleFile** for bgen input

```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --sampleFile=./input/samplelist.txt \
        --AlleleOrder=ref-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_bgen_fullGRMforNull_with_vr.txt    \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.05 \
        --is_output_moreDetails=TRUE    \
        --LOCO=TRUE
```

### Sparse GRM was used for fitting the model in Step 1. Variance ratio is estimated
* For Step 1 generated with version < 1.0.6, DO NOT specify --sparseGRMFile and --sparseGRMSampleIDFile
* For Step 1 generated with version >= 1.0.6. Please specify --sparseGRMFile and --sparseGRMSampleIDFile. Specify --is_fastTest=TRUE for fast run


```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --sampleFile=./input/samplelist.txt \
        --AlleleOrder=ref-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_bgen_Firth.txt    \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary_sparseGRM_vr.rda \
        --varianceRatioFile=./output/example_binary_sparseGRM_vr.varianceRatio.txt  \
        --LOCO=FALSE \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.1 \
        --is_output_moreDetails=TRUE \
        --sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt \
        --is_fastTest=TRUE
```

### Sparse GRM was used for fitting the model in Step 1. Variance ratio was not estiamted in Step 1. Please specify --sparseGRMFile and --sparseGRMSampleIDFile. 

```
Rscript step2_SPAtests.R        \
        --bgenFile=./input/genotype_100markers.bgen    \
        --bgenFileIndex=./input/genotype_100markers.bgen.bgi \
        --sampleFile=./input/samplelist.txt \
        --AlleleOrder=ref-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_bgen_sparseGRMforNull_no_vr.txt    \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary_sparseGRMforNull_no_vr.rda \
        --LOCO=FALSE \
        --is_Firth_beta=TRUE    \
        --pCutoffforFirth=0.1 \
        --is_output_moreDetails=TRUE	\
	--sparseGRMFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx   \
        --sparseGRMSampleIDFile=output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt
```

### More input file formats are supported: PLINK, VCF, and BCF

* Using *--bedFile, --bimFile, --famFile, --AlleleOrder* for PLINK input

``` 
Rscript step2_SPAtests.R        \
        --bedFile=./input/genotype_100markers.bed       \
        --bimFile=./input/genotype_100markers.bim       \
        --famFile=./input/genotype_100markers.fam       \
        --AlleleOrder=alt-first \
        --SAIGEOutputFile=./output/genotype_100markers_marker_plink.txt \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --LOCO=TRUE \
        --is_output_moreDetails=TRUE
```


* Using *--vcfFile， --vcfFileIndex， --vcfField, --chrom* for VCF, BCF, and SAV input
* Pleas refer to the Set-based test step 2 (example #8) for more example codes when using VCF files as input

```
Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/genotype_100markers_marker_vcf.txt \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --LOCO=FALSE \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --is_output_moreDetails=TRUE
```


## Conditional analysis

* --condition = Genetic marker ids (**chr:pos:ref:alt**) separated by comma. e.g.chr3:101651171:C:T,chr3:101651186:G:A
* conditioning markers MUST be specified in the same order as stored in the dosage file (e.g. in the VCF file)
```
Rscript step2_SPAtests.R        \
        --vcfFile=./input/genotype_100markers.vcf.gz    \
        --vcfFileIndex=./input/genotype_100markers.vcf.gz.csi     \
        --vcfField=GT   \
        --SAIGEOutputFile=./output/genotype_100markers_marker_vcf_cond.txt \
        --chrom=1       \
        --minMAF=0 \
        --minMAC=20 \
        --GMMATmodelFile=./output/example_binary.rda \
        --varianceRatioFile=./output/example_binary.varianceRatio.txt   \
        --is_output_moreDetails=TRUE    \
        --condition=1:13:A:C,1:79:A:C
```


## Input files

1. (Required) Dosage file
SAIGE supports different formats for dosages: PLINK, VCF, BCF, [BGEN](http://www.well.ox.ac.uk/~gav/bgen_format/bgen_format_v1.2.html) and [SAV](https://github.com/statgen/savvy).

    * PLINK

    ```
    ./input/genotype_100markers.bed
    ./input/genotype_100markers.bim
    ./input/genotype_100markers.fam
    ```

    * BGEN

    ```
    ./input/genotype_100markers.bgen
    ./input/genotype_100markers.bgen.bgi 
    ```

    * VCF containing genotypes

    ```
    #go to input
    cd ./input
    #index file can be generated using tabix
    tabix --csi -p vcf genotype_10markers.vcf.gz
    zcat genotype_10markers.vcf.gz | less -S
    genotype_10markers.vcf.gz.csi
    ```

    * VCF containing dosages

    ```
    #index file can be generated using tabix
    tabix --csi -p vcf dosage_10markers.vcf.gz    
    zcat dosage_10markers.vcf.gz | less -S
    dosage_10markers.vcf.gz.csi
    ```

    * SAV

    ```
    dosage_10markers.sav
    dosage_10markers.sav.s1r
    ```

2. (Optional. Only for BGEN file not containing sample IDs) Sample file <br\>
The option was originally for BGEN file that does not contain sample information. 

The file can be in two different formats: 
contains one column for sample IDs corresponding to the sample order in the dosage file. **No header is included.** 
    ```
    less -S ./input/samplelist.txt
    ```
or

in the default bgen .sample format

    ```
    less -S ./input/genotype_100markers_2chr.sample
    ```


3. (Required. Output in Step 1) Model file from step 1

    ```
    ./output/example_binary.rda
    ```

4. **(Optional. Output in Step 1)**Variance ratio file from step 1

    ```
    ./output/example_binary.varianceRatio.txt
    ```

5. **(Optional. Same as the input in Step 1)** sparse GRM file and the sample ID file for the sparse GRM. Please see Step 0 for more details.

    ```
    ./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx
    ./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt

    ##the sparse matrix can be read and viewed using the R library Matrix
    library(Matrix)
    sparseGRM = readMM("./output/sparseGRM_relatednessCutoff_0.125_1000_randomMarkersUsed.sparseGRM.mtx") 
    ```

## Output file
1. A file with association test results

    ```
    less -S ./output/genotype_100markers_marker_vcf.txt

    ```
2. An index for the output file. This file contains the progress of the association test. If specifying --is_overwrite_output=FALSE in Step 2, the program will check this index and continue the unfinished analysis instead of starting over from the beginining

    ```
    less -S ./output/genotype_100markers_marker_vcf.txt.index 
    ```

NOTE:
* Association results are with regard to Allele2. 


```
#check the header
head -n 1 output/genotype_100markers_marker_vcf_cond.txt
```

<img src="{{site.baseurl | prepend: site.url}}/assets/img/SAIGE-step2-output-header.png" width="500">

```
CHR: chromosome
POS: genome position 
SNPID: variant ID
Allele1: allele 1
Allele2: allele 2
AC_Allele2: allele count of allele 2
AF_Allele2: allele frequency of allele 2
MissingRate: missing rate (If the markers in the dosage/genotype input are imputed with is_imputed_data=TRUE, imputationInfo will be output instead of MissingRate)
imputationInfo: imputation info (output with is_imputed_data=TRUE). If not in dosage/genotype input file, will output 1
BETA: effect size of allele 2
SE: standard error of BETA
Tstat: score statistic of allele 2
var: estimated variance of score statistic
p.value: p value (with SPA applied for binary traits)
p.value.NA: p value when SPA is not applied (only for binary traits)
Is.SPA.converge: whether SPA is converged or not (only for binary traits)

#if --condition= is used for conditioning analysis, the conditional analysis results will be output  
BETA_c, SE_c, Tstat_c, var_c, p.value_c, p.value.NA_c 


AF_case: allele frequency of allele 2 in cases (only for binary traits)
AF_ctrl: allele frequency of allele 2 in controls (only for binary traits)
N_case: sample size in cases (only for binary traits)
N_ctrl: sample size in controls (only for binary traits)

if --is_output_moreDetails=TRUE
homN_Allele2_cases, hetN_Allele2_cases, homN_Allele2_ctrls, hetN_Allele2_ctrls will be output for sample sizes with different genotypes in cases and controls (only for binary traits)

```
