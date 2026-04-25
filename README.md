# gdc-rnaseq-cohort-builder
GDC API pipeline to build, filter and export RNA-seq cohorts with somatic mutation and clinical metadata.

## Overview
This notebook queries the [GDC API](https://api.gdc.cancer.gov) to build, filter and download RNA-seq cohorts from any GDC-hosted project. It generates an annotated metadata file and a STAR raw count matrix, ready for downstream machine learning or differential expression analysis.

Compatible with any project hosted on the [GDC Data Portal](https://portal.gdc.cancer.gov) — not limited to TCGA.

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
4. Launch Jupyter Lab:
   ```bash
   jupyter lab
   ```
5. Run the pipeline:
   - Open gdc_cohort_builder.ipynb.
   - Configure your cohort parameters in **Section 0**.
   - Run sections 1-5 locally.
   - Sections 6-7 involve downloading and processing large RNA-seq count files. These sections are designed to run on a remote compute environment (HPC or Cloud, see below).
  
6.A) HPC
- Transfer the cohort metadata file to the HPC and run in the appropriate path:
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
   
6.B) Cloud
- The general workflow is the same regardless of provider:
     - Launch a virtual machine instance
     - Install Miniconda
     - Clone the repository and install dependencies
     - Transfer the cohort metadata file to the instance
       ```bash
       # AWS example
       scp -i your-key.pem cohort_metadata.csv ec2-user@your-instance-ip:/path/to/project/
       ```
     - Open Jupyter Lab on the instance and run Sections 6-7


## Configuration


## Notes
- Sections 0–5 are designed to run locally
- Sections 6–7 are designed to run on an HPC due to file size
