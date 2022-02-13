---
layout: default
title: Set-based test 
nav_order: 5
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
---


SAIGE-GENE (now called SAIGE-GENE+) takes two steps to perform set-based association tests 
- BURDEN, SKAT, and SKAT-O
- Ultra-rare variants with MAC <= 10 are collpased as a pseudo marker in the tests
- Multiple function annotations, e.g. Lof only, Lof+Missense, Lof+Missense+Synonymous
- Multiple max MAF cutoffs, e.g. 0.0001, 0.001, and 0.01
- For each gene, multiple p-values corresponding to different annotations and max MAF cutoffs are combined based on Cauchy ditribution

<img src="{{ site.baseurl }}/assets/img/SAIGE-GENE-pie.png" width="300">
