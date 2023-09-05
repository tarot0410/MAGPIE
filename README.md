# MAGPIE

MAGPIE is a statistical and computational method for identifying probabilistically both the specific genes within a pathway and the individual mutations within such genes that are truly the drivers. It builds on a likelihood approach leveraging the mutual exclusivity pattern within an oncogenic pathway.

A tutorial of MAGPIE on Google Colab: <a 
				       href="https://colab.research.google.com/drive/1ozZQ5wAZfWK3i8cazJ003nfelLR7EMzw?usp=sharing">
  	<img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
	</a>
	

# Installation

### From source

Download a local copy of MAGPIE and install from the directory:

	git clone https://github.com/tarot0410/MAGPIE.git
	cd MAGPIE
	pip install .

# Core function (gene-level analysis)

MAGPIE(x_mat, tmb_vec, return_pval = True, nPermut=100, lr = .1, maxIter=200, earlystop=1e-8)

### Input
Required
- **x_mat**: Gene alteration matrix (a torch tensor of size N*M, N: # of tumors; M: # of genes)
- **tmb_vec**: Tumor mutational burden score for each tumor (a torch tensor of size N, can be binary or continuous)

Optional
- **return_pval**: If true (default), the algorithm will compute emperical p-value through parametric bootstrap (could take a few minutes)
- **nPermut**: The number of bootstrap samples used to compute p-value (default 100).
- **lr**: Learning rate for L-BFGS optimization algorithm (default 0.1)
- **maxIter**: Maximum iterations allowed (default 200)
- **earlystop**: Stop iterations if the relative change in loss is minimal (default 1e-8)

### Output
- **driver_freq**: A torch tensor of size (M+1), where the first value refers to the proportion of tumors not estimated to have a driver mutation, and the rest values refer to proportion of tumors estimated to have a driver mutation in each gene.
- **beta0_est**: A torch tensor of size M, where each value refers to the estimated baseline log odds of the passenger mutation rate for the each gene
- **beta1_est**: A scalor referring to the estimated common association of tumor mutational burden with passenger mutation rate (in log scale)
- **logPostProb**: A torch tensor of size N*(M+1), where each entry refers to the posterior probability of having a driver mutation in each gene (or not having any driver mutation). The first column refers to the posterior probability of not having any driver mutation. Subsequent columns refer to genes in the data.
- **logLik_iter**: A torch tensor of size *maxIter*, recording the loglikelihood value at each iteration. If the algorithm stops early before reaching maxIter, the rest entries are filled with 0.
- **logLik_final**: A scalor summarizing the final loglikelihood value at convergence.
pval: A scalor referring to the emperical p-value.

# Variant-level analysis
The average posterior probability that variant *l* is a driver can be computed as follows:

$$ P_{j(l)}= \frac{1}{N_{j(l)}}\sum_{i=1}^N x_{ij(l)} w_{ij} $$ where $$ N_{j(l)}=\sum_{i=1}^N x_{ij(l)} $$

# Paper
Wang X, Kostrzewa C, Reiner A, Shen R, Colin B. Adaptation of a Mutual Exclusivity Framework to Identify Driver Mutations within Biological Pathways. [*Submitted*. 2023+]
