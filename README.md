# CallMeMaybe — Telecom Operator Efficiency Analysis

Analysis of a telecom call center (August–November 2019) to identify ineffective operators using a composite inefficiency score, then validate the findings with statistical hypothesis tests.

TripleTen Data Analyst program project — Mara Torres Blanco.

## What this project does

CallMeMaybe is a virtual telecom operator. With **43.7% of calls missed**, the business needed to know which operators were underperforming and whether the difference was real. This project:

1. Explores the call data (EDA) and builds per-operator metrics.
2. Builds a **weighted composite inefficiency score** (missed-call rate, average wait time, outbound volume) and flags the worst 25% of operators.
3. Tests the differences between effective and ineffective operators with the **Mann-Whitney U test** (α = 0.05).

## Tools

- Python
- Pandas, NumPy
- Matplotlib, Seaborn
- SciPy (Mann-Whitney U)
- scikit-learn (MinMaxScaler)

## Data

- `telecom_dataset_us.csv` — **53,902 call records**, August–November 2019
- `telecom_clients_us.csv` — **732 client records**, 3 rate plans (A, B, C)
- **1,092 operators**, 290 clients
- 8,172 records (15.2%) have no operator assigned (system/routing calls) and were excluded from the per-operator analysis.

The datasets are included in this repository (`telecom_dataset_us.csv` and `telecom_clients_us.csv`), so the notebook runs as delivered.

## Key findings

### Exploratory analysis

- **43.7% of all calls are missed** — a systemically high figure, not just an individual-operator problem.
- Calls are mostly **outgoing (59.1%)** and **external (86.5%)**; internal calls are 13.5%.
- Median call duration is **165 seconds**; median wait time for incoming calls is **34 seconds** (mean 290s — heavily skewed, with waits over 46,000 seconds, likely abandoned queued calls).

### Ineffective operators

The composite score weights the missed-call rate at 40%, average wait time at 35%, and (inverted) outbound volume at 25%. Operators at or above the **75th percentile (score ≥ 0.263)** are flagged as ineffective.

- **273 operators (25%)** flagged as ineffective.
- The 3 most critical cases: operator **948756** misses 100% of incoming calls; operators **919794** and **906070** have average waits of **1,040s** and **853s** (~14–17 minutes).
- Missed-call rate and wait time are the best predictors of inefficiency; outbound volume alone is not.

### Hypothesis tests (Mann-Whitney U, α = 0.05)

| Hypothesis | Result |
|---|---|
| H1: Ineffective operators have a higher missed-call rate | Rejected H₀ (p ≈ 4.15e-92) |
| H2: Ineffective operators have a higher wait time | Rejected H₀ (p ≈ 1.14e-100) |
| H3: Effective operators make more outbound calls | Not rejected (p = 0.999) |

Outbound call volume by itself does not distinguish effective from ineffective operators — the outbound **success rate** would be a better metric.

### Recommendations

- Prioritize a review of the top 20 operators (score > 0.35) with their supervisors.
- Add the outbound success rate as a complementary metric.
- Review call routing for operators with wait times above 600s.
- Set automatic alert thresholds: missed-call rate > 30% or wait time > 120s.

## Project structure

```
callmemaybe-telecom-operator-analysis/
├── callmemaybe_operator_analysis.ipynb   # Full analysis (EDA, scoring, hypothesis tests)
├── callmemaybe_operator_analysis.pdf     # Written report with charts and recommendations (in Spanish)
├── README.md
└── .gitignore
```

## How to run it

Open `callmemaybe_operator_analysis.ipynb` in Jupyter Notebook or JupyterLab and run the cells top to bottom. It expects `telecom_dataset_us.csv` and `telecom_clients_us.csv` in the same folder (both included).
