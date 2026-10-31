# Class-Imbalance Handling for Indonesian–English Code-Mixed Sentiment Classification

Code and supporting data for the article:

> Putra, B. P., Dewi, K. C., & Laili, N. *Class-Imbalance Handling for Indonesian–English Code-Mixed Sentiment Classification: A Comparison of SVM, CNN-BiLSTM, IndoBERT, and IndoBERTweet.* Journal of Mathematics and Scientific Computing with Applications (under review).

The study classifies the sentiment (Negative, Neutral, Positive) of 1,600 Indonesian–English code-mixed YouTube comments from four gadget-review videos. It compares a TF-IDF SVM, a hybrid CNN-BiLSTM, IndoBERT (IndoLEM, `indolem/indobert-base-uncased`), and IndoBERTweet (`indolem/indobertweet-base-uncased`), together with three class-imbalance strategies: class weighting, random oversampling, and focal loss.

## Repository contents

| File | Description |
|---|---|
| `Code-Mix Indonesia Inggris Classification.ipynb` | Single notebook that reproduces every table in the article: preprocessing, data split, cross-validation of all imbalance strategies, five-seed cross-validation, test-set evaluation, sensitivity analyses, leave-one-video-out evaluation, IndoBERTweet, error analysis, logistic regression, statistical tests, inter-annotator agreement, and filter validation. |
| `filter.py` | Dictionary-based code-mixing filter and deduplication applied before labelling. |
| `scraperkomen.py` | Comment collection with the YouTube Data API v3. Insert your own API key before running. |
| `label_annotator2.xlsx` | Labels of the second, independent annotator on a stratified subset of 320 comments, with the labels of the first annotator and of the LLM check. |
| `validasi_filter.xlsx` | Manual validation of the code-mixing filter on 200 flagged and 200 non-flagged comments. |
| `requirements.txt` | Main Python packages. |

### `label_annotator2.xlsx`

| Column | Description |
|---|---|
| `No` | Order in which the comment was shown to the second annotator |
| `Baris di fix_revisibaru.xlsx` | Excel row number of the comment in sheet `Data Sentimen` of the labelled dataset |
| `Video ID` | YouTube video ID |
| `Label Annotator 1` | Ground-truth label (first annotator) |
| `Label Annotator 2` | Label of the second annotator, who worked independently with the same guidelines and without access to the other labels |
| `Label LLM` | Label of the LLM consistency check (Claude Sonnet 5.5, claude.ai, 14 October 2026) |

The subset contains 100 Negative, 120 Neutral, and 100 Positive comments according to the first annotator.

### `validasi_filter.xlsx`

| Column | Description |
|---|---|
| `No` | Random order in which the comment was checked (the filter decision was hidden) |
| `Status filter` | `Lolos` = flagged as code-mixed by the filter; `Tidak lolos` = not flagged |
| `Indonesia + Inggris (manual)` | `Ya` if the comment contains both Indonesian and English according to the manual check |
| `Hanya kata serapan (manual)` | `Ya` if the English elements are only established loanwords (e.g., *review*, *budget*, *upgrade*); filled only when the previous column is `Ya` |

Precision is computed on the flagged sample; recall weights both samples to their population sizes (1,600 unique flagged and 5,884 unique non-flagged comments).

## Data availability

The labelled dataset (`fix_revisibaru.xlsx`) is **not** published in this repository, in order to protect the commenters: it contains the original comment texts. The two files above contain no comment text. The anonymised dataset is available from the corresponding author (kartikadewi@unesa.ac.id) on reasonable request.

## How to run

1. Open the notebook in Google Colab and select a GPU runtime (Runtime → Change runtime type → T4 GPU).
2. Upload the input files to `/content/`:
   - `fix_revisibaru.xlsx` (labelled dataset, available on request)
   - `label_annotator2.xlsx`
   - `validasi_filter.xlsx`
   - optionally, ZIP files of previously saved results, which are restored so that trained configurations are not retrained
3. Run all cells (Runtime → Run all). Results are saved per configuration to `MyDrive/hasil_penelitian` on Google Drive, so an interrupted session can be resumed by running the notebook again.
4. At the end, all results are packed into `hasil_penelitian.zip`.

Training every configuration from scratch takes several hours on a T4 GPU.

## Experimental settings

- Stratified split: 80% training, 10% validation, 10% test (seed 42).
- Stratified 5-fold cross-validation on training + validation data (1,440 comments), with 10% of each training fold held out for early stopping.
- Five seeds: 32, 37, 42, 47, 52.
- Deterministic PyTorch/cuDNN settings.
- Statistical tests: Nadeau–Bengio corrected t-test, corrected repeated-CV t-test (Bouckaert–Frank), Holm correction, bootstrap confidence intervals (5,000 resamples).

## Software

Python 3, PyTorch 2.11, Hugging Face Transformers 5.20, scikit-learn 1.6.1, statsmodels, SciPy, pandas, NumPy, matplotlib, openpyxl.

## Ethics

Comments were collected from public YouTube videos through the official YouTube Data API v3. Usernames and @mentions were replaced with `@user` before labelling and before any comment was sent to the LLM.

## Contact

Kartika Chandra Dewi (corresponding author), Universitas Negeri Surabaya, kartikadewi@unesa.ac.id
