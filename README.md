# Employee Attrition Risk Analyzer

An explainable AI system that predicts which employees are likely to leave, explains **why**, and recommends **what HR can do about it**.

**Live demo:** https://YOUR-LINK-HERE

![Dashboard overview](screenshots/overview.png)

## The problem

HR teams usually learn about attrition after the employee has already left, along with the hiring, training and experience cost. Most prediction projects stop at a Yes/No answer, which tells HR nothing about what to change.

## What this project does

- **Risk score (0-100)** for every employee, grouped into Low, Medium and High risk.
- **Explainable reasons** using SHAP. Reasons are split into *controllable* factors HR can change (overtime, travel, satisfaction, promotion gap) and *profile* factors it cannot (age, role, marital status).
- **Retention strategy generator** that maps each risk factor to an HR action, with a department-wise playbook.
- **Impact and cost view** estimating how much risk and replacement cost an intervention could remove.
- **Fairness audit** across age and gender, including the gaps found.
- **Live Predictor:** enter a new employee and get an instant score, reasons and recommended action.
- **What-if Simulator:** change overtime, travel, satisfaction and more for an existing employee and compare risk before and after.

## Dashboard pages

| # | Page | What it shows |
|---|------|---------------|
| 01 | Overview | Headline numbers and whether the risk score separates leavers from stayers |
| 02 | Attrition Drivers | Attrition by any factor, plus SHAP charts |
| 03 | Risk Explorer | Threshold slider with the recall and precision trade-off, and a watchlist |
| 04 | Employee Lookup | One employee's score, reasons and action |
| 05 | Retention Strategy | Department-wise factors and recommended actions |
| 06 | Impact and Cost | Estimated effect of interventions and replacement cost |
| 07 | Model and Fairness | Fairness by age and gender, sensitive-feature test, limitations |
| 08 | Live Predictor | Score a new employee |
| 09 | What-if Simulator | Before and after risk for changed attributes |

## Dataset

[IBM HR Analytics Employee Attrition and Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset): 1,470 employees, 35 attributes. About 16% left and 84% stayed, so the data is imbalanced.

## Method

1. Clean the data and drop constant columns.
2. Exploratory data analysis of attrition patterns.
3. One-hot encode categorical features and standardize numeric ones (44 model features).
4. Stratified 80:20 split, class weighting for the imbalance.
5. Compare Logistic Regression, Random Forest and XGBoost using recall, precision, F1 and ROC-AUC rather than accuracy.
6. Generate out-of-fold (5-fold) risk scores so each employee is scored by a model that did not train on them.
7. Explain predictions with SHAP and generate retention actions.
8. Export the model parameters so the dashboard can score employees in the browser.

## Model results (held-out test set, 294 employees, 47 leavers)

| Model | Recall | F1 | ROC-AUC |
|-------|--------|----|---------|
| **Logistic Regression** | **0.617** | 0.443 | **0.798** |
| Random Forest | 0.255 | 0.329 | 0.787 |
| XGBoost | 0.383 | 0.419 | 0.771 |

Logistic Regression was chosen because it caught the most leavers, had the best ROC-AUC, and is the easiest to explain. Accuracy was not used to choose a model: because 84% of employees stay, a model that predicts "stay" for everyone would already score 84%.

## Tech stack

Python, Pandas, NumPy, Scikit-learn, XGBoost, SHAP, Matplotlib, Seaborn, Jupyter Notebook. The dashboard is a single HTML file (HTML, CSS, JavaScript) with the model coefficients embedded, so it runs without a server.

## Project structure

```
employee-attrition-risk-analyzer/
├── notebooks/        # analysis and modelling notebooks, run in order
├── data/             # dataset and generated outputs
├── dashboard/        # dashboard source
├── index.html        # the dashboard (open this file)
├── screenshots/
└── README.md
```

## How to run

- **Dashboard:** open `index.html` in any modern browser. No installation needed.
- **Notebooks:** install the requirements, then run the notebooks in order.

```bash
pip install pandas numpy scikit-learn xgboost shap matplotlib seaborn jupyter
jupyter notebook
```

## Limitations

- The dataset is small and synthetic, not real company data.
- The what-if simulator and impact estimates reflect patterns the model learned. They do not prove that an HR action will cause an employee to stay.
- The fairness audit found uneven recall across age groups (about 0.89 for the best group down to 0.41 for the worst), so the model should not be used for decisions about individuals without human review.
- The live score uses the final model, while other pages use out-of-fold scores, so small differences are expected.

## Future scope

- Connect to a real HR database and retrain as new leaver outcomes arrive.
- Automated alerts for employees who cross the High-risk threshold.
- Validate on larger real-world datasets.

## Author

**Yash Gupta**, BCA Semester 5, ITM University Gwalior.
