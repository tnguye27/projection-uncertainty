# projection-uncertainty

## Overview

This repo extracts paragraph-level text from CBO *Budget and Economic Outlook* reports, matches paragraphs to CBO outlay subcategories, calculates text-based uncertainty scores, and compares those scores to CBO outlay projection errors.

The main workflow is organized into four Jupyter notebooks:

1. `01_chunk_paragraphs.ipynb`
2. `02_match_projection_errors.ipynb`
3. `03_FinDict.ipynb`
4. `04_Error_Corr.ipynb`

PDF files are included directly in the repo, so the old scraper is no longer needed.

---

## Requirements

Python 3.10+ recommended.

Install dependencies with:

```bash
pip install -r requirements.txt
```

Core packages used include:

- `pandas`
- `numpy`
- `PyMuPDF` / `fitz`
- `pyarrow`
- `scikit-learn`
- `nltk`
- `gensim`
- `matplotlib`
- `seaborn`
- `scipy`

If using Word2Vec-related functionality, download the relevant model/source files separately. For example, pretrained Google News Word2Vec vectors can be accessed through `gensim` or downloaded from common public mirrors. Note that these files are large and should not be committed to the repo.

---

## Input data

### CBO reports

The analysis uses CBO *Budget and Economic Outlook* PDF reports. These are included directly in the repo.

Original source:

https://www.cbo.gov/about/products/major-recurring-reports#1

### Projection errors

CBO outlay projection error data are pulled from:

https://raw.githubusercontent.com/US-CBO/eval-projections/main/output_data/outlay_projection_errors.csv

### Uncertainty dictionary

The uncertainty terms are based in part on the Loughran-McDonald Master Dictionary, plus additional context words.

Source:

https://sraf.nd.edu/loughranmcdonald-master-dictionary/

---

## Run order

Run the notebooks in the following order.

### 1. `01_chunk_paragraphs.ipynb`

**Purpose:**  
Extracts text from the CBO PDF reports and chunks the text into paragraph-level rows.

**Main output:**  

```text
data_files/chunked_paragraphs.csv
```

---

### 2. `02_match_projection_errors.ipynb`

**Purpose:**  
Matches paragraph text to CBO outlay subcategories and merges in projection error data.

The current matching approach uses exact matches for matching text with subcategories and no longer uses Word2Vec synonyms for this step.

**Main outputs:**  
Outputs are saved in `data_files/` and used by later notebooks.

---

### 3. `03_FinDict.ipynb`

**Purpose:**  
Calculates uncertainty scores using finance/economic uncertainty terms.

The uncertainty-score construction was updated to generate synonyms differently. The current version uses the LM dictionary plus additional context words. Previously, subcategories may have been used more directly as uncertainty terms.

Projection errors are converted to absolute values before the main correlation analysis.

**Main outputs:**  
Uncertainty-scored paragraph/program data used in the final analysis.

---

### 4. `04_Error_Corr.ipynb`

**Purpose:**  
Produces the main figures and correlation analysis comparing text-based uncertainty scores with projection errors.

Main figures include:

1. A bar chart showing the correlation between uncertainty and projection errors by program area.
2. Blue/red line charts showing the uncertainty measure over time against projection error for programs with large correlations.

Programs of interest include:

- **Medicaid:** Shows a negative correlation because there was a lot of uncertainty expressed in 2012, while the largest errors occur in 2020, likely due to the pandemic.
- **Net interest:** Looks similar, with a lot of uncertainty earlier in the sample around the dot-com bubble and financial crisis, though this interpretation is less certain.
- **Medicare:** Appears to reflect the spending slowdown around 2015; this slowdown was known and discussed at the time.

---

## Notes / troubleshooting

- Run the notebooks in order, since later notebooks depend on outputs from earlier ones.
- If re-running with changed settings, delete or overwrite prior outputs in `data_files/` to avoid confusion.
- If a step fails, confirm that the expected input file from the previous notebook exists.
- Large external model files, such as Word2Vec vectors, should be downloaded separately and not committed to the repo.
