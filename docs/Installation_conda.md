---
layout: default
title: Conda
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Installation
---

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
Please make sure to set up the LDFLAGS and CPPFLAGS using export (the last two command lines), so libraries can be linked correctly when the SAIGE source code is compiled.

Note: [Here](https://github.com/saigegit/SAIGE/blob/main/conda_env/createCondaEnvSAIGE_steps.txt) are the steps to create the conda environment file


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
