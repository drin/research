# Overview

The main idea of this project is to use machine learning to cluster single cell
gene expression into cell types. There are two objectives this could satisfy:

* can we determine some notion of similarity between existing cell types (cluster centroids)
* can we apply **P**robabilistic **S**oft **L**ogic (PSL) to this problem.

I would want to essentially have a clustering implementation that uses PSL for the
use case described in the PAGA paper, and compare speed performance and prediction performance.

# Related work

* Use of PSL for prediction of drug-drug interactions: [A Probabilistic Approach for Drug-Drug Interaction Prediction][paper-psl-drugs]
* A recent approach to cell type and trajectory analysis: [PAGA: clustering with trajectory inference][paper-paga]
    
<!-- resources -->
[paper-psl-drugs]: https://linqs.soe.ucsc.edu/sites/default/files/papers/sridhar-bioinformatics_2016.pdf
[paper-paga]:      https://genomebiology.biomedcentral.com/articles/10.1186/s13059-019-1663-x
