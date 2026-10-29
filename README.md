
# Walmart Markdown Anomaly Detection

**Hybrid Anomaly Detection Using Extra Trees, Isolation Forest, and RAG**

## 1. Project Overview

This project develops a hybrid anomaly detection framework to identify Walmart store-weeks with unusual sales patterns and department-level sales underperformance during periods when markdown information is recorded.

The system combines supervised forecasting, unsupervised anomaly detection, and historical validation to prioritize cases for investigation by an AI agent.

### Research Question

**How can we automatically flag markdown-active store-weeks where actual sales fall below expected performance?**

Markdown records provide contextual information but do not establish that markdowns caused the observed sales deviations.

## 2. Dataset and Evaluation

The project uses the **Walmart Recruiting Store Sales Forecasting dataset**, including:

- Historical weekly department sales
- Store characteristics and store types
- Markdown information (MarkDown1–MarkDown5)
- Temperature and fuel prices
- Consumer Price Index (CPI)
- Unemployment rates

### Final Evaluation Period

**July 20 – October 26, 2012**

| Dataset Component | Count |
|---|---:|
| Stores | 45 |
| Evaluated store-weeks | 675 |
| Eligible department-weeks | 42,400 |

Chronological training and evaluation are used to reduce temporal data leakage.

## 3. Models and Benchmarks

### 3.1 Supervised Forecasting

We evaluated six forecasting approaches:

1. Extra Trees Regressor
2. Random Forest Regressor
3. XGBoost
4. LightGBM
5. Ridge Regression
6. 8-Week Moving Average baseline

**Extra Trees was selected as the primary forecasting model.**

Final-test performance:

| Metric | Extra Trees |
|---|---:|
| WAPE | 7.86% |
| MAE | Approximately $1,280 |

### 3.2 Anomaly Detection Thresholds

Five department-level anomaly detection methods were evaluated:

| Method | Threshold | Flag Rate |
|---|---:|---:|
| Fixed 10% deviation | 10.00% | 26.43% |
| Historical P90 | 26.60% | 7.92% |
| Historical P95 | 38.59% | 4.07% |
| **Global MAD** | **22.36%** | **10.29%** |
| Store-Type MAD | Type-specific | 10.33% |

We selected **Global Median Absolute Deviation (MAD) with a 2× multiplier** as the primary Extra Trees department-level screening method.

Additional sensitivity experiments evaluated MAD multipliers of 2.5 and 3, as well as alternative store-week aggregation rules.

The selected threshold is provisional because verified anomaly ground-truth labels are unavailable.

### 3.3 Unsupervised Anomaly Detection

We replicated Isolation Forest using a chronological evaluation design.

The Isolation Forest identifies unusual store-week patterns, while Extra Trees determines whether department-level actual sales fall substantially below forecast expectations.

Moving Average benchmarks and store-type-specific MAD thresholds were also evaluated.

## 4. Final Hybrid Detection Framework

The proposed workflow follows five stages:

1. **Isolation Forest Screening:** Identify unusual store-week patterns.
2. **Extra Trees + Global MAD:** Identify departments with unusually negative forecast residuals.
3. **Business Eligibility Screening:** Require a positive aggregate modeled sales gap and recorded markdown information.
4. **Case Prioritization:** Rank cases using modeled dollar shortfalls among Global MAD-flagged departments.
5. **RAG Evidence Generation:** Export structured investigation records for the AI agent.

### Hybrid Evaluation Results

| Metric | Result |
|---|---:|
| Evaluated store-weeks | 675 |
| Isolation Forest flagged | 68 |
| Flagged by both models | 67 |
| Final hybrid cases | 40 |
| Stores represented | 18 |
| Type A cases | 30 |
| Type B cases | 10 |
| Type C cases | 0 |

The final 40 cases represent prioritized investigation candidates rather than confirmed business failures.

## 5. RAG Evidence Development

The structured JSON evidence was progressively improved through several development versions.

| Version | Main Improvement |
|---|---|
| V1 | Extra Trees + Global MAD department-level cases |
| V2 | Combined Extra Trees and Isolation Forest priority cases |
| V3 | Hybrid screening and selection of 40 cases |
| V3.1 | Actual sales, expected sales, historical predictions, and reconciliation validation |
| V3.2 | Recent historical, seasonal, cross-store, and external-factor comparisons |
| V3.3 | Independent diagnostic signals for recent sales decline and possible forecasting bias |

