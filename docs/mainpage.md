---
layout: default
title: Home
nav_order: 1
description: "Documentation for SAIGE and SAIGE-GENE+."
permalink: /
---

SAIGE is an R package developed with Rcpp for genome-wide association tests in large-scale data sets and biobanks. The method

- accounts for sample relatedness based on the generalized mixed models
- allows for model fitting with either full or sparse genetic relationship matrix (GRM)
- works for quantitative and binary traits
- handles case-control imbalance of binary traits
- computationally efficient for large data sets
- performs single-variant association tests
- provides effect size estimation through Firth's Bias-Reduced Logistic Regression
- performs conditional association analysis

SAIGE-GENE (now known as SAIGE-GENE+) are new method extension in the R package for testing rare variant in set-based tests.
- performs BURDEN, SKAT, and SKAT-O tests
- allows for tests on multiple minor allele frequencies cutoffs and functional annotations
- allows for specifying weights for markers in the set-based tests
- performs conditional analysis to identify associations independent from nearly GWAS signals


The package takes genotype file input in the following formats
- PLINK (bed, bim, fam), BGEN, VCF, BCF, SAV

## Citation

### SAIGE:
- Zhou W, Nielsen JB, Fritsche LG, Dey R, Gabrielsen ME, Wolford BN, LeFaive J, VandeHaar P, Gagliano SA, Gifford A, Bastarache LA, Wei WQ, Denny JC, Lin M, Hveem K, Kang HM, Abecasis GR, Willer CJ, Lee S. Efficiently controlling for case-control imbalance and sample relatedness in large-scale genetic association studies. Nat Genet. 2018 Sep;50(9):1335-1341. doi: 10.1038/s41588-018-0184-y. Epub 2018 Aug 13. PMID: 30104761; PMCID: PMC6119127.

### SAIGE-GENE:
- Zhou W\*, Zhao Z\*, Nielsen JB, Fritsche LG, LeFaive J, Gagliano Taliun SA, Bi W, Gabrielsen ME, Daly MJ, Neale BM, Hveem K, Abecasis GR, Willer CJ, Lee S. Scalable generalized linear mixed model for region-based association tests in large biobanks and cohorts. Nat Genet. 2020 Jun;52(6):634-639. doi: 10.1038/s41588-020-0621-6. Epub 2020 May 18. PMID: 32424355; PMCID: PMC7871731.

### SAIGE-GENE+:
- Wei Zhou\*, Wenjian Bi\*, Zhangchen Zhao\*, Kushal K. Dey, Karthik A. Jagadeesh, Konrad J. Karczewski, Mark J. Daly, Benjamin M. Neale, Seunggeun Lee. Set-based rare variant association tests for biobank scale sequencing data sets. medRxiv 2021.07.12.21260400; doi: https://doi.org/10.1101/2021.07.12.21260400 

## License
SAIGE is distributed under an GPL license.


## Contact
If you have any questions about SAIGE please contact
saige.genetics@gmail.com
