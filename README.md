# PyGDC-RNA-ETL

**PyGDC-RNA-ETL** is a robust pipeline designed to automate the extraction, transformation, and integration of genomic data from the [GDC Data Portal](https://portal.gdc.cancer.gov).

The toolkit bridges the gap between raw GDC API responses and analysis-ready datasets by coupling RNA-seq expression levels, somatic mutation calls, and clinical metadata into a unified format optimized for Machine Learning and Differential Expression workflows.

Compatible with any project hosted on the [GDC Data Portal](https://portal.gdc.cancer.gov), not limited to TCGA.

### Key Capabilities:
- **Automated Cohort Discovery**: Filter across thousands of GDC cases using flexible clinical and molecular criteria.
- **Clinical Data Standardization**: Automated expansion of AJCC pathologic stages and normalization of exposure/demographic variables.
- **Integrated Metadata Mapping**: Couples mutation status with gene expression counts at the sample level.
- **Scalable Data Architecture**: Handles large-scale downloads by separating cohort configuration from remote data retrieval, optimized downloads by batching and parallelizing API requests.
- **Ready-to-use Output**: A clean parquet file containing the matrix of raw RNA-seq expression data per gene and sample is outputted, ready for downstream differential expression or Machine Learning analyses.

---

## Pipeline Overview
### [01_cohort_construction.ipynb](/scripts/01_cohort_construction.ipynb)
- **Function**: Query GDC API, filter cohort, annotate mutation & clinical labels, export metadata.
- **Environment**: Local.
- **Output**: [cohort_metadata.csv](/Data/sample_metadata.csv).

### [02_download_counts.py](/scripts/02_download_counts.py)
- **Function**: Download STAR count files listed in [cohort_metadata.csv](/Data/sample_metadata.csv).
- **Parallelization**: By default uses a 50-file batch size for API requests and 8 parallel download threads. Can be overwritten in CLI using `--batch-size` and `--workers`, respectively.
- **Environment**: HPC / Cloud for convenience and reliability as it may take a while if the number of files to download is high, speed is not dependant on computing power.
- **Output**: raw .tsv count files.

### [03_build_count_matrix.py](/scripts/03_build_count_matrix.py)
- **Function**: Merge individual count files into a single sample × gene matrix containing all RNA-seq raw counts.
- **Environment**: Local.
- **Output**: [sample_labels.csv](/Data/sample_labels.csv) and [sample_counts_matrix.parquet](/Data/sample_counts_matrix.parquet)
---

## Requirements
- Python 3.10
- Anaconda or Miniconda
---

## Installation
#### 1. Clone the repository:
   ```bash
   git clone https://github.com/ccarloscr/PyGDC-RNA-ETL.git
   cd PyGDC-RNA-ETL
   ```
#### 2. Set up the environment
   ```bash
   conda create -n gdc-cohort python=3.10 -y
   conda activate gdc-cohort
   pip install requests pandas jupyterlab pyarrow
   ```
---

## Usage Guide
#### Step 1 — Build Cohort
Launch Jupyter Lab and open [01_cohort_construction.ipynb](/scripts/01_cohort_construction.ipynb):

Configure your cohort in Section 1 (project ID, gene mutations, sample type, and any clinical filters) and run all cells.
The total number of samples found, a cohort summary and a cohort composition plot are generated within the cohort builder notebook.

#### Step 2 — Download Count Files
It is recommended to run this step in your remote environment. Transfer [cohort_metadata.csv](/Data/sample_metadata.csv) and [02_download_counts.py](/scripts/02_download_counts.py) into the same remote directory and run:
   ```bash
   # Activate the environment
   conda activate gdc-cohort

   # Run the script
   python 02_download_counts.py
   ```

#### Step 3 — Build Matrix
Once downloads are complete, merge the files:
   ```bash
   python 03_build_count_matrix.py
   ```

### Step 4 - Final output files
Two different files are outputted from Step 3: [sample_labels.csv](/Data/sample_labels.csv) and [sample_counts_matrix.parquet](/Data/sample_counts_matrix.parquet).

- The `sample_labels.csv` file contains the full molecular (mutational status) and clinical data for each `sample_id`.
- The `sample_counts_matrix.parquet` file is in parquet format and contains the matrix of raw RNA-seq expression. Each row is a gene and each column corresponds to a `sample_id`.

>**Note:** By separating the sample metadata from the raw RNA-seq expression data, the merged count matrix is ready for downstream analyses.

---

## Configuration Reference
All parameters for the cohort construction are located in Section 1 of [01_cohort_construction.ipynb](/scripts/01_cohort_construction.ipynb):
| GDC API Field | Description | Typical Values |
| :--- | :--- | :--- |
| `cases.samples.sample_type` | Sample type | Primary Tumor, Solid Tissue Normal, Recurrent Tumor |
| `gene_mutation` | Somatic mutation in selected genes | <details><summary>*View examples*</summary> <ul><li>TP53</li><li>TP53 and EGFR</li><li>EGFR or KRAS</li></details>| |
| `cases.tobacco_smoking_status` | Smoking Status | <details><summary>*View all 6 options*</summary> <ul><li>Lifelong Non-Smoker</li><li>Current Smoker</li><li>Current Reformed Smoker for > 15 yrs</li><li>Current Reformed Smoker for < or = 15 yrs</li><li>Current Reformed Smoker, Duration Not Specified</li><li>Not Reported</li></details>|
| `cases.diagnoses.ajcc_pathologic_stage` | Clinical stage | Stage I, II, III, IV |
| `cases.demographic.vital_status` | Vital status | Alive, Dead |
| `cases.demographic.gender` | Sex at birth | male, female |
| `cases.diagnoses.age_at_diagnosis` | Age at diagnosis (in days) | *integer* |

> **Note on Clinical Stages**: The pipeline automatically expands general stages (Stages I-IV) into their clinical subtypes (IA, IB, etc.) using the STAGE_GROUPS dictionary defined in the Section 1 of [01_cohort_construction.ipynb](/scripts/01_cohort_construction.ipynb).
---

## Notes
- **Access**: This pipeline only supports open-access data. Controlled-access files require authorization and are not supported.
- **Privacy**: [cohort_metadata.csv](/Data/sample_metadata.csv) may contain patient-level data. It is included in .gitignore to prevent accidental commits.
- **API limits**: The GDC API limits requests to 10,000 records. Queries exceeding this will trigger a warning.
- **Sample Data**: Sample output files are provided in [data/](data/). All data originates from the NCI Genomic Data Commons (GDC) and follows their Open Access Data Policy.
---

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