**V3.3 is the latest completed evidence version.**

The underlying 40 hybrid case selections and their priority rankings remain unchanged across V3–V3.3.

### V3.3 Evidence Fields

Each investigation case provides:

- Store, department, date, and store type
- Isolation Forest anomaly flag and score
- Extra Trees expected and actual sales
- Global MAD flags and modeled sales shortfalls
- Historical 4-week and 8-week comparisons
- Prior-year seasonal benchmarks
- Same-department cross-store comparisons
- Markdown and external economic context
- Independent forecast reliability indicators
- Investigation questions and evidence requirements

Root causes remain unverified unless independently supported by operational evidence.

## 6. Interactive Case Explorer

An interactive Google Colab dashboard supports investigation of the 40 hybrid cases.

Available features include:

- Store-week and department selection
- Actual versus expected sales charts
- Historical weekly sales trend graphs
- Recent and seasonal sales comparisons
- Forecast overprediction indicators
- Markdown and economic context
- Exportable charts, summaries, and case JSONs

The dashboard provides an analytical explanation layer without modifying model predictions or anomaly flags.

## 7. Experiment Tracking and Version Control

GitHub is used to track source code, analytical decisions, documentation, and future development changes.

### Experiment Log

The `experiment_log.csv` file documents:

- Forecasting benchmark models
- Anomaly threshold comparisons
- MAD sensitivity experiments
- Store-week aggregation experiments
- Isolation Forest and Moving Average comparisons
- Hybrid case selection
- RAG evidence version development
- Dashboard development
- Pending failure-handling work

Future changes will be recorded through descriptive Git commits.

Previous development versions are documented in the experiment log; they are not necessarily represented as separate historical Git commits.

## 8. Limitations

- Verified anomaly ground-truth labels are unavailable.
- Isolation Forest detects unusual patterns, not necessarily negative sales events.
- Extra Trees forecasts may reflect seasonal prediction errors.
- Modeled dollar shortfalls are not confirmed lost revenue.
- Markdown records exist at the store-week level and do not establish department-level promotional activity.
- No causal relationship between markdowns and sales shortfalls has been established.
- The final hybrid case list includes no Type C stores.
- Diagnostic thresholds are exploratory rather than independently validated.
- Business root causes require additional evidence.

## 9. Current Status and Next Steps

### Completed

- [x] Supervised forecasting benchmarks
- [x] Extra Trees threshold evaluation
- [x] MAD sensitivity analysis
- [x] Chronological Isolation Forest replication
- [x] Hybrid anomaly screening
- [x] Selection of 40 prioritized store-week cases
- [x] V3.3 structured RAG evidence generation
- [x] Historical and seasonal validation
- [x] Interactive case investigation dashboard
- [x] Experiment log documentation
- [x] Initial GitHub repository setup

### Remaining Work

- [ ] Implement and test failure-handling loops
- [ ] Validate AI agent responses against analytical evidence
- [ ] Evaluate potential seasonal forecasting artifacts
- [ ] Review Type C store coverage
- [ ] Finalize reproducibility documentation
- [ ] Prepare the final report and research paper

## 10. Repository Contents

```text
walmart-markdown-anomaly-detection/
├── README.md
├── experiment_log.csv
└── Walmart_IF_Replication_+_ET_Combination_final.ipynb
```

The repository contains the combined analysis notebook, experiment log, and methodology documentation.

Large datasets, generated model outputs, and RAG JSON archives are maintained separately in Google Drive.

## 11. Project Scope

This repository primarily documents the analytical components of the Walmart AI Agent project:

**Analytics Component**
- Forecasting and anomaly detection
- Model benchmarking
- Historical validation
- Case selection and prioritization
- Structured evidence preparation

**AI Agent and RAG Component**
- Evidence retrieval
- Root-cause investigation
- Evidence-grounded explanations
- Recommended business actions

The combined workflow is intended to support informed investigation of potential sales exceptions rather than automated causal conclusions.
