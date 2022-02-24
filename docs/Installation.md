---
layout: default
title: Installation
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: false
---

How to install and run SAIGE and SAIGE-GENE


## Install SAIGE/SAIGE-GENE (current version 0.96.1 (updated on Feb 21 2022))

### List of dependencies:

* R-3.6.1, gcc >= 5.4.0, cmake 3.14.1, [cget](https://cget.readthedocs.io/en/latest/src/intro.html#installing-cget), [savvy](https://github.com/statgen/savvy)
* R packages: Rcpp, RcppArmadillo, RcppParallel, data.table, SPAtest (== 3.1.2),
        RcppEigen, Matrix, methods, BH, optparse, SKAT, qlcMatrix, RhpcBLASctl

"R.utils", "Rcpp", "RcppParallel", "RcppArmadillo", "data.table", "RcppEigen", "Matrix", "methods", "BH", "optparse", "SPAtest", "SKAT"

* /extdata/install_packages.R can be used to install the R packages

###  Install SAIGE from conda (Not current version)

#### Warning: please do not use this bioconda version for bgen input. We are working on the issue.
![r-saige](https://anaconda.org/bioconda/r-saige/badges/version.svg)
![latest_update](https://anaconda.org/bioconda/r-saige/badges/latest_release_date.svg)

To install saige from conda simply create environment with latest version of R and saige:
```
conda create -n saige -c conda-forge -c bioconda "r-base>=4.0" r-saige
conda activate saige
```

More info on [r-saige conda package](https://anaconda.org/bioconda/r-saige) and available versions can be found in the [issue #272](https://github.com/weizhouUMICH/SAIGE/issues/272).


###  Install SAIGE using the conda environment

0. Download and install [miniconda](https://docs.conda.io/en/latest/miniconda.html) 

1. Create a conda environment using
    
     * conda environment file is in the SAIGE folder: ./conda_env/environment-RSAIGE.yml

     * After downloading environment-RSAIGE.yml, run following command
     ```
       conda env create -f environment-RSAIGE.yml
     ```

2. Activate the conda environment RSAIGE

     ```
       conda activate RSAIGE
       FLAGPATH=`which python | sed 's|/bin/python$||'`
       export LDFLAGS="-L${FLAGPATH}/lib"
       export CPPFLAGS="-I${FLAGPATH}/include"
     ```
Please make sure to set up the LDFLAGS and CPPFLAGS using export (the last two command lines), so libraries can be linked correctly when the SAIGE source code is compiled. Note: [Here](https://github.com/weizhouUMICH/SAIGE/blob/master/conda_env/createCondaEnvSAIGE_steps.txt) are the steps to create the conda environment file


3. Install SAIGE from the source code.

     ```
       src_branch=main
       repo_src_url=https://github.com/saigegit/SAIGE
       git clone --depth 1 -b $src_branch $repo_src_url
       Rscript ./SAIGE/extdata/install_packages.R
       R CMD INSTALL --library=path_to_final_SAIGE_library SAIGE
     ```

     When call SAIGE in R, set lib.loc=path_to_final_SAIGE_library

     ```
       library(SAIGE, lib.loc=path_to_final_SAIGE_library)
     ```

### Run SAIGE using a docker image

Thanks to Juha Karjalainen for sharing the Dockerfile.

* Dockerfile can be found in the SAIGE folder: ./docker/Dockerfile 

* The docker image can be pulled

    ```
    docker pull wzhou88/saige:0.96.1
    ```

Functions can be called

    ```
    step1_fitNULLGLMM.R --help
    step2_SPAtests.R --help
    createSparseGRM.R --help
    ```
