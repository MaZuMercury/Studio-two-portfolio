---
layout: default
title: Premier League Goal Difference Prediction
---

# ⚽ Predicting Premier League Goal Difference

## Problem Definition

### Research Question

**Can transfer spending and previous-season team performance predict a club's goal difference in the following Premier League season?**

This project examines whether transfer expenditure and previous-season performance can be used to predict how well a Premier League club will perform in the following season. The target variable is **Target Goal Difference**, calculated as goals scored minus goals conceded during the following season.

This is a **regression problem** because the target is a continuous numerical value rather than a category. A positive goal difference indicates that a club scored more goals than it conceded, while a negative goal difference indicates the opposite.

This type of model could be useful to sports analysts, clubs, coaches, and researchers who want to understand which factors provide useful information about future team performance. However, the model is intended as an analytical tool rather than a standalone system for making real-world decisions.

## Background and Context

## Background and Context

Transfer spending is one of the most visible ways that professional soccer clubs attempt to improve their squads. Premier League clubs spend substantial amounts of money acquiring players, but spending more does not automatically guarantee better results. This makes transfer expenditure an interesting variable to examine when trying to understand and predict future team performance.

Agha et al. (2024) examined different types of investment in football clubs and their relationship with subsequent sporting performance. Their work provides support for considering previous investment as a potential predictor of future performance. This is particularly relevant to this project because the model uses previous-season transfer expenditure rather than transfer spending from the same season being predicted.

Transfer spending is also particularly relevant in the Premier League because clubs have substantial differences in their financial resources. Burdekin and Franklin (2015) examined transfer spending in the English Premier League and discussed the differences between clubs with greater and more limited financial resources. Their research provides context for investigating whether differences in transfer expenditure are associated with differences in on-field performance.

To measure team performance, this project uses **goal difference** as the target variable. Goal difference represents the difference between goals scored and goals conceded and provides information about how strongly a team performed over the course of a season. McHale and Scarf (2020) examined methods for evaluating English Premier League team performance and provide an academic basis for using goal difference as an important measure of team performance.

Together, these studies provide the foundation for the research question. Previous research suggests that investment can be related to subsequent football performance, transfer spending is an important feature of the competitive environment in the Premier League, and goal difference provides a meaningful measure of team performance. This project builds on those ideas by testing whether **previous transfer expenditure and previous-season performance can predict a club's goal difference in the following Premier League season**.


## Data Description

The project combines Premier League match data from **Football-Data.co.uk** with transfer expenditure data from **Transfermarkt**. The data covers the 2019–20 through 2024–25 Premier League seasons.

Football-Data.co.uk provides individual match results. Each match represents one observation in the original match-level dataset. These results were aggregated by club and season to calculate:

* League points
* Goals scored
* Goals conceded
* Goal difference

Transfermarkt provides club-level transfer expenditure for each Premier League season.

The original datasets contain 120 club-season observations for the six seasons, representing 20 Premier League clubs per season. To create the prediction dataset, each club's previous-season information was matched with its performance during the following season.

After creating these chronological predictor-target pairs, the modeling dataset contained **85 observations**, representing 17 clubs across five target seasons. The reduction from 120 observations occurs because a club must have information from both a previous season and a following season in order to be included in the prediction dataset.

The target variable is:

**Target_Goal_Difference** — the club's goal difference during the following Premier League season.

The final predictor variables were:

* **Transfer_Expenditure** — previous-season transfer expenditure, measured in millions of euros.
* **Previous_League_Points** — league points earned during the previous season.
* **Previous_Goal_Difference** — goal difference from the previous season.

There were no missing values in the variables used for the final modeling dataset.

### Data Sources

