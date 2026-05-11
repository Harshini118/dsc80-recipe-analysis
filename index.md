<style>
  .summary-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 1rem;
    margin: 1.5rem 0;
  }

  .summary-card {
    border: 1px solid #d8dee4;
    border-radius: 8px;
    padding: 1rem;
    background: #f6f8fa;
  }

  .summary-card strong {
    display: block;
    font-size: 0.85rem;
    color: #57606a;
    margin-bottom: 0.35rem;
  }

  iframe {
    width: 100%;
    max-width: 900px;
    height: 520px;
    border: 1px solid #d8dee4;
    border-radius: 8px;
    background: white;
  }
</style>

# What Makes a Recipe Highly Rated?

**Authors:** Harshini Kanakala and Jiya Sreejesh  
**Dataset:** Food.com Recipes and Ratings  
**Main question:** Do recipes with more ingredients receive different ratings than simpler recipes?

<div class="summary-grid">
  <div class="summary-card">
    <strong>Recipes</strong>
    83,782
  </div>
  <div class="summary-card">
    <strong>User Interactions</strong>
    731,927
  </div>
  <div class="summary-card">
    <strong>Model Target</strong>
    Average recipe rating
  </div>
  <div class="summary-card">
    <strong>Final Takeaway</strong>
    Complexity is not a strong rating driver
  </div>
</div>

## Introduction

When choosing a recipe, users often rely heavily on ratings to decide whether
something is worth making. However, it is not always clear what factors actually
influence these ratings. Do more complex recipes receive better ratings, or are
simpler recipes just as successful?

In this project, we analyze a dataset of recipes and user ratings from Food.com
to understand what influences a recipe's average rating. Specifically, we
investigate whether recipe complexity, measured by the number of ingredients,
affects how users rate recipes.

We focus on the following key features:

- `minutes`: preparation time
- `n_ingredients`: number of ingredients
- `n_steps`: number of steps
- `avg_rating`: average user rating per recipe

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

We merged the recipes and interactions datasets using recipe IDs. Since a rating
of 0 indicates that a user did not provide a rating, we replaced all zero ratings
with missing values (`NaN`).

We then computed the average rating per recipe, which serves as the main
variable throughout our analysis.

After cleaning:

- The dataset contains **83,782 recipes**
- The `avg_rating` column contains **2,609 missing values**, or about **3.1%**

These missing values correspond to recipes that have not received any ratings,
which is important for our missingness analysis.

### Distribution of Ratings

<iframe src="assets/rating_dist.html" title="Distribution of recipe ratings"></iframe>

The distribution of ratings is highly concentrated between **4 and 5**,
indicating that most recipes are rated positively. This creates a left-skewed
distribution, where lower ratings are relatively rare.

This pattern suggests that user ratings may be biased toward positive
experiences, which can make it harder to detect meaningful differences between
groups.

### Ingredients vs. Ratings

<iframe src="assets/ingredients_scatter.html" title="Ingredients versus ratings scatter plot"></iframe>

The scatter plot shows that ratings remain consistently high across all values
of `n_ingredients`. There is no visible upward or downward trend, indicating
little to no correlation between ingredient count and rating.

Even recipes with very few ingredients can achieve ratings near 5, while more
complex recipes do not consistently outperform simpler ones. The high density of
points near rating = 5 further reinforces that ratings are clustered regardless
of complexity.

### Cooking Time vs. Ratings

<iframe src="assets/time_scatter.html" title="Cooking time versus ratings scatter plot"></iframe>

Most recipes cluster at lower cooking times, with very few extremely long
recipes. Despite this variation in cooking time, ratings remain consistently
high.

There is no clear relationship between cooking time and rating. This suggests
that longer or more time-intensive recipes do not necessarily lead to better
user experiences.

### Aggregate Trends

<iframe src="assets/aggregate_plot.html" title="Average rating by ingredient count group"></iframe>

We grouped recipes by ingredient count and computed average ratings:

