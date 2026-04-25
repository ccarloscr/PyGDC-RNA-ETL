# gdc-rnaseq-cohort-builder
GDC API pipeline to build, filter and export RNA-seq cohorts with somatic mutation and clinical metadata — ready for downstream ML or differential expression analysis.
Compatible with any project hosted on the [GDC Data Portal](https://portal.gdc.cancer.gov), not limited to TCGA.

## Pipeline overview
01_build_cohort.ipynb
- Query GDC API, filter cohort, annotate mutation & clinical labels, export metadata
- Run locally
- Output: cohort_metadata.csv

02_download_counts.py
- Download STAR count files listed in cohort_metadata.csv
- Run on HPC / Cloud
- Output: raw .tsv count files

03_build_matrix.py
- Merge individual count files into a single sample × gene matrix
- Run on HPC / Cloud
- Output: count_matrix.tsv

## Outputs
- cohort_metadata.csv: one row per sample with case IDs, clinical variables, and mutation labels
- count_matrix.tsv: raw STAR counts matrix (samples × genes)

## Requirements
- Python 3.10
- Anaconda or Miniconda

## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/youruser/gdc-rnaseq-cohort-builder.git
   cd gdc-rnaseq-cohort-builder
   ```
2. Create and activate the conda environment:
   ```bash
   conda create -n gdc-cohort python=3.10
   conda activate gdc-cohort
   ```
3. Install dependencies:
   ```bash
   pip install requests pandas jupyterlab
   ```
## Usage
Step 1 — Build cohort (local)
Launch Jupyter Lab and open 01_build_cohort.ipynb:
   ```bash
   jupyter lab
   ```
Configure your cohort in Section 1: project ID, gene mutations, sample type, and any clinical filters. Run all cells. This produces cohort_metadata.csv.

Step 2 — Download count files (HPC / Cloud)
Transfer the metadata file to your remote environment and run the download script:
   ```bash
   # Transfer metadata
   scp cohort_metadata.csv user@hpc:/path/to/project/

   # Set up environment on remote (first time only)
   conda create -n gdc-cohort python=3.10
   conda activate gdc-cohort
   pip install requests pandas

   # Run download
   python 02_download_counts.py
   ```

Step 3 — Build count matrix (HPC / Cloud)
Once all files are downloaded, build the merged count matrix:
   ```bash
   python 03_build_matrix.py
   ```
This produces count_matrix.tsv (samples × genes) alongside the annotated cohort_metadata.csv.

## Configuration reference
All parameters for 01_build_cohort.ipynb are set in Section 1:
[TABLE HERE]

## Notes
- The GDC API only exposes open-access data. Controlled-access files (e.g. WGS BAMs) require dbGaP authorization and are not supported by this pipeline.
- cohort_metadata.csv may contain patient-level metadata. Do not commit it to version control — it is listed in .gitignore.
- The GDC API returns a maximum of 10,000 records per request. A warning is printed if your query exceeds this limit.
