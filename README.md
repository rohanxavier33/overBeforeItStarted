# Over Before It Started: Predicting Chess Outcomes from Metadata

This project investigates how accurately a chess game's winner can be predicted using only pre-game metadata, without analyzing the board state or move sequences. The central finding is that while machine learning models can achieve ~65% accuracy, they offer only a marginal improvement over a simple baseline heuristic: **predicting the higher-rated player will win**. This demonstrates that the players' rating difference is the overwhelmingly dominant predictive feature.

The analysis started as a multi-class problem (White win, Black win, Draw) but pivoted to a binary classification task (White win vs. Black win) due to the severe class imbalance of 'draw' outcomes (~4.5% of the data).

---

## 📋 Table of Contents
* [Key Findings](#key-findings)
* [Dataset](#dataset)
* [Methodology](#methodology)
* [Model Performance](#model-performance)
* [How to Run](#how-to-run)
* [Project Structure](#project-structure)
* [Dependencies](#dependencies)

---

## 🎯 Key Findings

* **Dominance of Rating Difference**: The single most predictive feature is the difference in Elo ratings between the two players. A simple baseline model that predicts the higher-rated player will win achieves **64.3% accuracy**.
* **Marginal ML Improvement**: The best-performing machine learning model, a tuned Random Forest, achieved **65.3% accuracy**. This is only a 1% improvement over the simple baseline.
* **Limited Value of Other Metadata**: Features like opening choice (`opening_eco`), time controls (`increment_code`), and opening depth (`opening_ply`) provided very little additional predictive power for the win/loss outcome.

---

## 💾 Dataset

This project uses the **Chess Game Dataset** from Lichess, collected by Mitchell J. It contains metadata for over 20,000 chess games.

* **Source**: [Mitchell J, 2017. Chess Game Dataset (Lichess) 1.0 on Kaggle](https://www.kaggle.com/datasets/datasnaek/chess)

The dataset (`games.csv`) should be placed in a `data/` directory.

---

## 🛠️ Methodology

The analysis is documented in the `main.ipynb` notebook and follows these key steps:

1.  **Data Cleaning**:
    * Dropped irrelevant columns like IDs, timestamps, and post-game info (`victory_status`).
    * Filtered out all games that resulted in a 'draw' to create a binary classification problem.

2.  **Feature Engineering**:
    * Created `rating_difference` by subtracting Black's rating from White's.
    * Engineered `base_time_seconds` and `increment_seconds` from the `increment_code` column.
    * Grouped rare chess openings (`opening_eco`) into a single 'Other' category to reduce dimensionality.

3.  **Modeling**:
    * Established a **Baseline Model** (higher rating wins).
    * Trained and evaluated five classification models:
        * Logistic Regression
        * Random Forest (with `GridSearchCV` for hyperparameter tuning)
        * XGBoost
        * LightGBM
    * Used `StandardScaler` within pipelines to prepare data for the models.

---

## 📊 Model Performance

The final tuned Random Forest model performed the best, but only slightly better than the baseline. This reinforces the conclusion that rating difference is the primary driver of the outcome.

| Model | Accuracy | F1-Score (Macro) |
| :--- | :---: | :---: |
| **Baseline (Higher Rating Wins)** | 64.3% | 0.64 |
| Logistic Regression | 64.4% | 0.64 |
| **Tuned Random Forest** | **65.3%** | **0.65** |
| XGBoost | 64.4% | 0.64 |
| LightGBM | 64.2% | 0.64 |
