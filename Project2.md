---
layout: default
title: Premier League Goal Difference Prediction
---

# ⚽ Predicting Premier League Goal Difference

## 1. Problem Definition

### Research Question

**Can transfer spending and previous-season team performance predict a club's goal difference in the following Premier League season?**

The goal of this project is to determine whether transfer expenditure and previous-season performance can be used to predict how well a Premier League club will perform in terms of goal difference in the following season.

This is a **supervised regression problem** because the target variable, `Target_Goal_Difference`, is a continuous numerical value.

The model predicts a club's goal difference in the target season, calculated as:

**Goal Difference = Goals Scored − Goals Conceded**

The intended audience includes sports analysts, football researchers, clubs, and anyone interested in understanding how financial investment and previous performance relate to future team success.

---

## 2. Background and Context

Transfer spending is one of the most visible forms of investment made by professional football clubs. Clubs spend large amounts of money on players with the expectation that these additions will improve future performance. However, spending more does not necessarily guarantee better results.

Previous research has examined the relationship between investment and football performance. Agha et al. (2024) investigate how different forms of investment, including player and managerial investment, relate to subsequent football club performance. Their work provides support for examining transfer investment as a potential predictor of future performance.

Burdekin and Franklin (2015) examine transfer spending in the English Premier League and highlight differences in spending between clubs. Their research provides additional context for understanding the financial environment surrounding Premier League transfers.

Goal difference is also a useful measure of team performance because it captures both offensive and defensive results rather than only wins or points. McHale and Scarf (2020) demonstrate the usefulness of goal-based performance measures when evaluating Premier League teams.

Based on this research, this project examines whether transfer spending and previous-season performance provide useful information for predicting a club's goal difference in the following Premier League season.

---

## 3. Data Description

The project combines Premier League match results with transfer expenditure data.

### Data Sources

* **Football-Data.co.uk** — Premier League match results and match statistics
* **Transfermarkt** — transfer expenditure information

The analysis covers Premier League seasons from **2019–20 through 2024–25**.

The original dataset contained **120 club-season observations**, representing 20 clubs across each of six seasons.

The match results were aggregated to calculate:

* League points
* Goals scored
* Goals conceded
* Goal difference
* Final league position

Transfer expenditure was then combined with these performance measures using standardized club names and season information.

### Prediction Dataset

To make the prediction meaningful, predictors from one season were matched with the target performance from the **following season**.

After creating these chronological relationships and removing observations that could not be matched with both a previous and following season, the final modeling dataset contained **85 observations**.

These observations represent **17 clubs across five target seasons**, from 2020–21 through 2024–25.

### Target Variable

`Target_Goal_Difference`

This represents the club's goal difference during the following Premier League season.

### Predictor Variables

The final model uses three predictors:

* `Transfer_Expenditure`
* `Previous_League_Points`
* `Previous_Goal_Difference`

There were no missing values in the variables used for the final modeling dataset.

---

## 4. Data Understanding and Exploration

Before building the models, I examined relationships between the available variables using correlation analysis and visualizations.

The target variable had the strongest correlation with previous-season goal difference:

| Variable                 | Correlation with Target Goal Difference |
| ------------------------ | --------------------------------------: |
| Previous Goal Difference |                                   0.693 |
| Previous Goals Scored    |                                   0.678 |
| Previous League Points   |                                   0.650 |
| Transfer Expenditure     |                                   0.348 |
| Previous Goals Conceded  |                                  -0.511 |

These results suggest that previous team performance has a stronger relationship with future goal difference than transfer expenditure alone.

Transfer expenditure still showed a positive relationship with the target, but the relationship was considerably weaker.

![Correlation Heatmap](images/project2-correlation-heatmap.png)

*Figure 1. Correlation matrix showing relationships among the modeling variables.*

### Multicollinearity

I also examined correlations between the predictor variables and calculated Variance Inflation Factors (VIF).

Previous league points and previous goal difference were highly correlated:

* Previous League Points ↔ Previous Goal Difference: **0.953**

After reducing redundant variables, the remaining VIF values were:

| Predictor                |   VIF |
| ------------------------ | ----: |
| Transfer Expenditure     |  1.06 |
| Previous League Points   | 10.85 |
| Previous Goal Difference | 10.90 |

The high VIF values indicate substantial multicollinearity between previous league points and previous goal difference. I retained both variables because they represent different aspects of team performance and the project compares both a linear model and a Random Forest model.

Because of this multicollinearity, the coefficients of the linear regression model should be interpreted cautiously.

---

## 5. Data Preparation and Feature Selection

The data required several preparation steps before modeling.

