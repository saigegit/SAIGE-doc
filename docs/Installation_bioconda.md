---
layout: default
title: Bioconda
nav_order: 4
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Installation
---

###  Install SAIGE from bioconda

![r-saige](https://anaconda.org/bioconda/r-saige/badges/version.svg)
![latest_update](https://anaconda.org/bioconda/r-saige/badges/latest_release_date.svg)

Recent versions of SAIGE are distributed through bioconda with exclusively linux support. Mac OS support may be possible through other installation methods.

To install saige from bioconda simply create environment with latest version of R and saige:
```
conda create -n saige -c conda-forge -c bioconda r-saige
conda activate saige
```

The installation will place the scripts in `extdata/` into your conda environment's PATH.

More info on [r-saige conda package](https://anaconda.org/bioconda/r-saige) and available versions can be found in the [issue #272](https://github.com/weizhouUMICH/SAIGE/issues/272).

