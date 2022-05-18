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
    docker pull wzhou88/saige:1.0.9
    ```

Functions can be called

```
step1_fitNULLGLMM.R --help
step2_SPAtests.R --help
createSparseGRM.R --help
```

### If docker version of SAIGE is run on a local system

```
docker run wzhou88/saige:1.0.6 step1_fitNULLGLMM.R --help
docker run wzhou88/saige:1.0.6 step2_SPAtests.R --help
docker run wzhou88/saige:1.0.6 createSparseGRM.R --help
```

NOTE: ```-v``` option is also important for docker run to ```Bind mount a volume```. Check [link](https://docs.docker.com/engine/reference/commandline/run/#:~:text=%2D%2Dvolume%20%2C%20%2Dv,mount%20a%20volume) for more details.