First, club names were standardized so that transfer and performance datasets could be correctly matched.

Next, match-level results were aggregated into season-level team statistics. League points, goals scored, goals conceded, and goal difference were calculated for each club and season.

The datasets were then organized chronologically so that each club's previous-season information was used to predict its performance in the following season.

For example, a club's performance during the 2022–23 season was used as a predictor for its target goal difference during the 2023–24 season.

This chronological structure was important for preventing **data leakage**. Information from the target season was not used to predict that same season.

The final predictors were selected based on their relevance to the research question and the exploratory analysis.

No categorical encoding was required because the final modeling variables were numerical. Feature scaling was also not necessary for the models used in this project.

---

## 6. Baseline and Model Development

Before training machine-learning models, I created a simple baseline model that predicts the mean goal difference from the training data for every test observation.

The mean training goal difference was approximately:

**5.47**

The baseline produced:

* **MAE:** 13.211
* **RMSE:** 16.810
* **R²:** -0.046

This baseline provides a reference point for determining whether the machine-learning models provide useful predictive information.

### Linear Regression

The first machine-learning model was a **Linear Regression** model.

The model produced:

* **MAE:** 13.730
* **RMSE:** 15.675
* **R²:** 0.090

The coefficients were:

| Predictor                | Coefficient |
| ------------------------ | ----------: |
| Transfer Expenditure     |       0.078 |
| Previous League Points   |       0.268 |
| Previous Goal Difference |       0.524 |

All three coefficients were positive, indicating that higher values of these predictors were associated with higher predicted future goal difference while holding the other predictors constant.

The transfer expenditure coefficient suggests that an additional €1 million in transfer expenditure was associated with approximately a **0.078 increase in predicted goal difference**, holding the other variables constant.

This should not be interpreted as a causal effect. The model identifies an association within the dataset rather than demonstrating that spending directly causes an increase in goal difference.

### Random Forest

The second model was a **Random Forest Regressor** using 200 trees.

The Random Forest produced:

* **MAE:** 14.565
* **RMSE:** 17.223
* **R²:** -0.098

Random Forest was included because it can model nonlinear relationships and interactions between predictors without assuming a strictly linear relationship.

---

## 7. Model Evaluation and Selection

The three approaches were evaluated using Mean Absolute Error (MAE), Root Mean Squared Error (RMSE), and R².

| Model             |    MAE |       RMSE |        R² |
| ----------------- | -----: | ---------: | --------: |
| Mean Baseline     | 13.211 |     16.810 |    -0.046 |
| Linear Regression | 13.730 | **15.675** | **0.090** |
| Random Forest     | 14.565 |     17.223 |    -0.098 |

### Evaluation Metrics

**MAE** measures the average absolute difference between predicted and actual goal difference. Lower values indicate smaller average errors.

**RMSE** also measures prediction error but gives greater weight to larger errors. Lower values are better.

**R²** measures how much variation in the target variable is explained by the model. Higher values are better, with a value of 0 indicating performance comparable to predicting the mean.

### Model Selection

I selected **Linear Regression** as the strongest overall model.

It produced the lowest RMSE and the highest R² among the three approaches. However, the mean baseline had a slightly lower MAE than Linear Regression.

This means that Linear Regression did not outperform the baseline on every metric, but it provided the strongest overall combination of predictive performance based on RMSE and R².

The relatively low R² also indicates that the models explain only a limited amount of the variation in future goal difference.

![Actual vs. Predicted Goal Difference](images/project2-actual-vs-predicted.png)

*Figure 2. Actual versus predicted goal difference for the Linear Regression model.*

---

## 8. Model Interpretation and Insights

The results suggest that **previous-season team performance was more informative than transfer expenditure** when predicting future goal difference.

In the Linear Regression model, previous goal difference had the largest coefficient:

**Previous Goal Difference: 0.524**

Transfer expenditure had a smaller coefficient:

**Transfer Expenditure: 0.078**

The Random Forest feature importance results showed a similar pattern:

| Feature                  | Importance |
| ------------------------ | ---------: |
| Previous Goal Difference |      0.394 |
| Previous League Points   |      0.361 |
| Transfer Expenditure     |      0.245 |

This provides consistent evidence across the two models that previous team performance contained more predictive information than transfer expenditure.

However, transfer spending still contributed information to the models. Its positive relationship with future goal difference suggests that financial investment may be related to future performance, but the relationship is not strong enough in this dataset to make transfer expenditure a highly accurate standalone predictor.

![Random Forest Feature Importance](images/project2-feature-importance.png)

*Figure 3. Random Forest feature importance for the three predictors.*

