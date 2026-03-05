# credit-risk-pd-policy-simulation

A GitHub-ready **Basel/IRB-style credit risk policy simulation** project.

This repository scores applicants using:
- **Proxy PD (`prob_def`)** (because the included sample dataset has *no observed default outcome*)
- **Policy cutoff** -> `loan_approval` (1=approve, 0=decline)
- **EAD/LGD/EL** proxies for expected loss reporting

> If you have a real dataset with an outcome (e.g., `default_flag`), use the supervised template notebook and extend the module to train/validate a true PD model.

## Why “Proxy PD”? (Important)
The bundled dataset `data/Bank_Loan_Approval.csv` contains applicant features but **no historical performance outcome**
(no `default_flag`, `bad_flag`, `Personal.Loan`, etc.). Without an outcome, you cannot do dev/val/test splits, AUC/KS, calibration, or backtesting.
This repo therefore provides an **explainable proxy PD** calibrated to a target mean PD (default 5%) and a realistic decisioning layer.

## Project Structure
```
credit-risk-pd-policy-sim/
├─ data/
├─ notebooks/
├─ src/irb_simulator/
├─ scripts/
├─ outputs/
├─ tests/
├─ LICENSE
├─ .gitignore
├─ requirements.txt
└─ pyproject.toml
```

## Quickstart

### Install
```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
```

### Score the dataset
```bash
python scripts/score_dataset.py --infile data/Bank_Loan_Approval.csv --outfile outputs/scored.csv --pd-cutoff 0.05
```

### Make a report
```bash
python scripts/make_report.py --scored outputs/scored.csv --out outputs/report.md
```

## IRB components implemented (proxies)
- **PD**: proxy PD (scorecard-style, calibrated mean)
- **EAD**: 30% of annual income (approved-only exposure)
- **LGD**: 60% base; 40% if CD/Securities; small mortgage uplift (bounded)
- **EL**: PD × EAD × LGD

## Upgrade path (true IRB PD model)
Add an outcome column (e.g., `default_flag`) and run `notebooks/02_supervised_pd_template.ipynb` to do:
- dev/val/test split
- AUC/ROC
- calibration/backtesting (extend as needed)

## License
MIT License (see LICENSE).
