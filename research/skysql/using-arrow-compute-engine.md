# Overview

**Work in Progress.** This section is a work in progress and is incomplete.

This is a post that describes how I use the arrow compute engine.


# Running Example

To make the time writing this post extra useful, I will be using a running example related to my
research: differential gene expression between 2 datasets. Generally, the datasets are gene
expression counts and the differential expression is calculated by quantifying how much the
expression of each gene changes between the 2 datasets. Even more generally, differential gene
expression can include more than 2 datasets.

In this post, we'll specifically use T-statistic between 2 metaclusters as our example query. One
metacluster will correspond to a particular EBI single-cell dataset, and the other metacluster will
correspond to 2 EBI single-cell datasets.


# Learning

This section describes how I am learning (have learned) to use the arrow compute engine. I am not
sure yet if I will come back and modify this section to be more cohesive, or if I will simply write
this as I go and leave it as is. In either case, this section should be useful for anyone trying to
understand the components of the arrow compute engine in the context of a specific example.
