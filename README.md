# gdc-rnaseq-cohort-builder
![WIP](https://img.shields.io/badge/status-under%20development-orange)
>**Note**: This repository is currently under development.

GDC API pipeline to build, filter and export RNA-seq cohorts with somatic mutation and clinical metadata ready for downstream machine learning or differential expression analysis.

Compatible with any project hosted on the [GDC Data Portal](https://portal.gdc.cancer.gov), not limited to TCGA.

## Roadmap
- [x] Script to query GDC API, filter cohort and export metadata.
- [ ] Script to download STAR count files from [cohort_metadata.csv](/cohort_metadata.csv).
- [ ] Script to merge the downloaded count files into a single matrix.
- [ ] 
---

## Pipeline Overview
### [01_build_cohort.ipynb](/01_build_cohort.ipynb) ![Status: Active](https://img.shields.io/badge/status-active-success)
- **Function**: Query GDC API, filter cohort, annotate mutation & clinical labels, export metadata.
- **Environment**: Local.
- **Output**: [cohort_metadata.csv](/cohort_metadata.csv).

### 02_download_counts.py ![WIP](https://img.shields.io/badge/status-work%20in%20progress-orange)
- **Function**: Download STAR count files listed in [cohort_metadata.csv](/cohort_metadata.csv).
- **Environment**: HPC / Cloud.
- **Output**: raw .tsv count files.

### 03_build_matrix.py ![WIP](https://img.shields.io/badge/status-work%20in%20progress-orange)
- **Function**: Merge individual count files into a single sample × gene matrix.
- **Environment**: HPC / Cloud.
- **Output**: count_matrix.tsv.

## Requirements
- Python 3.10
- Anaconda or Miniconda

## Installation
#### 1. Clone the repository:
   ```bash
   git clone https://github.com/youruser/gdc-rnaseq-cohort-builder.git
   cd gdc-rnaseq-cohort-builder
   ```
#### 2. Set up the environment
   ```bash
   conda create -n gdc-cohort python=3.10 -y
   conda activate gdc-cohort
   pip install requests pandas jupyterlab
   ```

## Usage Guide
#### Step 1 — Build Cohort (local)
Launch Jupyter Lab and open [01_build_cohort.ipynb](/01_build_cohort.ipynb):

Configure your cohort in Section 1 (project ID, gene mutations, sample type, and any clinical filters) and run all cells.

>**Result**: Generates [cohort_metadata.csv](/cohort_metadata.csv).

#### Step 2 — Download Count Files (HPC / Cloud)
Transfer the [cohort_metadata.csv](/cohort_metadata.csv) file to your remote environment and run the [02_download_counts.py](/02_download_counts.py) script:
   ```bash
   # Transfer metadata
   scp cohort_metadata.csv user@hpc:/path/to/project/

   # Set up environment on remote (if not created before)
   conda create -n gdc-cohort python=3.10
   conda activate gdc-cohort
   pip install requests pandas

   # Run download
   python 02_download_counts.py
   ```

#### Step 3 — Build Matrix (HPC / Cloud)
Once downloads are complete, merge the files:
   ```bash
   python 03_build_matrix.py
   ```
>**Result**: Generates [count_matrix.tsv](/count_matrix.tsv).

## Configuration Reference
All parameters for the cohort construction are located in the first cell of [01_build_cohort.ipynb](/01_build_cohort.ipynb):
| GDC API Field | Description | Typical Values |
| :--- | :--- | :--- |
| `cases.samples.sample_type` | Sample type | Primary Tumor, Solid Tissue Normal, Recurrent Tumor |
| `gene_mutation` (via `/ssm_occurrences`) | Somatic mutation in selected genes | - |
| `cases.tobacco_smoking_status` | Smoking Status | <details><summary>*View all 6 options*</summary> <ul><li>Lifelong Non-Smoker</li><li>Current Smoker</li><li>Current Reformed Smoker for > 15 yrs</li><li>Current Reformed Smoker for < or = 15 yrs</li><li>Current Reformed Smoker, Duration Not Specified</li><li>Not Reported</li></details>|
| `cases.diagnoses.ajcc_pathologic_stage` | Pathologic stage | Stage I, II, III, IV |
| `cases.demographic.vital_status` | Vital status | Alive, Dead |
| `cases.demographic.gender` | Sex at birth | male, female |
| `cases.diagnoses.age_at_diagnosis` | Age at diagnosis (in days) | *integer* |


## Notes
- **Access**: This pipeline only supports open-access data. Controlled-access files require authorization and are not supported.
- **Privacy**: [cohort_metadata.csv](/cohort_metadata.csv) may contain patient-level data. It is included in .gitignore to prevent accidental commits.
- **API limits**: The GDC API limits requests to 10,000 records. Queries exceeding this will trigger a warning.
