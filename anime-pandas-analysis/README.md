# Anime Dataset — Pandas Data Profiling & Cleaning

A professional data-cleaning and exploratory analysis project built with **Python and Pandas** using an anime dataset containing information on titles, ratings, popularity, audience activity, production details, airing information, and other metadata.

This project focuses on a realistic data-science workflow rather than simply producing charts or statistics. The dataset contains inconsistent representations of missing information, categorical `"Unknown"` values, numeric values stored as strings, and duration values embedded in text. The goal is to profile, clean, validate, and prepare the data for reliable downstream analysis.

---

## Project Objectives

The main objectives of this project are to:

- Profile the dataset and understand its structure and quality.
- Identify duplicate records and different forms of missing data.
- Detect hidden missing values represented by `"Unknown"`.
- Investigate data types and identify incorrectly stored numerical variables.
- Apply column-specific cleaning decisions instead of blindly replacing values.
- Convert suitable variables to appropriate Pandas data types.
- Extract useful numerical features from text-based columns.
- Validate the cleaned dataset after transformation.
- Build a reliable dataset suitable for further analysis and real-world data science work.

---

## Dataset Overview

| Property | Value |
|---|---:|
| Rows | 17,562 |
| Columns | 35 |
| Duplicate rows initially detected | 0 |
| Columns containing `"Unknown"` initially | 25 |
| Explicit `NaN` values initially reported by Pandas | 0 |

The absence of explicit `NaN` values did **not** mean the dataset had no missing information. A substantial amount of missing information was encoded as the literal string `"Unknown"`.

This made the dataset useful for practicing an important real-world data-cleaning problem: **missing data is not always represented as `NaN`.**

---

## Dataset Quality Challenges

The dataset contains several data-quality issues that require investigation before analysis.

### 1. Hidden Missing Values

The literal value `"Unknown"` appears in multiple columns, including:

- `Score`
- `Episodes`
- `Ranked`
- `English_name`
- `Premiered`
- `Licensors`
- `Producers`
- `Studios`
- `Source`
- `Duration`
- `Rating`
- and other categorical fields.

The frequency of `"Unknown"` varies considerably between columns.

Rather than treating every occurrence identically, each column was evaluated according to its meaning and intended use.

### 2. Incorrect Data Types

Several variables that are logically numerical were initially stored as strings.

Examples include:

- `Score`
- `Ranked`
- `Episodes`
- `Score-1` through `Score-10`

Values such as `"8.0"` are text representations of numbers and must be converted before numerical analysis can be performed correctly.

### 3. Text-Based Duration

The original `Duration` column contains values such as:

```text
24 min. per ep.
1 hr. 30 min.
18 sec. per ep.
Unknown
```

Because the numerical information is embedded in text, the column cannot be used directly for numerical analysis.

A new `Duration_minutes` feature was therefore created by extracting hours, minutes, and seconds and converting them into total minutes.

For example:

```text
1 hr. 30 min. → 90 minutes
18 sec.        → 0.30 minutes
24 min.        → 24 minutes
```

The original `Duration` column is retained so that the transformation does not destroy the source information.

---

## Data Cleaning Approach

The project follows a structured data-science workflow:

```text
Profile
   ↓
Understand
   ↓
Decide
   ↓
Clean
   ↓
Validate
   ↓
Analyze
```

### Step 1 — Preserve the Raw Dataset

The original dataset is kept unchanged.

A separate working copy is created:

```python
anime_data_clean = anime_data.copy()
```

This provides a reproducible distinction between the raw source data and the cleaned dataset.

---

### Step 2 — Identify Hidden Missing Values

Instead of relying only on:

```python
anime_data.isnull().sum()
```

the dataset was also checked for the literal `"Unknown"` value.

This revealed that the dataset contained substantial missing-like information that Pandas could not identify automatically as null data.

---

### Step 3 — Clean Numerical Variables

Variables such as `Score`, `Ranked`, `Episodes`, and `Score-1` through `Score-10` were investigated to determine whether `"Unknown"` was the primary non-numeric issue.

Where appropriate, `"Unknown"` was converted to `NaN` and the columns were assigned suitable numeric data types.

Nullable Pandas integer types (`Int64`) were used where integer values needed to coexist with missing values.

Examples include:

```text
Ranked
Episodes
Score-1 ... Score-10
```

`Score` was converted to a floating-point type because its values contain decimal scores.

---

### Step 4 — Handle Categorical `"Unknown"` Values Carefully

Not every `"Unknown"` value should automatically become `NaN`.

For categorical and descriptive fields such as:

- `Genres`
- `Type`
- `Premiered`
- `Producers`
- `Licensors`
- `Studios`
- `Source`
- `Rating`
- `English_name`
- `Japanese_name`

`"Unknown"` can communicate that the information was not available or recorded.

These values were therefore retained rather than blindly replacing every occurrence with `NaN`.

This distinction is important because data cleaning should be based on **column semantics**, not only on the appearance of a particular string.

---

### Step 5 — Engineer Duration

The original `Duration` column was preserved and a numerical feature was created:

