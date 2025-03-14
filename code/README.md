#!usr/bin/bash

srun -c 8 -A mseldin_lab --pty --x11 bash -i
conda activate ee282
cd /pub/cassandv/myrepos/deconvolution

# Download desired TS RNA-seq files.
cd data/raw
sh TSdownloads.sh
unzip 'sc*.h5ad.zip'
for file in *.h5ad; do h5adtoh5seurat "$file"; done

# Extract cell annotations for future examination.
cd ../..
for tissue in data/raw/TS_*.h5seurat; do h5seuratCellAnnots "$tissue" 'free'; done

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



##### The commands below analyze one tissue at a time. See code/src/TissueComparisons.txt for completed single-cell Tabula Sapiens dataset(s) matchup to bulk GTEx dataset(s).
##### Example here is for 'Heart'.

# Load bulk dataset and filter for tissue of interest.
bulkProcessing data/raw/GTEx_subfiltered.RData 'Heart' data/processed/bulkDF_heart.csv
#column -s "," -t data/processed/bulkDF_heart.csv | less -S

# Run each tissue through ADAPTS and save figures.
mkdir output/figures/deconvolution_heart
cd output/figures/deconvolution_heart
ADAPTSmethods ../../../data/processed/scDF_heart.csv ../../../data/processed/bulkDF_heart.csv 'heart' ../../data/processed/binProp_heart.csv

# Choose deconvolution method and subset binProp.


##### Extra Functions

# Check single-cell cell type markers through Enrichr databases.
mkdir output/figures/EnrichR_heart
cd output/figures/EnrichR_heart
EnrichRCheck scDF.csv #needs troubleshooting to work in Unix, works well in R.

# Correlate cell types locally with GOI. You can change the deconvolution method.
cd output/figures
localGOI ../../data/processed/binProp_heart.csv ../../data/processed/bulkDF_heart.csv 'CRLF2' 'DeconRNASeq' 'heart'

# Correlate cell types with functional pathways in a tissue of interest. You can change the deconvolution method.
cd output/figures/crossTissueGO
crossTissueGO ../../../data/raw/go_terms.RData ../../../data/processed/binProp_heart.csv ../../../data/processed/bulkDF_heart.csv 'inflammatory' 'DeconRNASeq' 'heart'
