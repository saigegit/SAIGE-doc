---
layout: default
title: Estimate effect sizes and heritability for rare variants  
nav_order: 9
description: "RareEffect is a novel method to estimate the variant-level effect size (beta) and region-level (gene-level) heritability for rare variants"
has_children: false
has_toc: true
---

# RareEffect

RareEffect is a novel method to estimate the variant-level effect size (beta) and region-level (gene-level) heritability for rare variants.
RareEffect is integrated into SAIGE and is available in SAIGE version 1.3.x or higher.

## Procedure

1. First, run SAIGE step 1. The output file from SAIGE step 1 (`.rda` file) will be used as the input for RareEffect.
2. The inputs for RareEffect are as follows:

  * Genotype file (plink bed/bim/fam)
  * Group file (same format as the group file for SAIGE-GENE+)
  * SAIGE step 1 output rda file

3. Execute the main function of RareEffect. An example command is provided below.

```
Rscript run_RareEffect.R \
    --rdaFile=$PATH_TO_RDA_FILE \
    --chrom=$CHR \
    --geneName=$GENE_NAME \
    --groupFile=$PATH_TO_GROUP_FILE \
    --traitType=binary \
    --bedFile=$PATH_TO_BED_FILE \
    --bimFile=$PATH_TO_BIM_FILE \
    --famFile=$PATH_TO_FAM_FILE \
    --macThreshold=10 \
    --collapseLoF=FALSE \
    --outputPrefix=$PATH_TO_OUTPUT_FILE
```

  * rdaFile: SAIGE step 1 output file
  * chrom: chromosome number (eg. 11)
  * geneName: name of gene to analyze (eg. APOC3)
  * groupFile: group file containing variant list and functional annotations.
  * traitType: type of trait to be analyzed, `binary` or `quantitative`
  * bedFile, bimFile, famFile: genotype file
  * macThreshold: minor allele count threshold for ultra-rare variant collapsing
  * collapseLoF: if true, RareEffect collapses all LoF variants into one super-variant like Burden test (regardless of their MAC)
  * outputPrefix: path to output

4. RareEffect generates two output files: variant-level effect size and region-level heritability.

  * Variant-level effect size

```
variant          effect              PEV
11:116830637:C:T 0.304491201231766   0.00727297925199994
11:116830638:G:A 0.325660176157425   0.000662034527515423
11:116830638:G:T 0.21230324192781    0.0645359637154959
11:116830897:G:T 0.310004879463224   0.060623563613811
lof_UR           0.261071923418984   0.0869925579756295
11:116830533:G:A -0.0631819446554343 0.0235133013294374
11:116830620:C:T -0.0130476721146154 0.0539691054813582
11:116830787:G:A 0.0448881905903484  0.0317167120002997
11:116830833:C:A 0.310894962948392   0.0570479313033626
```

  * Region-level heritability

```
LoF                 mis                 syn                  all
0.00326766343863732 0.00028977664861778 3.21446473889367e-05 0.00358958473464404
```

5. Using the variant-level effect size from RareEffect, you can calculate the polygenic score in another cohort.

```
Rscript calculate_RareEffect_PRS.R \
    --effectFile $PATH_TO_EFFECT_SIZE_FILE \
    --bedFile $PATH_TO_BED_FILE \
    --bimFile $PATH_TO_BIM_FILE \
    --famFile $PATH_TO_FAM_FILE \
    --groupFile $PATH_TO_GROUP_FILE \
    --geneName $GENE_NAME \
    --variantListFile $PATH_TO_VARIANT_LIST_FILE \
    --outputPrefix $PATH_TO_OUTPUT_FILE
```

  * effectFile: variant-level effect size output from RareEffect main function
  * variantListFile: list of common and rare, but non-ultra-rare variants. These variants will not be used for ultra-rare variant scoring.

  * Individual RareEffect PRS
```
IID     PRS
1000019 0
1000022 0
1000035 0
1000046 0
1000054 0
1000063 0
1000078 0.48012911
1000081 0
1000090 0
1000105 0
```

## Example output

We estimated variant-level effect size and gene-level heritability for genome-wide significant genes (or top 10 genes) using 393,247 White British individuals in UKB WES data.
The example output files can be downloaded at:
  * Variant-level effect size: https://storage.googleapis.com/leelabsg/RareEffect/RareEffect_effect_size.zip
  * Gene-level signed heritability: https://storage.googleapis.com/leelabsg/RareEffect/RareEffect_h2.zip
