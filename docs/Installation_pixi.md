---
layout: default
title: pixi
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Installation
---

###  Install SAIGE using pixi 

Note: These steps are similar to what is used by [Dockerfile](https://github.com/saigegit/SAIGE/blob/main/docker/Dockerfile).

0. Download the SAIGE package from github

```
src_branch=main
repo_src_url=https://github.com/saigegit/SAIGE
git clone -b $src_branch $repo_src_url

```

1. Install pixi and the R package lintools
```
curl -fsSL https://pixi.sh/install.sh | sh && \
    . ~/.$(basename $SHELL)rc && \
    cd SAIGE && \
    pixi install && \
    rm -rf ~/.cache && \
    pixi run Rscript -e 'install.packages("lintools", repos="https://cloud.r-project.org")'
```

2. Download and compile plink for reading pgen files in Step 2 association tests
```
curl -L https://github.com/chrchang/plink-ng/archive/refs/tags/v2.0.0-a.6.16.tar.gz | tar -zx && \
    mv plink-ng-2.0.0-a.6.16 plink-ng && \
    pixi run x86_64-conda-linux-gnu-cc -std=c++14 -fPIC -O3 -I.pixi/envs/default/include -L.pixi/envs/default/lib -o libplink2_includes.a plink-ng/2.0/include/*.cc -shared -lz -lzstd -lpthread -lm -ldeflate && \
    mv libplink2_includes.a .pixi/envs/default/lib
```
On osx-arm64 hardware, the clang compiler is used and the previous step is slightly different:
```
curl -L https://github.com/chrchang/plink-ng/archive/refs/tags/v2.0.0-a.6.16.tar.gz | tar -zx && \
    mv plink-ng-2.0.0-a.6.16 plink-ng && \
    pixi run cc -shared -std=c++14 -fPIC -O3 -I.pixi/envs/default/include -L.pixi/envs/default/lib -o libplink2_includes.so plink-ng/2.0/include/*.cc -lz -lzstd -lpthread -lm -ldeflate -lc++ && \
    mv libplink2_includes.so .pixi/envs/default/lib
```

3. Install the SAIGE package

```
pixi run R CMD INSTALL .
```

SAIGE can now be used in the pixi environment (`pixi run R`):


```
    library(SAIGE)
```
