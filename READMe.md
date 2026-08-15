
# Table of Contents

- [Executive Diagram Summary](#executive-diagram-summary)
- [Project Detailed Summary](#project-detailed-summary)
  - [Dataset](#dataset)
  - [Processing](#processing)
  - [Label Mapping](#label-mapping)
  - [Final Dataset](#final-dataset)
  - [EDA Findings](#eda-findings)
  - [YARA Baseline](#yara-baseline)
  - [ML Model](#ml-model)
  - [Initial ML Results](#initial-ml-results)
- [Current State - Status Quo (7th Aug 2026)](#current-state---status-quo-7th-aug-2026)
  - [Strength](#strength)
  - [Weakness](#weakness)
- [Planned/In-progress State](#plannedin-progress-state)
  - [Strength](#strength-1)
  - [Weakness](#weakness-1)

---

# Executive Diagram Summary

---

# Project Detailed Summary

## Dataset

**PhiUSIIL Phishing URL Dataset**

**Source:** [Kaggle — PhiUSIIL Phishing URL Dataset](https://www.kaggle.com/datasets/ndarvind/phiusiil-phishing-url-dataset/data)

### Processing

- Selected only `URL` and `label`
- Removed missing URLs/labels
- Removed duplicate URLs
- Saved as `cleaned_url_list.csv`

### Label Mapping

```text
0 = Phishing
1 = Legitimate
```

### Final Dataset

**235,370 URLs**

### EDA Findings

- **57.29% legitimate / 42.71% phishing** → reasonably balanced
- Phishing URLs have a higher median length (**34**) than legitimate URLs (**27**)
- Phishing URLs have substantially larger length outliers, reaching **6,097 characters**
- Keywords such as `login`, `verify`, `account`, `secure`, and `update` show strong class differences
- URL length and individual keywords provide useful signals but should **not be used as standalone detectors**

### YARA Baseline

```text
Precision: 99.13%
Recall:    11.10%
F1-score:  19.97%
```

YARA produced **89,360 false negatives**, demonstrating that signature/rule-based detection alone misses a large portion of phishing URLs.

### ML Model

A **Logistic Regression classifier** was trained using **character-level TF-IDF features**.

```text
Raw URL
   ↓
Character TF-IDF
3–5 character n-grams
   ↓
Logistic Regression
   ↓
Phishing / Legitimate
```

The TF-IDF vectorizer was fitted **only on the training set**, while the test set was transformed using the learned vocabulary.

### Initial ML Results

```text
Accuracy:  99.59%
Precision: 100.00%
Recall:    99.04%
F1-score:  99.52%
```

The model substantially outperformed the YARA baseline on the random stratified test split. However, the unusually high performance requires further validation, particularly for **domain leakage and protocol-related shortcut learning**, before treating it as representative of real-world performance.

---

# Current State - Status Quo (7th Aug 2026)

![Current State Diagram](https://github.com/user-attachments/assets/9873ca69-6cec-45d6-b577-b47efd786b7a)

### Strength

It can scan 6 file types:

- `apk`
- `elf`
- `pe`
- `win64`
- `win32`
- `pdf`
- `all` (general/guessing)

### Weakness

The URL scanning is unreliable. It is only based on blacklisting and structural scanning. Cannot adapt to the fast-growing malicious URLs.

---

# Planned/In-progress State

Updating only the URL scanning feature.

![Planned State Diagram](https://github.com/user-attachments/assets/34c15641-1977-4be5-a526-f41d8cf57459)

### Strength

It can scan the 6 file types **and** the URL. Both features have 3 layers:

- **Layer 1:** From blacklisting database (CSV files)
- **Layer 2:** YARA rules
- **Layer 3:** LLM (for file scanning) and ML (for URL scanning)

### Weakness

Unidentified.

