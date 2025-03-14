Deconvolution Methods overview

This repository provides all scripts written to deconvolute bulk RNA-seq datasets using single-cell RNA-seq datasets, through the use of the ADAPTS package (Danziger et al., 2019).

The pipeline walkthrough is detailed in deconvolution/code/README.md.



Publicly available data used were originally acquired from:
The bulk dataset was derived from our prior analysis (https://github.com/Leandromvelez/myokine-signaling)

GTEx V8: https://gtexportal.org/home/datasets (the raw data was filtered where individuals were required to show counts > 0 in 1.2e6 gene_tissue combinations across all data. Given that our goal was to look across tissues at enrichments, this was done to limit spurious influence of genes only expressed in specific tissues. Post-filtering consists of 310 individuals and 1.8e7 gene_tissue combinations). This results in 52% NA values in the final matrix used.

Uniprot annotations for secreted proteins in humans: https://www.uniprot.org/uniprot/?query=locations%3A%28location%3A%22Secreted+%5BSL-0243%5D%22+type%3Acomponent%29+AND+organism%3A%22Homo+sapiens+%28Human%29+%5B9606%5D%22&sort=score

Tabula Sapiens for annotated single-cell RNA sequencing data:
The Tabula Sapiens Consortium, Science 376, eabl4896 (2022). https://tabula-sapiens.sf.czbiohub.org/
