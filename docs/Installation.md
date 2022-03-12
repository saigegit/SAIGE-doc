---
layout: default
title: Installation
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: false
---

How to install and run SAIGE and SAIGE-GENE


## Install SAIGE/SAIGE-GENE (current version 0.99.3 (updated on March 12 2022))

### List of dependencies:

* R >= 3.6.1, gcc >= 5.4.0, cmake 3.14.1, [cget](https://cget.readthedocs.io/en/latest/src/intro.html#installing-cget), [savvy](https://github.com/statgen/savvy)
* R packages: Rcpp (>= 1.0.7), RcppArmadillo, RcppParallel, data.table, SPAtest (== 3.1.2),
        RcppEigen, Matrix, methods, BH, optparse, SKAT, qlcMatrix, RhpcBLASctl, roxygen2, rversions, devtools, dplyr, dbplyr
-  Rscript ./extdata/install_packages.R can be used to install the R packages

* Two libraries for reading VCF files. Will be automatically installed during the SAIGE installation
-  [cget](https://cget.readthedocs.io/en/latest/src/intro.html#installing-cget), [savvy](https://github.com/statgen/savvy)

### Logs:

v0.99.3 (March 12, 2022): all marker IDs used with --condition and in the group file for set-based tests need to use the format chr:pos:ref:alt
