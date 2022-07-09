---
layout: default
title: Installation
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: false
---

How to install and run SAIGE and SAIGE-GENE


## Install SAIGE/SAIGE-GENE (current version 1.1.2 (updated on June 23 2022))

### List of dependencies:

* R >= 3.6.1, gcc >= 5.4.0, cmake 3.14.1, [cget](https://cget.readthedocs.io/en/latest/src/intro.html#installing-cget), [savvy](https://github.com/statgen/savvy)
* R packages: Rcpp (>= 1.0.7), RcppArmadillo, RcppParallel, data.table, SPAtest (== 3.1.2),
        RcppEigen, Matrix, methods, BH, optparse, SKAT, qlcMatrix, RhpcBLASctl, roxygen2, rversions, devtools, dplyr, dbplyr
-  Rscript ./extdata/install_packages.R can be used to install the R packages

* Two libraries for reading VCF files. Will be automatically installed during the SAIGE installation
-  [cget](https://cget.readthedocs.io/en/latest/src/intro.html#installing-cget), [savvy](https://github.com/statgen/savvy)

### Logs:


## v1.1.3 (July 9, 2022)

Bugs fixed:

Fixed the bug for computating imputation scores in bgen files. 

Improvements:

Added an option --isLowMemLOCO=TRUE to output the null model results by chromosome in different files in Step 1 when LOCO=TRUE. This can reduce memory usage in Step 1 and later in Step 2 when LOCO is TRUE. 

Implemented the efficient resampling in C++ and added an option --max_MAC_for_ER to specify the maximum MAC to conduct ER. p-values of genetic variants with MAC <= max_MAC_for_ER will be calculated via efficient resampling. The default value is 4. 

Implemented functions to output "LD matrices" for variant sets. This feature is under evaluation and will be used for set-based meta-analyses.  


## v1.1.2 (June 23, 2022)
Improvements:

Rewritten codes to obtain sample subset who are in the sparse GRM, plink file and phenotype file. 

Support the sparse matrix with 1s

## v1.1.1 (June 8, 2022)

Bugs fixed:

fixed the redudant output for single-variant conditional analysis for quantitative traits.


## v1.0.9 (May 17, 2022)

Bugs fixed:

Fixed an output missing when the last group in the group file is empty

Improvements:

Get a more accurate variance estimation when the sparse GRM is used for fitting the null model and used for Step 2


## v1.0.7 (May 8, 2022)

Bugs fixed:

Fixed the error when reading in per-marker weights from the gorup file

Fixed the issue when the CRHOM column contains string, e.g. "chr1", in the VCF file and the matching with marker IDs in the group file and --chrom. 


## v1.0.6 (May 6, 2022)

Bugs fixed: 

for vcf input, the largest chromosome position is 250000000. In the previouse version, it is 2000000000, which leads to missing Chr 1 and Chr 2 markers 

Fixed the issue to read the group file separated by tab. Thanks to yonas-yuh!

When the markers are not ordered by chromosome position in the group file, reading genotypes from VCF files have issues. It has been fixed and now the markers do not need to be ordered by chromosome position within each set in the group file.  

Improvements: 

Added an option --is_fastTest for single and set-based tests to further improve the computation efficiency 

Allow for different prefix (--bimFile, --bedFile, --famFile) for plink files in Step 0 and Step 1

Further improved the randomness of marker selection for variance ratio estimation


## v1.0.5 (April 1, 2022)
Bugs fixed:

Removed the two extra header columns from the header line in the single assoc results when conducting the set-based tests

Improvements:

Updated and cleaned the dockerfile (Ubuntu 20.04 with system R, OpenBLAS) and docker readme (Thanks to Alex Petty!) 


## v1.0.4 (March 30, 2022)
Bugs fixed:

Fixed the issue when SKAT-O is performed with no weights applied to markers and one or more groups have no markers. error message "Error in if (abs(q - m1)/sqrt(var1) < Cutoff) { :"


## v1.0.3 (March 30, 2022)
Bugs fixed:

Fixed the annolist issue.

Improvements:

Codes added to avoid using markers selected for variance ratio estimation in the GRM construction.


## v1.0.2 (March 25, 2022):
Bugs fixed:

if LOCO = FALSE, --chrom is 
- not required for set-based tests for VCF, BGEN or PLINK input 
- not required for single-variant assoc tests for BGEN or PLINK input
- required for single-variant assoc tests for VCF input

if LOCO = TRUE, --chrom is always required 

Improvements:

Sample file for bgen input now can either contain one column without header or be in the format of the bgenix (UKBB provided)  

## v1.0.1 (March 18, 2022): 
Bugs fixed:
--AlleleOrder for bgen input
Header of the single-variant assoc test output

Improvements:
change PI to M_PI in CCT.cpp
add flag in Makevar for installation on Centos 8

## v1.0.0 (March 15, 2022): First stable version

## v0.99.3 (March 12, 2022): all marker IDs used with --condition and in the group file for set-based tests need to use the format chr:pos:ref:alt

