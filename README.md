# projection-uncertainty

## Overview

This repo extracts paragraph-level text from CBO *Budget and Economic Outlook* reports, matches paragraphs to CBO outlay subcategories, calculates textual uncertainty scores, and compares those scores to CBO outlay projection errors.

The workflow uses four notebooks:

1. `01_chunk_paragraphs.ipynb`
2. `02_text_to_subcategory_mapping.ipynb`
3. `03_text_findict_uncertainty_scoring.ipynb`
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

### 2. `02_text_to_subcategory_mapping.ipynb`

This notebook uses cleaned text paragraphs from 'chunked_paragraphs.csv' and matches each paragraph with outlay subcategories, such as Social Security or Medicare. 

Current approach: Creates a phrase dictionary consisting of exact matches (e.g. "Social Security") along with related words for each subcategory in the context of government spending. Then, search each paragraph for those phrase and matches it with the corresponding subcategory. 

### 3. `03_text_findict_uncertainty_scoring.ipynb`

This notebook calculates uncertainty scores using finance/economic uncertainty terms.

Current approach: Uses the LM dictionary plus additional context words to generate uncertainty-related synonyms. Projection errors are converted to absolute values before the main correlation analysis.

### 4. `04_Error_Corr.ipynb`

This notebook calculates correlation between textual uncertainty scores with projection errors and produces main figures.

Main figures:

1. Bar chart showing the correlation between uncertainty and projection errors by program area.
2. Blue/red line charts showing uncertainty over time against projection error for programs with large correlations.

Notes on selected programs:

- **Medicaid:** Negative correlation because uncertainty is high in 2012, while the largest errors occur in 2020, likely due to the pandemic.
- **Net interest:** Similar pattern, with high uncertainty earlier in the sample around the dot-com bubble and financial crisis, though this interpretation is less certain.
- **Medicare:** Appears to reflect the spending slowdown around 2015, which was known and discussed at the time.

## Notes

Run notebooks in order, since later notebooks depend on earlier outputs. If re-running with changed settings, delete or overwrite prior outputs in `data_files/` to avoid confusion. Large external model files, such as Word2Vec vectors, should be downloaded separately and not committed to the repo.
