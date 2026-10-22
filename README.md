# Marketing A/B Testing & Conversion Analysis

[![Python](https://img.shields.io/badge/Python-Analysis-blue?logo=python&logoColor=white)]()
[![Jupyter Notebook](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter&logoColor=white)]()
[![Statistics](https://img.shields.io/badge/Statistics-A%2FB%20Testing-purple)]()

---

## Table of Contents

- [Overview](#overview)
- [Business Problem](#business-problem)
- [Dataset](#dataset)
- [Tools and Technologies](#tools-and-technologies)
- [Project Workflow](#project-workflow)
- [Data Cleaning and Quality Checks](#data-cleaning-and-quality-checks)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [A/B Testing Methodology](#ab-testing-methodology)
- [Key Results](#key-results)
- [Additional Exploratory Findings](#additional-exploratory-findings)
- [Business Recommendations](#business-recommendations)
- [Limitations](#limitations)
- [Project Structure](#project-structure)
- [How to Run the Project](#how-to-run-the-project)
- [Contact](#contact)

---

## Overview

This project uses Python in Jupyter Notebook to evaluate whether an advertising campaign produces a higher conversion rate than a public service announcement (PSA).

The analysis combines data quality checks and exploratory data analysis (EDA) with a one-sided, two-proportion z-test. It compares conversion rates, quantifies the observed improvement, and examines ad-exposure patterns by frequency, day, and hour.

The notebook also explains the limitations of the results and separates the main A/B test findings from other patterns that do not prove cause and effect.

## Business Problem

The business wants to understand whether showing users an advertisement leads to more conversions than showing them a PSA instead.

The analysis addresses these questions:

1. Did the ad group achieve a higher conversion rate than the PSA group?
2. Is the observed difference statistically significant?
3. How large is the improvement in percentage points and relative terms?
4. What lower confidence bound can be estimated for the conversion-rate difference?
5. How do ad exposure and observed conversion rates vary by frequency, day, and hour?
6. What can the findings support, and what requires further testing?

## Dataset

The notebook's outputs describe a user-level dataset with **588,101 rows and 6 columns after removing the unnecessary index column**. The original file also contains an `Unnamed: 0` index column, making 7 columns before cleaning.

### Columns used

| Column | Description |
|---|---|
| `user id` | Identifier for each user |
| `test group` | Experiment group: `ad` or `psa` |
| `converted` | Whether the user converted (`True`/`False`) |
| `total ads` | Total number of ads seen by the user |
| `most ads day` | Day on which the user saw the most ads |
| `most ads hour` | Hour at which the user saw the most ads |

### Dataset quality

The notebook's quality checks reported:

- **Rows after cleaning:** 588,101
- **Columns after cleaning:** 6
- **Missing values:** 0
- **Duplicate user IDs:** 0
- **Unexpected experiment groups:** None
- **Non-binary conversion values:** None
- **Negative ad counts:** 0

### Experiment group sizes in the original data

| Group | Users | Conversions |
|---|---:|---:|
| Ad (`ad`) | 564,577 | 14,423 |
| PSA (`psa`) | 23,524 | 420 |
| **Total** | **588,101** | **14,843** |

**Dataset file note:** This project uses the `marketing_AB.csv`. dataset for exploratory data analysis (EDA) and A/B testing. Place the CSV file in the dataset/ folder and use the relative file path in the notebook to load the data. Ensure the dataset is in the correct location before running the notebook from start to finish..

## Tools and Technologies

| Tool / Library | Purpose |
|---|---|
| Python | Analysis and calculations |
| Pandas | Data loading, cleaning, grouping, and summary tables |
| NumPy | Numerical calculations |
| SciPy (`scipy.stats`) | Statistical calculations for the z-test and confidence bound |
| Matplotlib | Charts and visual comparisons |
| Jupyter Notebook | Data analysis and testing |
| GitHub | Project hosting and version control |

## Project Workflow

1. **Load and inspect the data** - review columns, data types, unique values, and missing values.
2. **Clean the dataset** - remove the unnecessary index column and standardize the conversion field as Boolean.
3. **Validate data quality** - check duplicate users, valid experiment groups, binary conversion values, and non-negative ad exposure.
4. **Review group sizes** - identify the substantial difference in the number of users assigned to each group.
5. **Create a balanced analytical sample** — randomly select 23,524 users from the ad group to match the 23,524 PSA users, using `random_state=42`.
6. **Compare conversion rates** - calculate the number of conversions and conversion rate in each balanced group.
7. **Run the hypothesis test** - use a one-sided two-proportion z-test to test whether the ad group's conversion rate is higher.
8. **Measure the effect size** - calculate absolute uplift in percentage points and relative lift.
9. **Estimate uncertainty** - calculate a 95% one-sided lower confidence bound for the conversion-rate difference.
10. **Explore ad exposure** - examine exposure counts, day/hour patterns, correlation, and conversion rates across exposure quartiles.
11. **Interpret and recommend** - separate the main A/B test result from exploratory associations and identify follow-up experiments.

## Data Cleaning and Quality Checks

The notebook removes `Unnamed: 0`, which is an unnecessary index column, and keeps the six fields used for the analysis. It standardizes `converted` as a Boolean field.

Before testing, it checks for missing values, duplicate user IDs, unexpected group labels, non-binary conversion values, and negative ad counts. The notebook outputs show no issues in these checks.

### Why balance the analytical sample?

The original dataset contains 564,577 ad users and 23,524 PSA users. The notebook randomly samples 23,524 users from the ad group, creating a balanced analytical sample of **47,048 users**.

Balancing the sample helps us compare the Ad and PSA groups using equal numbers of users, so that the larger size of the Ad group does not dominate the comparison. By randomly selecting 23,524 users from each group, we can compare their conversion rates on an equal sample-size basis and assess whether the Ad group performs better than the PSA group. However, equal group sizes do not guarantee that both groups have equal ad exposure or identical characteristics.

## Exploratory Data Analysis

The notebook explores the experiment data before and after the main statistical comparison.

### 1. Conversion overview

It counts conversions and users in each experiment group, then calculates:

**Conversion Rate = Number of Conversions / Number of Users × 100**

### 2. Ad exposure

It summarizes the number of ads seen by users in each group using the count, mean, median, minimum, and maximum. It also visualizes the distribution of ad exposure.

### 3. Exposure by day and hour

It calculates the percentage of users associated with each `most ads day` and `most ads hour` value. These percentages describe the distribution of recorded exposure timing; they are not proof that a day or hour caused more conversions.

### 4. Conversion patterns by day and hour

Within the ad group, it compares observed conversion rates across days and hours. These are exploratory comparisons and are not separate controlled experiments.

### 5. Relationship between ad frequency and conversion

The notebook calculates a correlation between `total ads` and the binary `converted` field, and compares conversion rates across four ad-exposure quartiles. These analyses describe association, not causation.

## A/B Testing Methodology

### Treatment and control groups

- **Treatment group (`ad`):** Users shown the advertising campaign.
- **Control group (`psa`):** Users shown a public service announcement instead.

### Hypotheses

- **Null hypothesis (H₀):** The groups have equal conversion rates: \(p_{ad} = p_{psa}\).
- **Alternative hypothesis (H₁):** The ad group has a higher conversion rate: \(p_{ad} > p_{psa}\).

The notebook uses a **one-sided two-proportion z-test** with a significance level of **α = 0.05**. Since conversion is binary and the comparison is between two groups, the test evaluates whether the observed difference provides evidence that the ad group's conversion rate is higher.

### Effect size

- **Absolute uplift:** Ad conversion rate minus PSA conversion rate, reported in percentage points.
- **Relative lift:** The absolute difference divided by the PSA conversion rate, expressed as a percentage.

Both measures are reported because a relative percentage alone can make an improvement appear larger without showing the underlying percentage-point difference.

### Confidence bound

The notebook estimates a **95% one-sided lower confidence bound** for the difference \(p_{ad} - p_{psa}\). This is a lower bound, not a two-sided 95% confidence interval.

## Key Results

The following results are taken from the notebook's balanced analytical sample and its saved outputs.

| Metric | Result |
|---|---:|
| Users in ad sample | 23,524 |
| Users in PSA sample | 23,524 |
| Ad conversions | 637 |
| PSA conversions | 420 |
| Ad conversion rate | 2.7079% |
| PSA conversion rate | 1.7854% |
| Absolute uplift | 0.9225 percentage points |
| Relative lift | 51.67% |
| Z-statistic | 6.7508 |
| One-sided p-value | 7.351 × 10⁻¹² |
| 95% one-sided lower bound for uplift | 0.6978 percentage points |
| Decision at α = 0.05 | Reject H₀ |

### Interpretation

The ad group’s observed conversion rate was **2.7079%**, compared with **1.7854%** for the PSA group. The difference was **0.9225 percentage points**, equivalent to a **51.67% relative lift** over the PSA baseline.

The one-sided z-test returned a p-value below 0.05, so the notebook rejects the null hypothesis. Under the test's assumptions, the result provides strong statistical evidence that the ad group's conversion rate was higher than the PSA group's in the balanced sample.

Statistical significance does not, on its own, quantify revenue impact or prove that the same lift will occur in every future campaign.

## Additional Exploratory Findings

### Ad exposure

In the original data, the mean number of ads seen was **24.8234** for the ad group and **24.7611** for the PSA group. The medians were 13 and 12, respectively. The ad group's maximum recorded exposure was 2,065, compared with 907 in the PSA group.

### Day and hour patterns

- Friday had the largest share of users by `most ads day` at **15.7470%**.
- Monday had the highest observed ad-group conversion rate by day at **3.3241%**.
- Among the hours shown in the notebook's output, hour 16 had the highest observed ad-group conversion rate at **3.0893%**.
- The correlation between `total ads` and `converted` was **0.2174**.
- Conversion rates by exposure quartile were **0.2343% (Q1)**, **0.5180% (Q2)**, **1.1887% (Q3)**, and **8.2387% (Q4)**.

These are descriptive findings from the notebook, not proof that a particular day, hour, or higher ad frequency caused the conversion difference. The exposure-quartile pattern may also reflect differences between users who receive different amounts of exposure.

## Business Recommendations

1. **Use the A/B test result as evidence, not a guarantee.** The sampled comparison supports the conclusion that the ad group converted at a higher rate than the PSA group.
2. **Report absolute and relative improvement together.** Communicate the 0.9225 percentage-point uplift alongside the 51.67% relative lift.
3. **Test timing before changing campaign schedules.** Use the observed day/hour patterns to design a follow-up controlled experiment rather than assuming they identify the best schedule.
4. **Test ad frequency experimentally.** Run a controlled test with defined exposure levels to assess whether increasing ad frequency causes an improvement.
5. **Collect revenue or order-value data.** Conversion data alone cannot quantify revenue impact, profit, or return on advertising spend.

## Limitations

- The main reported A/B test uses a balanced sample created by randomly sampling the ad group, rather than using every user in the original dataset.
- The test result is based on the sample and the assumptions of the two-proportion z-test.
- Day/hour comparisons, correlations, and exposure quartiles are exploratory and do not establish causation.
- Users who see more ads may differ in engagement or other ways; the association between ad frequency and conversion should not be interpreted as a causal effect.
- The dataset does not contain revenue or order values, so revenue impact and return on ad spend cannot be calculated.
- The supplied `marketing_campaign.csv` file does not match the data used in the notebook; the correct A/B testing dataset is required to reproduce the analysis.

## Project Structure

A simple structure for this Python-only project is:

```text
marketing-ab-testing/
│
├── dataset/
│   └── marketing_AB.csv
│
├── notebooks/
│   └── Marketing_AB_Testing_Analysis.ipynb
│
└── README.md
```

## How to Run the Project

### 1. Clone the repository

```bash
git clone <repository-url>
cd marketing-ab-testing
```

### 2. Install the required libraries

```bash
pip install pandas numpy scipy matplotlib 
```

### 3. Add the correct dataset

Place the A/B testing dataset (`marketing_AB.csv`) in the `dataset/` folder, or in the location referenced by the notebook. Update the notebook's loading path if needed. The current notebook includes Kaggle download and local Windows path cells; for a portable GitHub project, use a relative path to the CSV instead.

### 4. Open the notebook

```bash
jupyter notebook
```

Open `notebooks/Marketing_AB_Testing_Analysis.ipynb` and run the cells in order.

## Contact

**Rohit Das**

- **Email:** rohit.41.das@gmail.com
- **LinkedIn:** https://www.linkedin.com/in/rohit-das-jsr/
