# gdc-rnaseq-cohort-builder
GDC API pipeline to build, filter and export RNA-seq cohorts with somatic mutation and clinical metadata.

## Overview
Brief description of what the notebook does, 
the GDC API, and what the output files are.
????????

## Requirements
- Python 3.???
- See requirements.txt

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
4. Launch Jupyter Lab:
   ```bash
   jupyter lab
   ```
5. Run the full pipeline:
   - Open gdc_cohort_builder.ipynb and run section 0 locally.
   - Proceed to Section 1 to configure your cohort.
   - Run sections 1-5 locally.
   - Sections 6-7 involve downloading and processing large RNA-seq count files. These sections are designed to run on a remote compute enrironment.
  
6.A) HPC
- Transfer the cohort metadata file to the HPC and run in the apropriate path:
   ```bash
   scp cohort_metadata.csv user@hpc:/path/to/project/
   ```
- Set up the environment on the HPC
   ```bash
   conda create -n gdc-cohort python=3.10
   conda activate gdc-cohort
   pip install requests pandas jupyterlab
   ```
- Run Sections 6-7 on the cluster.
   

## Configuration



## Compatible Projects
Any project hosted on the [GDC Data Portal](https://portal.gdc.cancer.gov).
Change `PROJECT_ID` to target a different cancer type or study.

## Notes
- Sections 0–5 are designed to run locally
- Sections 6–7 are designed to run on an HPC due to file size
