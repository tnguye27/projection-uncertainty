## projection-uncertainty (quick README)

### Overview
This repo extracts paragraph-level text from CBO Budget and Economic Outlook reports and assigns each paragraph to a CBO outlay subcategory (or leaves it unclassified). It then produces summary statistics/diagnostics.

### Requirements
- Python 3.10+ recommended  
- Install dependencies (example): `pandas`, `numpy`, `PyMuPDF (fitz)`, `pyarrow`, `gensim`, `scikit-learn`, `nltk`, `matplotlib`, `seaborn`

### Input data
- Place PDFs in the expected reports folder (see `01_scrape_outlook_reports` for the directory name/path it uses).
- Subcategory/category/component mapping is pulled from:
  https://raw.githubusercontent.com/US-CBO/eval-projections/main/output_data/outlay_projection_errors.csv

### Run order (important)
Run these files in order:

1) **01_scrape_outlook_reports**  
   - Purpose: Extracts text from PDFs and chunks into paragraph-level rows.  
   - Output: `data_files/chunked_paragraphs.csv`

2) **02_match_prjection_errors.ipynb**  
   - Purpose: Interactive exploration/debugging of matching/classification logic and outputs.  
   - Output: varies (used for inspection/QA)

3) **03_match_proejction_errors**  
   - Purpose: Runs the classification pipeline on `chunked_paragraphs.csv` and merges in category/component from the CBO mapping.  
   - Output (main): `data_files/flattened_cbo_reports_*.parquet` (see script for exact filename)  
   - Output (centroids, if enabled): `data_files/subcategory_centroids.parquet`

4) **04_summary_statisitcs**  
   - Purpose: Produces summary tables/diagnostics (match counts, similarity distributions, plots, etc.) from the Parquet outputs.  
   - Output: printed tables and/or figures (depending on script settings)

### Notes / troubleshooting
- If you re-run matching with new settings, delete or overwrite prior outputs in `data_files/` to avoid confusion.
- If Word2Vec downloads slowly the first time: it’s a large model and will be cached for future runs.
- If a step fails, confirm the expected input file from the previous step exists in `data_files/`.