### Prediction Error Analysis

I also examined individual predictions from the Linear Regression model.

Some of the largest prediction errors occurred for clubs such as:

* Manchester City
* Tottenham
* Nottingham Forest
* Manchester United
* Brentford

For example, Manchester City's actual goal difference was **+28**, while the model predicted approximately **+55.35**.

Nottingham Forest had an actual goal difference of **+12**, while the model predicted approximately **-11.22**.

These errors demonstrate that previous performance and transfer spending cannot fully capture the factors that influence a team's performance from one season to the next.

Factors such as injuries, manager changes, tactical changes, squad composition, player development, and fixture difficulty could contribute to these unexpected results.

---

## 9. Limitations, Ethics, and Reflection

There are several limitations to this analysis.

### Small Sample Size

The final modeling dataset contains only **85 observations**. This is a relatively small sample for machine-learning applications and limits how confidently the results can be generalized.

### Limited Predictors

The models only use transfer expenditure and previous-season performance. Football performance depends on many additional factors, including:

* Injuries
* Managerial changes
* Player quality
* Squad depth
* Tactical systems
* Fixture difficulty
* Player development
* Squad continuity
* Transfer quality rather than transfer cost

These omitted variables may explain some of the prediction errors.

### Transfer Spending Does Not Equal Transfer Quality

Transfer expenditure measures how much clubs spend, but it does not measure whether the players acquired were good fits for the team.

A club could spend heavily on players who underperform, while another club could make relatively inexpensive transfers that have a large positive impact.

### Multicollinearity

Previous league points and previous goal difference were highly correlated. This makes individual Linear Regression coefficients more difficult to interpret because the predictors contain overlapping information.

### Ethical Considerations

This type of model could potentially be used by clubs or analysts to support decisions about transfers and team planning. However, the model should not be treated as a definitive decision-making tool.

Incorrect predictions could lead analysts to overestimate or underestimate a team's expected performance. Because the model has limited predictive power, its results should be combined with domain knowledge and additional information rather than being used on their own.

### Future Improvements

A stronger version of this project could include additional seasons and more detailed variables such as:

* Expected goals (xG)
* Player-level performance
* Squad age
* Injuries
* Manager changes
* Player wages
* Transfer quality
* Squad continuity
* Fixture difficulty

Cross-validation and hyperparameter tuning could also be used to evaluate whether the models generalize better to unseen data.

---

## 10. Code and Transparency

The complete Python analysis and modeling process is available in the project notebook.

[View the Project 2 Notebook](https://github.com/MaZuMercury/Studio-two-portfolio/blob/main/Project-2.ipynb)

The notebook includes:

* Data collection
* Data cleaning
* Feature engineering
* Exploratory analysis
* Correlation and VIF analysis
* Train/test splitting
* Baseline modeling
* Linear Regression
* Random Forest
* Model evaluation
* Feature importance
* Prediction error analysis

### Data Sources

* Football-Data.co.uk — Premier League match results
* Transfermarkt — transfer expenditure data

### AI Transparency

Generative AI tools were used as a supporting resource during development of this project. AI assistance was used for explanations, debugging, organization, and clarification of Python and machine-learning concepts. The analysis, modeling decisions, interpretation of results, and final conclusions were reviewed and completed by me.

---

## References


Nowland, J., & Sankara, J. (2024). New players? New managers? New stadiums? Which investments drive football club performance? Sport, Business and Management, 14(4), 540–556. https://doi.org/10.1108/SBM-10-2023-0124


Burdekin, R. C. K., & Franklin, M. (2015). TRANSFER SPENDING IN THE ENGLISH PREMIER LEAGUE: THE HAVES AND THE HAVE NOTS. National Institute Economic Review, 232(232), R4–R17. https://doi.org/10.1177/002795011523200102


Beggs, C., & Bond, A. J. (2020). A CUSUM tool for retrospectively evaluating team performance: the case of the English Premier League. Sport, Business and Management, 10(3), 263–289. https://doi.org/10.1108/SBM-03-2019-0025

---

## Project Summary

This project investigated whether transfer expenditure and previous-season team performance could predict a Premier League club's goal difference in the following season.

The results indicate that previous team performance was more informative than transfer expenditure, while the overall predictive performance of the models remained limited. Linear Regression was selected as the strongest overall model based on RMSE and R², although it did not outperform the mean baseline on MAE.

Overall, the analysis suggests that **transfer spending alone is not enough to accurately predict future Premier League performance**. Previous performance provides more useful information, but additional football-specific variables would likely be necessary to build a stronger predictive model.