```text
Duration_minutes
```

The transformation accounts for:

- hours
- minutes
- seconds
- missing duration values

Regular expressions were used to identify the different time components before converting them into a common unit.

---

## Current Missing-Value Profile

After the numerical cleaning performed so far, the cleaned dataset contains explicit missing values in variables where `"Unknown"` was converted to missing data.

| Column | Missing Values |
|---|---:|
| `Score` | 5,141 |
| `Ranked` | 1,762 |
| `Episodes` | 516 |
| `Score-10` | 437 |
| `Score-9` | 3,167 |
| `Score-8` | 1,371 |
| `Score-7` | 503 |
| `Score-6` | 511 |
| `Score-5` | 584 |
| `Score-4` | 977 |
| `Score-3` | 1,307 |
| `Score-2` | 1,597 |
| `Score-1` | 459 |

Other categorical `"Unknown"` values remain intentionally represented as `"Unknown"` where they have not yet been determined to require conversion.

---

## Key Data-Science Decisions

A major focus of this project is **reasoning about data quality**, not simply applying transformations.

### Decision: Do not blindly replace `"Unknown"`

A global operation such as:

```python
df.replace("Unknown", np.nan)
```

can be convenient, but it may remove meaningful categorical information.

Instead, the project evaluates variables individually.

### Decision: Keep the original `Duration`

The original text representation contains useful information and provides traceability for the engineered `Duration_minutes` feature.

### Decision: Use nullable integer types

Pandas' nullable `Int64` type allows integer columns to contain missing values without forcing the entire column into floating-point representation.

### Decision: Separate cleaning from analysis

The cleaning stage is designed to produce a trustworthy dataset first. Analytical questions can then be answered using the validated data rather than mixing cleaning decisions with conclusions.

---

## Tools & Technologies

- **Python**
- **Pandas**
- **NumPy**
- **Regular Expressions (`re`)**
- **Jupyter Notebook / VS Code**
- **Git & GitHub**

---

## Project Structure

A suggested repository structure is:

```text
anime-pandas-analysis/
│
├── Data/
│   └── anime.csv
│
├── notebooks/
│   └── anime_data_analysis.ipynb
│
├── README.md
│
└── ...
```

The exact folder structure may vary depending on the organization of the wider GitHub repository.

---

## Validation

After cleaning, the dataset is re-profiled rather than assuming that transformations worked correctly.

Important validation checks include:

```python
anime_data_clean.shape
anime_data_clean.dtypes
anime_data_clean.duplicated().sum()
anime_data_clean.isnull().sum()
```

Additional checks include verifying:

- remaining `"Unknown"` values
- numerical ranges
- data types
- missing-value percentages
- duration conversion results
- unexpected or invalid values
- consistency between related variables

The objective is to ensure that the cleaning process improves data quality without introducing new errors.

---

## Planned Analysis

After the cleaning and validation stage, the project will move beyond data preparation into real-world analysis questions.

Potential areas include:

- What factors are associated with higher anime scores?
- How does popularity differ across anime types?
- How does audience engagement vary across ratings?
- Which studios have produced the largest number of titles?
- How does episode count relate to audience engagement?
- How does duration vary by anime type?
- How do airing periods relate to popularity and ratings?
- What patterns exist between score distributions and audience activity?
- Which variables contain enough reliable information to support meaningful analysis?

The final questions will be selected based on the quality and suitability of the cleaned data rather than forcing analysis onto unreliable variables.

---

## What This Project Demonstrates

This project demonstrates practical skills relevant to entry-level data science and data analytics roles:

- Data profiling
- Data-quality assessment
- Missing-data detection
- Data cleaning
- Type conversion
- Feature engineering
- Regular expressions
- Pandas indexing and filtering
- Categorical-data reasoning
- Nullable data types
- Data validation
- Reproducible analysis
- Translating messy real-world data into analysis-ready data

The emphasis is on **understanding why a transformation is appropriate**, not simply knowing the syntax required to perform it.

---

## Project Status

**Current stage:** Data profiling and cleaning

- [x] Load dataset
- [x] Inspect dataset structure
- [x] Check for duplicate rows
- [x] Check explicit missing values
- [x] Identify hidden `"Unknown"` values
- [x] Investigate affected columns
- [x] Clean numerical `"Unknown"` values
- [x] Convert appropriate numerical columns
- [x] Engineer `Duration_minutes`
- [ ] Complete final validation
- [ ] Perform exploratory analysis
- [ ] Answer real-world analytical questions
- [ ] Document analytical findings
- [ ] Finalize portfolio presentation

---

## Why This Project Matters

Real-world datasets rarely arrive perfectly structured.

A data scientist must be able to determine:

> **What is wrong with the data, why it is wrong, how it should be handled, and whether the solution actually worked.**

This project is designed around that workflow.

Rather than treating Pandas as a collection of commands, the project uses Pandas to develop a repeatable approach to **data profiling, cleaning, validation, and analysis**.

---

## Author

**Data Science Portfolio Project**

Built as part of a practical Python and Pandas data-science learning portfolio.