* [Football-Data.co.uk](https://www.football-data.co.uk/)
* [Transfermarkt](https://www.transfermarkt.com/)

## Data Understanding and Exploration

Before modeling, I examined the relationships between the available variables and the target variable using correlation analysis and a correlation heatmap.

Previous-season performance variables showed stronger relationships with future goal difference than transfer expenditure. The correlations with the target were:

* Previous Goal Difference: **0.693**
* Previous League Points: **0.650**
* Transfer Expenditure: **0.348**

These results suggest that previous-season performance provides more information about future goal difference than transfer expenditure in this dataset.

Transfer expenditure still showed a positive relationship with future goal difference, but the relationship was considerably weaker than the relationships involving previous performance.

The predictor variables also showed strong relationships with one another. In particular, Previous League Points and Previous Goal Difference had a correlation of approximately **0.953**. This indicated that the two variables contain substantial overlapping information.

Variance Inflation Factor (VIF) analysis was used to investigate this multicollinearity. After removing other redundant performance variables, Previous League Points and Previous Goal Difference still had VIF values of approximately **10.85** and **10.90**, respectively.

I chose to retain both variables because they represent related but different aspects of previous-season performance. League points measure results using the Premier League scoring system, while goal difference measures the difference between goals scored and goals conceded. However, the high VIF values mean that individual Linear Regression coefficients for these variables should be interpreted cautiously.

## Data Preparation and Feature Selection

The original match-level data was transformed into club-season observations. For each club and season, I calculated league points, goals scored, goals conceded, and goal difference.

The transfer data required additional cleaning because club names were not always formatted consistently between the data sources. Club names were standardized so that the two datasets could be merged correctly.

The data was then organized chronologically. For each club, information from one season was used as the predictor information for the following season's goal difference.

For example, information from the 2023–24 season was used to predict a club's goal difference during the 2024–25 season.

The final three features were selected based on their relevance to the research question and their relationship with the target:

1. Transfer Expenditure
2. Previous League Points
3. Previous Goal Difference

Other variables, such as previous goals scored and goals conceded, were excluded from the final model because they were highly correlated with the selected performance variables and would add additional multicollinearity.

No categorical encoding or feature scaling was necessary because all final predictors were numerical.

### Training and Testing Strategy

The data was divided chronologically rather than randomly. The target seasons from **2020–21 through 2023–24** were used for training, while **2024–25** was held out as the test set.

This resulted in:

* **68 training observations**
* **17 test observations**

A chronological split was selected because the goal is to predict future performance. Randomly mixing observations from different seasons could allow information from later seasons to influence the training process and would not reflect how the model would actually be used.

Using the 2024–25 season as a held-out test set also provides a more realistic evaluation of how the model performs on a future season.

## Baseline and Model Development

A **Mean Baseline** was established before training the machine-learning models. The baseline predicts the same value for every test observation using the average target goal difference from the training data.

The baseline prediction was approximately **5.47 goals**.

Two regression models were then developed:

### Linear Regression

Linear Regression was selected because it provides an interpretable way to estimate the relationship between the three predictors and future goal difference. The model produces coefficients that show the direction and estimated magnitude of each predictor's relationship with the target while holding the other predictors constant.

### Random Forest Regression

Random Forest Regression was selected as a second model because it can capture nonlinear relationships and interactions that a simple linear model may not capture. Using a different modeling approach provides a useful comparison with Linear Regression.

The Random Forest model used **200 trees** with a fixed random state of 42 to make the results reproducible.

Both models were trained using the same training data and evaluated on the same held-out 2024–25 test set.

## Model Evaluation and Selection

The models were evaluated using **Mean Absolute Error (MAE), Root Mean Squared Error (RMSE), and R²**.

**MAE** measures the average absolute difference between the predicted and actual values. Lower values indicate better performance.

**RMSE** measures the square root of the average squared prediction error. Because larger errors are squared before averaging, RMSE places more emphasis on large prediction mistakes. Lower values are better.

**R²** measures how much of the variation in the target is explained by the model relative to a baseline. Higher values indicate better performance, while a negative R² indicates that the model performs worse than simply predicting the mean of the training target.

### Model Results

| Model             |        MAE |       RMSE |        R² |
| ----------------- | ---------: | ---------: | --------: |
| Mean Baseline     |     13.211 |     16.810 |    -0.046 |
| Linear Regression | **13.730** | **15.675** | **0.090** |
| Random Forest     |     14.565 |     17.223 |    -0.098 |

Linear Regression was selected as the preferred model because it achieved the **lowest RMSE (15.675)** and the **highest R² (0.090)**.

There is an important tradeoff between the metrics. The Mean Baseline had a slightly lower MAE than Linear Regression, meaning its average absolute error was slightly smaller. However, Linear Regression performed better on RMSE and R². Because RMSE places greater emphasis on larger prediction errors and R² provides information about the amount of variation explained by the model, Linear Regression was selected as the strongest overall model.

Random Forest performed worse than both alternatives across all three metrics.

## Model Interpretation and Insights

The Linear Regression coefficients were:

| Feature                  | Coefficient |
| ------------------------ | ----------: |
| Transfer Expenditure     |       0.078 |
| Previous League Points   |       0.268 |
| Previous Goal Difference |       0.524 |

All three coefficients were positive, meaning that higher values of each predictor were associated with higher predicted future goal difference when the other variables were held constant.

The Transfer Expenditure coefficient suggests that an additional €1 million in previous-season transfer expenditure was associated with approximately **0.078 additional goals of predicted future goal difference**, holding the other predictors constant.

Previous Goal Difference had the largest coefficient, followed by Previous League Points. However, because Previous League Points and Previous Goal Difference had high VIF values, these individual coefficients should not be interpreted as completely independent effects.

The Random Forest produced a similar overall conclusion about the importance of previous performance. Its feature importance scores were:

* Previous Goal Difference: **0.394**
* Previous League Points: **0.361**
* Transfer Expenditure: **0.245**

This means that the Random Forest relied more heavily on previous-season performance variables than on transfer expenditure when making predictions.

These results provide consistent evidence across both models that previous-season performance contains more predictive information about future goal difference than transfer expenditure in this dataset.

### Prediction Error Analysis

I also examined the individual predictions made by the Linear Regression model for the 2024–25 season.

Some clubs had relatively small prediction errors, while others had much larger differences between their predicted and actual goal differences. The largest errors included Manchester City, Tottenham, Nottingham Forest, Manchester United, and Brentford.

The actual-versus-predicted plot showed that several observations were substantially separated from the ideal prediction line. The residual plot also showed that prediction errors varied considerably between clubs.

These errors suggest that the three selected predictors do not capture all of the factors that influence changes in team performance from one season to the next.

Factors such as managerial changes, injuries, player departures and arrivals, tactics, squad quality, and fixture difficulty could help explain some of these unexpected changes.

## Limitations, Ethics, and Reflection

There are several important limitations to this analysis.

First, the final modeling dataset contains only **85 club-season observations**, with just **17 observations in the held-out test season**. This is a relatively small sample for machine learning, so the results may not generalize to other Premier League seasons.

Second, transfer expenditure is an imperfect measure of squad improvement. Spending more money does not necessarily mean that a club acquired better players, and transfer expenditure does not account for player quality, wages, injuries, or how well new players fit into a team's tactics.

Third, the model does not include several factors that could have a substantial impact on future performance. These include managerial changes, injuries, squad continuity, player-level performance, tactical changes, and fixture difficulty.

There is also potential bias created by the limited time period and the focus on Premier League clubs. The results may not apply to lower divisions, other countries, or leagues with different financial structures.

The high multicollinearity between Previous League Points and Previous Goal Difference is another limitation. Although both variables were retained because they represent different measures of previous performance, their strong relationship makes it difficult to interpret their individual regression coefficients independently.

Prediction errors also have to be considered when thinking about real-world use. A club analyst using this model could incorrectly expect a team to improve or decline based on the model's prediction. Because of this, the model should be viewed as an additional analytical tool rather than a system that should independently determine recruitment, financial, or coaching decisions.

This model would be more useful as part of a larger analytical process that incorporates additional information about players, teams, and the circumstances surrounding each season.

### Future Improvements

Future versions of the project could improve the model by incorporating:

* Player-level performance statistics
* Expected goals (xG)
* Injuries and player availability
* Managerial changes
* Squad continuity
* Fixture difficulty
* Player wages
* Transfer quality rather than expenditure alone
* More Premier League seasons
* Additional regression or ensemble models
* Hyperparameter tuning and cross-validation

These additions could help the model capture factors that are not represented by transfer expenditure and previous-season performance alone.

## Conclusion

Overall, transfer expenditure showed a **positive but limited relationship** with future Premier League goal difference. Previous-season performance had stronger relationships with future performance and was more influential in both machine-learning models.

Linear Regression was selected as the preferred model because it produced the lowest RMSE and highest R² on the held-out 2024–25 test season. However, the model's R² of 0.090 shows that the three selected predictors explain only a small portion of the variation in future goal difference.

The results therefore suggest that transfer spending can provide some predictive information, but **previous-season performance is more useful for predicting future goal difference within this dataset and modeling approach**.

Most importantly, these results should not be interpreted as evidence that transfer spending directly causes better performance. The analysis identifies predictive relationships and associations, not causal effects.

## References

Agha, N., Nowland, J., & Sankara, J. (2024). New players? New managers? New stadiums? Which investments drive football club performance? *Sport, Business and Management: An International Journal, 14*(4), 540–556.

Burdekin, R. C. K., & Franklin, M. (2015). Transfer spending in the English Premier League: The haves and the have nots. *Applied Economics Letters, 22*(11), 897–902.

McHale, I. G., & Scarf, P. A. (2020). A CUSUM tool for retrospectively evaluating team performance: The case of the English Premier League. *International Journal of Forecasting, 36*(1), 118–128.

Football-Data.co.uk. (n.d.). *Football results, statistics and data*. https://www.football-data.co.uk/

Transfermarkt. (n.d.). *Premier League transfer data*. https://www.transfermarkt.com/

## Code and Transparency

The complete Python notebook containing the data collection, preparation, modeling, evaluation, and visualizations is available below.

**[View the Project 2 Jupyter Notebook](Project-2.ipynb)**

### AI Usage Disclosure

I used ChatGPT (GPT-5.6 Luna) to help brainstorm and refine the research question, troubleshoot Python code, organize portions of the analysis, and improve the clarity of written explanations. I reviewed, tested, and interpreted the code and results myself. The final modeling decisions, conclusions, and submitted analysis were reviewed by me.