| Ingredient group | Average rating | Number of recipes |
| --- | ---: | ---: |
| 0-5 ingredients | 4.65 | 13,719 |
| 6-10 ingredients | 4.62 | 40,373 |
| 11-15 ingredients | 4.62 | 22,103 |
| 16-20 ingredients | 4.63 | 4,333 |
| 20+ ingredients | 4.70 | 645 |

While recipes with 20+ ingredients have the highest average rating, this group
contains significantly fewer recipes, making it less reliable. Overall, the
differences between groups are extremely small, within about 0.08 rating points,
suggesting that ingredient count has minimal practical impact on ratings.

## Assessment of Missingness

The primary missing values occur in the `avg_rating` column, representing
recipes that have not received ratings.

We conducted a permutation test to determine whether missingness depends on the
number of ingredients.

- **P-value = 0.001**

Since the p-value is less than 0.05, we reject the null hypothesis.

**Conclusion:** Missingness does depend on the number of ingredients, meaning
that recipes with different levels of complexity are not equally likely to
receive ratings.

<iframe src="assets/missingness_plot.html" title="Missingness by ingredient count"></iframe>

This suggests that the missingness is not completely random. A plausible
explanation is that more complex recipes may receive fewer ratings due to higher
effort or lower accessibility, while simpler recipes may be more frequently
attempted and rated.

It is also possible that missingness is MNAR, since user engagement, which is
unobserved, likely influences whether a recipe receives ratings.

## Hypothesis Testing

We tested whether recipes with more than 10 ingredients have different rating
distributions compared to simpler recipes.

- **Null hypothesis:** The distributions are the same
- **Alternative hypothesis:** The distributions are different
- **Test statistic:** Kolmogorov-Smirnov (KS)

**Results:**

- **KS statistic = 0.0057**
- **P-value = 0.5912**

Since the p-value is greater than 0.05, we fail to reject the null hypothesis.

**Conclusion:** There is no statistically significant difference in rating
distributions between simple and complex recipes. This reinforces our earlier
finding that ingredient count does not meaningfully affect user ratings.

## Framing a Prediction Problem

We aim to predict `avg_rating`, making this a regression problem.

At the time of prediction, we would know:

- `minutes`
- `n_ingredients`
- `n_steps`

We evaluate performance using Root Mean Squared Error (RMSE), which penalizes
large errors and is interpretable in rating units.

## Baseline Model

We trained a linear regression model using:

- `minutes`
- `n_ingredients`

Both features were standardized.

**Performance:**

- **Test RMSE = 0.636**

This means predictions are off by about 0.64 rating points on average, which is
relatively large given that ratings range from 1 to 5.

## Final Model

We improved the model by adding:

- `n_steps`
- `steps_per_minute`, which captures recipe complexity

We also used GridSearchCV to tune hyperparameters.

**Performance:**

- **Final RMSE = 0.631**
- **Improvement = about 0.005**

While the improvement is small, it shows that incorporating additional
structural features provides marginal gains. This limited improvement suggests
that ratings are inherently difficult to predict due to their subjective nature.

## Fairness Analysis

We evaluated model performance across:

- **Simple recipes:** 10 or fewer ingredients
- **Complex recipes:** More than 10 ingredients

**Results:**

- **RMSE for simple recipes = 0.638**
- **RMSE for complex recipes = 0.617**
- **P-value = 0.562**

Since the p-value is greater than 0.05, we fail to reject the null hypothesis.

**Conclusion:** There is no significant difference in model performance,
indicating that the model is fair across recipe complexity groups.

## Conclusion

Our analysis shows that recipe ratings are not strongly influenced by simple
measures of complexity, such as ingredient count or cooking time.

Although recipes with more ingredients may appear slightly higher rated in
aggregate, these differences are extremely small and not statistically
significant. The hypothesis test further confirms that rating distributions are
nearly identical across groups.

From a modeling perspective, predicting ratings remains challenging. Even with
additional features, improvements in RMSE are minimal, suggesting that ratings
are driven by factors not captured in the dataset, such as taste, presentation,
or individual user preferences.

Overall, this project highlights that while recipe complexity may influence user
perception slightly, it is not a primary driver of ratings. More nuanced factors
likely play a larger role.
