# Recipe Ratings Analysis

**What makes a Food.com recipe highly rated?**

This project analyzes Food.com recipe and user-rating data to understand whether
recipe complexity affects average ratings. We focus on ingredient count, cooking
time, number of steps, missing ratings, predictive modeling, and model fairness
across simple versus complex recipes.

## Project Snapshot

| Area | Details |
| --- | --- |
| Dataset | Food.com Recipes and Ratings |
| Scale | 83,782 recipes and 731,927 user interactions |
| Main question | Do recipes with more ingredients receive different ratings than simpler recipes? |
| Methods | Data cleaning, EDA, permutation testing, KS testing, regression modeling, GridSearchCV, fairness analysis |
| Tools | Python, pandas, scikit-learn, Plotly, Jekyll/GitHub Pages |
| Authors | Harshini Kanakala and Jiya Sreejesh |

## Key Findings

- Food.com ratings are highly concentrated between 4 and 5 stars, making rating
  differences difficult to separate visually and statistically.
- Ingredient count has very little practical relationship with average recipe
  rating. Group averages differ by only about 0.08 rating points.
- Missing ratings are not completely random: recipe complexity is related to
  whether a recipe receives ratings.
- A KS test found no statistically significant difference between simple and
  complex recipe rating distributions.
- Regression models using recipe structure features only modestly predicted
  average rating, suggesting that unobserved factors such as taste, presentation,
  and user preference drive much of the variation.
- The final model did not show a significant fairness gap between simple and
  complex recipes.

## Analysis Workflow

1. **Data Cleaning**
   - Merged recipe metadata with user interactions.
   - Replaced zero ratings with missing values.
   - Computed average rating per recipe.

2. **Exploratory Data Analysis**
   - Visualized rating distribution.
   - Compared ratings against ingredient count and cooking time.
   - Aggregated ratings by recipe complexity group.

3. **Missingness Assessment**
   - Tested whether missing ratings depend on ingredient count.
   - Found evidence that missingness is related to recipe complexity.

4. **Hypothesis Testing**
   - Compared rating distributions for simple and complex recipes.
   - Used a Kolmogorov-Smirnov test.

5. **Prediction and Fairness**
   - Built a baseline linear regression model.
   - Added engineered features and tuned model settings with GridSearchCV.
   - Compared RMSE across simple and complex recipe groups.

## Repository Structure

```text
.
├── assets/
│   ├── aggregate_plot.html
│   ├── ingredients_scatter.html
│   ├── missingness_plot.html
│   ├── rating_dist.html
│   └── time_scatter.html
├── index.md
├── README.md
└── _config.yml
```

## How To View

The full written report is in [`index.md`](index.md), with interactive HTML
visualizations in the [`assets/`](assets/) folder.

To publish this fork as a website, enable GitHub Pages from:

```text
Settings -> Pages -> Deploy from branch -> main
```

Then select the root folder as the source.

## Portfolio Summary

This project demonstrates an end-to-end data science workflow: cleaning a large
real-world dataset, asking a focused statistical question, building interactive
visualizations, testing missingness and group differences, training predictive
models, and evaluating model fairness.
