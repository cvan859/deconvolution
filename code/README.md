#!usr/bin/bash

srun -c 8 -A mseldin_lab --pty --x11 bash -i
conda activate ee282
cd /pub/cassandv/myrepos/deconvolution
cd data/raw

# Download desired TS RNA-seq files.
sh TSdownloads.sh
unzip 'sc*.h5ad.zip'
for file in *.h5ad; do h5adtoh5seurat "$file"; done

# Extract cell annotations for future examination.
h5seuratCellAnnots TS_*.h5seurat

# Collect normalized counts matrices -> cell type expression vectors
scProcessing TS_*.h5seurat 'free'

# View list of bulk tissues available
cd ../..
GTExTissuesCheck data/raw/GTEx_subfiltered.RData output/tables/GTExTissues.txt
less output/tables/GTExTissues.txt

# Make a folder and list of GOPathway terms (see Extra Functions below).
mkdir output/figures/crossTissueGO
cd ../../..
GOPathwaysCheck data/raw/go_terms.RData output/tables/GOPathways.txt



##### As-is, the commands below analyze one tissue at a time. I will update this section when I have it automated.
##### See code/src/TissueComparisons.txt for currently planned/completed single-cell Tabula Sapiens dataset(s) matchup to bulk GTEx dataset(s).
##### Example here is for 'Heart'.

# Load bulk dataset and filter for tissue of interest.
bulkProcessing data/raw/GTEx_subfiltered.RData 'Heart' data/processed/bulkDF_heart.csv
#column -s "," -t data/processed/bulkDF_heart.csv | less -S

# Run each tissue through ADAPTS and save figures.
mkdir output/figures/deconvolution_heart
cd output/figures/deconvolution_heart
ADAPTSmethods ../../../data/processed/scDF_heart ../../../data/processed/bulkDF_heart 'heart' ../../data/processed/BinnedProportions



##### Extra Functions

# Check single-cell cell type markers through Enrichr databases.
mkdir output/figures/EnrichR_heart
cd output/figures/EnrichR_heart
EnrichRCheck scDF.csv #needs troubleshooting to work in Unix, R ok.

# Correlate cell types locally with GOI.
cd output/figures
localGOI ../../data/processed/binnedProportions_heart.csv ../../data/processed/bulkDF_heart.csv 'CRLF2' 'DeconRNASeq' 'heart'

# Correlate cell types with functional pathways in a tissue of interest.
cd output/figures/crossTissueGO
crossTissueGO ../../../data/raw/go_terms.RData ../../../data/processed/binnedProportions_heart.csv ../../../data/processed/bulkDF_heart.csv 'inflammatory' 'DeconRNASeq' 'heart'

