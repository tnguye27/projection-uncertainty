# projection-uncertainty

## Overview

This repo extracts paragraph-level text from CBO *Budget and Economic Outlook* reports, matches paragraphs to CBO outlay subcategories, calculates text-based uncertainty scores, and compares those scores to CBO outlay projection errors.

The workflow uses four notebooks:

1. `01_chunk_paragraphs.ipynb`
2. `02_match_projection_errors.ipynb`
3. `03_FinDict.ipynb`
4. `04_Error_Corr.ipynb`

CBO report PDFs are included directly in the repo, so the old scraper is no longer needed.

## Requirements

Python 3.10+ recommended.

Install dependencies with:

```bash
pip install -r requirements.txt
```

Main dependencies include `pandas`, `numpy`, `scipy`, `PyMuPDF`, `matplotlib`, `seaborn`, and `gensim`.

If using Word2Vec-related functionality, download the relevant model files separately. Large model files should not be committed to the repo.

## Input data

- CBO *Budget and Economic Outlook* PDFs are included in the repo. Original source: [CBO Major Recurring Reports](https://www.cbo.gov/about/products/major-recurring-reports#1).
- Projection error data are pulled from CBO’s [`outlay_projection_errors.csv`](https://raw.githubusercontent.com/US-CBO/eval-projections/main/output_data/outlay_projection_errors.csv).
- Uncertainty terms are based partly on the [Loughran-McDonald Master Dictionary](https://sraf.nd.edu/loughranmcdonald-master-dictionary/) plus additional context words.

## Run order

Run the notebooks in order.

### 1. `01_chunk_paragraphs.ipynb`

This notebook preprocesses raw Congressional Budget Office (CBO) report PDFs from 2000 to 2026. It transforms unstructured PDF text blocks into a clean, tabular dataset for text analysis.

Main output:

```text
data_files/chunked_paragraphs.csv
```

### 2. `02_match_projection_errors.ipynb`

Matches paragraph text to CBO outlay subcategories and merges in projection error data.

Current approach: uses exact matches for matching text with subcategories and no longer uses Word2Vec synonyms for this step.

### 3. `03_FinDict.ipynb`

Calculates uncertainty scores using finance/economic uncertainty terms.

Current approach: uses the LM dictionary plus additional context words to generate uncertainty-related synonyms. Projection errors are converted to absolute values before the main correlation analysis.

### 4. `04_Error_Corr.ipynb`

Produces the main figures and correlation analysis comparing text-based uncertainty scores with projection errors.

Main figures:

1. Bar chart showing the correlation between uncertainty and projection errors by program area.
2. Blue/red line charts showing uncertainty over time against projection error for programs with large correlations.

Notes on selected programs:

- **Medicaid:** Negative correlation because uncertainty is high in 2012, while the largest errors occur in 2020, likely due to the pandemic.
- **Net interest:** Similar pattern, with high uncertainty earlier in the sample around the dot-com bubble and financial crisis, though this interpretation is less certain.
- **Medicare:** Appears to reflect the spending slowdown around 2015, which was known and discussed at the time.

## Notes

Run notebooks in order, since later notebooks depend on earlier outputs. If re-running with changed settings, delete or overwrite prior outputs in `data_files/` to avoid confusion. Large external model files, such as Word2Vec vectors, should be downloaded separately and not committed to the repo.
