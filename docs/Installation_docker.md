---
layout: default
title: Docker
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Installation
---


### Run SAIGE using a docker image

Thanks to Juha Karjalainen for sharing the Dockerfile.

* Dockerfile can be found in the SAIGE folder: ./docker/Dockerfile

* The docker image can be pulled

    ```
    docker pull wzhou88/saige:1.0.2
    ```

Functions can be called

    ```
    step1_fitNULLGLMM.R --help
    step2_SPAtests.R --help
    createSparseGRM.R --help
    ```
