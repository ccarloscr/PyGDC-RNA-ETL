# gdc-rnaseq-cohort-builder
GDC API pipeline to build, filter and export RNA-seq cohorts with somatic mutation and clinical metadata.

## Overview
Brief description of what the notebook does, 
the GDC API, and what the output files are.
????????

## Requirements
- Python 3.???
- See requirements.txt

## Usage
1. Clone the repository
2. Install dependencies: `pip install -r requirements.txt`
3. Open `gdc_cohort_builder.ipynb`
4. Configure the parameters in Section 0
5. Run sections 0–5 locally
6. Copy `luad_metadata.csv` to the HPC via `scp`
7. Run sections 6–7 on the HPC

## Configuration



## Compatible Projects
Any project hosted on the [GDC Data Portal](https://portal.gdc.cancer.gov).
Change `PROJECT_ID` to target a different cancer type or study.

## Notes
- Sections 0–5 are designed to run locally
- Sections 6–7 are designed to run on an HPC due to file size
