<style>
iframe {
  margin-bottom: 10px;
}
</style>

# Do "Healthy" Tagged Recipes Contain Significantly Lower Proportions of Saturated Fat and Sugar Compared to Untagged Recipes?

Authors: Katrina Suherman & Rheka Narwastu

## Overview
This data science project, conducted at UCSD, focuses on investigating whether recipes tagged as “healthy” tend to have a significantly lower proportion of saturated fat or sugar compared to recipes without this tag. 

## Introduction


This project uses the `RAW_recipes` and `RAW_interactions` datasets, which provide detailed information about thousands of recipes and user interactions on [food.com.](https://www.food.com) The `RAW_recipes` dataset includes attributes such as number of ingredients, steps, nutritional values, tags like "healthy, " and more. Meanwhile, the `RAW_interactions` dataset offers insights into user reviews and ratings. Together, these datasets enable us to explore whether recipes tagged as “healthy” truly reflect better nutritional quality. Our central question is: **Do recipes tagged as “healthy” tend to have significantly lower proportions of saturated fat or sugar compared to recipes without this tag?** 

This question matters because tags like "healthy" are often used to guide dietary decisions, yet their reliability is rarely scrutinized. With increasing focus on health-conscious eating, understanding whether these tags align with nutritional standards empowers individuals to make informed food choices and encourages transparency in recipe labeling. By examining this question, we aim to provide insights for health-conscious individuals, nutritionists, and recipe platforms, fostering better accountability and informed decisions in the culinary space.

The first dataset, `RAW_recipes.csv`, contains 83782 rows, indicating 83782 unique recipes, with 12 columns recording the following information:

| Column          |Description |
|---|---|
| `name`          | Recipe name |
| `id`            | Recipe ID |
| `minutes`       | Minutes to prepare recipe |
| `contributor_id`| User ID who submitted this recipe |
| `submitted`     | Date recipe was submitted |
| `tags`          | Food.com tags for recipe |
| `nutrition`     | Nutrition information in the form `[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`. PDV stands for "percentage of daily value". |
| `n_steps`       | Number of steps in recipe |
| `steps`         | Text for recipe steps, in order |
| `description`   | User-provided description |
| `ingredients`   | Text for recipe ingredients |
| `n_ingredients` | Number of ingredients in recipe |

The second dataset, `RAW_interactions.csv`, contains 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column     | Description           |
|------------|-----------------------|
| `user_id`  | User ID               |
| `recipe_id`| Recipe ID             |
| `date`     | Date of interaction   |
| `rating`   | Rating given          |
| `review`   | Review text           |

After gathering the recipes and interaction datasets, we left merged the two datasets and created a new column `'rating_avg'` containing the average rating per recipe. In the merged dataset, we filled all ratings of 0 with `np.nan`. A rating of 0 can indicate missing or unsubmitted ratings rather than an actual evaluation. By replacing these values with `np.nan`, we treat them as missing data, which aligns with their likely intent. Including 0 as a valid rating could also significantly lower the calculated average ratings for recipes, especially if a large number of 0 values are present (15035 ratings of 0).

Based on our question, we want to compare the nutritional values (sugar and saturated fat) for "healthy" tagged and no "healthy" tagged recipes. To investigate this, the 'nutrition' column was broken down into seperate components, including `'calories (#)'`, `'total fat (PDV)'`, `'sugar (PDV)'`, and others. The percent daily value (PDV) represents how much a nutrient in a serving contributes to a person's daily dietary needs. We then created a new column called `'sugar'`, which calculates the proportion of calories from sugar in relation to the total calories of a recipe. We also want to analyze the saturated fat; resulting to a second new column called `'saturated_fat'` that calculates the proportion of calories from saturated fat in relation to the total calories of a recipe. 

In addition, we also added `'is_healthy'` column to the dataframe by checking if the tag of the recipe contains 'healthy' with values 'True' if it contains 'healthy' and 'False' if it does not contain 'healthy' tag.

Doing these steps enable us to create a robust framework, allowing it easier to analyze the relationship between nutritional values and recipe characteristics. 

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning
After we obtained two datasets, we performed a series of data cleaning steps:

<!-- Describe, in detail, the data cleaning steps you took and how they affected your analyses. The steps should be explained in reference to the data generating process. Show the head of your cleaned DataFrame (see Part 2: Report for instructions). -->

1. Left-merged the two datasets based on the matching id. 

    From the `recipes` data, we can take `recipe_id` and `id` from the `interactions` data frame. This step is done to match the unique recipe id on the `recipes` data. By doing this step, all the columns from the `interactions` data frame will be added onto the `recipes` data frame.

2. After merging, replace all ratings of 0 with `np.nan`.

    This step is crucial because 0 indicates missing data or unsubmitted ratings rather than an actual evaluation. By replacing these values with `np.nan`, we can treat them as missing data. Including 0 as a valid rating could also significantly lower the calculated average ratings for recipe.

3. Calculated the average of ratings and add this to a new column named `rating_avg`.

    Some recipes have more than one reviews and ratings. That is why we take an average of all the ratings based on the id's recipe.

4. Added `sugar` column.

    We extracted the sugar content from the `nutrition` column and calculated the proportion of sugar relative to total calories using the formula:  
    $$
    \text{Sugar Proportion per Calorie} = \frac{\text{(Sugar PDV / 100) * 50}}{\text{Calories}}
    $$  
    This normalization accounts for varying calorie counts across recipes, enabling a fair comparison of sugar density between recipes.
    We extracted the sugar content from `nutrition` column and calculated the proportion of sugar relative to total calories.

5. Added `saturated_fat` column.

    We extracted the saturated fat content from `nutrition` column and calculated the proportion of saturated fat relative to total calories.

6. Added `is_healthy` column.

    We created a boolean column to identify recipes tagged as "healthy" marking them as `True` or `False` based on their tags.
    We have made sure that the values of the tags that contain "healthy" are only 'healthy' and 'healthy-2'.

Since we are not going to utilize all the columns, we will leave other columns as is and only selected the columns that are most relevant to our questions for display.

This is what the few rows of the resulted data frame looks like:

| name                                | id     | minutes | contributor_id | submitted   | date       | rating | review                                      | rating_avg | is_healthy |
|-------------------------------------|--------|---------|----------------|-------------|------------|--------|---------------------------------------------|------------|------------|
| 1 brownies in the world best ever   | 333281 | 40      | 985201         | 2008-10-27  | 2008-11-19 | 4.0    | These were pretty good, but took forever...| 4.0        | False      |
| 1 in canada chocolate chip cookies  | 453467 | 45      | 1848091        | 2011-04-11  | 2012-01-26 | 5.0    | Originally I was gonna cut the recipe in...| 5.0        | False      |
| 412 broccoli casserole              | 306168 | 40      | 50969          | 2008-05-30  | 2008-12-31 | 5.0    | This was one of the best broccoli casse... | 5.0        | False      |
| 412 broccoli casserole              | 306168 | 40      | 50969          | 2008-05-30  | 2009-04-13 | 5.0    | I made this for my son's first birthday... | 5.0        | False      |
| 412 broccoli casserole              | 306168 | 40      | 50969          | 2008-05-30  | 2013-08-02 | 5.0    | Loved this. Be sure to completely thaw...  | 5.0        | False      |



Each variables in the data frame above has their own data types.
## Column Data Types

| Column Name         | Data Type  |
|---------------------|------------|
| `name`                | object     |
| `id`                  | int64      |
| `minutes`             | int64      |
| `contributor_id`      | int64      |
| `submitted`           | object     |
| `tags`                | object     |
| `nutrition`           | object     |
| `n_steps`             | int64      |
| `steps`               | object     |
| `description`         | object     |
| `ingredients`         | object     |
| `n_ingredients`       | int64      |
| `user_id`             | float64    |
| `recipe_id`           | float64    |
| `date`                | object     |
| `rating`              | float64    |
| `review`              | object     |
| `rating_avg`          | float64    |
| `is_healthy`          | bool       |
| `sugar`               | float64    |
| `saturated_fat`       | float64    |

## Univariate Analysis

<!-- Embed at least one plotly plot you created in your notebook that displays the distribution of a single column (see Part 2: Report for instructions). Include a 1-2 sentence explanation about your plot, making sure to describe and interpret any trends present. (Your notebook will likely have more visualizations than your website, and that’s fine. Feel free to embed more than one univariate visualization in your website if you’d like, but make sure that each embedded plot is accompanied by a description.) -->

For this analysis, we observed the distribution of the proportion of sugar and the proportion of saturated fat in a recipe. Based on the two plots, the distribution skewed to the right, indicating that most of the recipes on [food.com](https://www.food.com) have a low proportion of sugar and saturated fats.

<!-- insert plot dist. of sugar proportion -->
<iframe
  src="assets/fig_sugar.html"
  width="800"
  height="400"
  frameborder="0"
  style="margin: 0; padding: 0; display: block;"
></iframe>
<!-- insert plot dist. of sat fat proportion -->
<iframe
  src="assets/fig_satfat.html"
  width="800"
  height="400"
  frameborder="0"
  style="margin: 0; padding: 0; display: block;"
></iframe>

## Bivariate Analysis
We also observed the distribution of the proportion of the saturated fats and the proportion of the sugar of the recipe based on the `'is_healthy'` column.

1. Observed the saturated fat distribution grouped by `'is_healthy'`.

<!-- insert plot dist. of sat fat grouped by is healthy -->
<iframe
  src="assets/box-fig.html"
  width="800"
  height="600"
  frameborder="0"
  style="margin: 0; padding: 0; display: block;"
></iframe>

The box plot above compares the distribution of saturated fat proportions (PDV) between recipes tagged as "healthy" (true) and those not tagged as "healthy" (false). This suggests that "healthy" recipes tend to have less saturated fat on average.

2. Observed the sugar distribution grouped by `'is_healthy'`.

<!-- insert plot dist. of sat fat grouped by is healthy -->
<iframe
  src="assets/box-fig2.html"
  width="800"
  height="600"
  frameborder="0"
  style="margin: 0; padding: 0; display: block;"
></iframe>
This box plot suggests that "healthy" recipes may sometimes include higher sugar content. 

## Interesting Aggregates
From this data set, we observed the relationship between sugar proportion to the number of ingredients to see if there might be a relationship across recipes. We used the mean, median, minimum, and maximum values for sugar content.
<!-- insert plot dist. of sugar and n ingredients -->
<iframe
  src="assets/sugar_n_fig.html"
  width="800"
  height="600"
  frameborder="0"
  style="margin: 0; padding: 0; display: block;"
></iframe>
Recipes with fewer ingredients tend to have higher sugar proportions on average.
This suggests that simpler recipes (fewer ingredients) might often be desserts or sugar-rich recipes like cookies.

# Assessment of Missingness

<!-- State whether you believe there is a column in your dataset that is NMAR. Explain your reasoning and any additional data you might want to obtain that could explain the missingness (thereby making it MAR). Make sure to explicitly use the term “NMAR.” -->

## NMAR Variables
In our data, the missingness of the `review` column is NMAR.

- **Explanation for NMAR**: Missingness in the `'review'` column might depend on the specific content or experience associated with a recipe, which is not directly observable in the dataset. For example, users might only leave reviews if they had an exceptional (positive or negative) experience, while those with neutral or lackluster experiences might be less likely to contribute a review. This suggests that the missingness is related to unobserved factors (such as user satisfaction) that are not included in the dataset.

- **Additional Data for Making it MAR**: To make the missingness **Missing At Random (MAR)**, we could collect data that explains why certain users choose not to leave reviews. For instance:
  - Feedback submission habits of users (e.g., whether they tend to leave reviews for other recipes).
  - Time spent on the recipe page or step completion rate, indicating the user’s level of engagement.
  
By gathering this additional data, we could link the missingness to observable variables, thereby making the missingness MAR instead of NMAR.
## Missingness Dependency
<!-- Present and interpret the results of your missingness permutation tests with respect to your data and question. Embed a plotly plot related to your missingness exploration; ideas include:
• The distribution of column Y when column X is missing and the distribution of column Y when column X is not missing, as was done in Lecture 8.
• The empirical distribution of the test statistic used in one of your permutation tests, along with the observed statistic. -->

To investigate the missingness of the `rating` column, **we investigated if the missingness in the `'rating'` depends on the proportion of the saturated fat in a recipe as indicated in the `'saturated_fat'` column**. 

The hypotheses and test statistic are as follows:

- Null Hypothesis: The distribution of `'saturated_fat'` when `'rating'` is missing is the same as the distribution of `'saturated_fat'` when `'rating'` is not missing.
- Alternate Hypothesis: The distribution of `'saturated_fat'` when `'rating'` is missing is not the same as the distribution of `'saturated_fat'` when `'rating'` is not missing.
- Test Statistic: The absolute difference of mean in the proportion of saturated fat of the distribution of the group without missing ratings and the distribution of the group without missing ratings.
- Significance Level: 0.05

1. Choosing a significance level.
    A significance level of 0.05 strikes a balance between rigor and practicality, minimizing the chance of incorrectly rejecting the null hypothesis while maintaining sensitivity to meaningful differences.

2. Choosing a test statistic. 
    Firstly, we added a new column `rating_missing` to see if the `rating` is missing or not.

    <!-- insert plot Distributions of saturated fat proportions when rating is missing or not -->
<iframe
    src="assets/sat_fat_missing.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>
Because the two distributions show a similar shape, we chose the absolute difference of mean in the proportion of saturated fat of the distribution of the group without missing and non-missing ratings as the test statistic.

3. Run a permutation test and calculate the observed difference.

    <!-- insert plot Empirical Distribution of the Absolute Difference in Saturated Fat-->

<iframe
    src="assets/fig_2.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

We ran a permutation test by shuffling the missingness of rating for 500 times to collect 500 simulating mean differences in the two distributions.

We also calculated the observed difference, which is 0.0019, indicated by the red vertical line on the graph. 
    
Since the p_value that we found (0.0) is <= 0.05 which is the significance level that we set, we reject the null hypothesis. **The missingness of `'rating'` does depend on the `'saturated_fat'`**, which is proportion of saturated fat in the recipe.



Second, **we investigated if the missingness in the `'rating'` depends on the proportion of the sugar in a recipe as indicated in the `'sugar'` column**. 

The hypotheses and test statistic are as follows:

- Null Hypothesis: The distribution of `'sugar'` when `'rating'` is missing is the same as the distribution of `'sugar'` when `'rating'` is not missing.
- Alternate Hypothesis: The distribution of `'sugar'` when `'rating'` is missing is not the same as the distribution of `'sugar'` when `'rating'` is not missing.
- Test Statistic: K-S test statistic of the two groups.
- Significance Level: 0.05

1. Choosing a significance level.
    A significance level of 0.05 strikes a balance between rigor and practicality, minimizing the chance of incorrectly rejecting the null hypothesis while maintaining sensitivity to meaningful differences.

2. Choosing a test statistic. 
    Plot the distributions of sugar proportions when rating is missing and when rating is not missing.
    <!-- insert plot Distributions of sugar proportions when rating is missing or not-->

<iframe
    src="assets/sugar_missing.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

We can see that the two distribution does not have similar shape. Hence, we chose K-S test as our test statistic.

3. Run a permutation test and calculate the observed difference.

    <!-- insert plot Empirical Distribution of the K-S Statistic of Sugar Proportion-->

<iframe
    src="assets/fig_3.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

We ran a permutation test by shuffling the missingness of rating for 500 times to collect 500 simulating mean differences in the two distributions.

We also calculated the observed statistic, which is 0.0186, indicated by the red vertical line on the graph. 
    
Since the p-value that we found (0.0) is <= 0.05 which is the significance level that we set, we reject the null hypothesis. **The missingness of `'rating'` does depend on the `'sugar'`**, which is proportion of sugar in the recipe.

Third, **we investigated if the missingness in the `'rating'` depends on the minutes column**. 

The hypotheses and test statistic are as follows:

- Null Hypothesis: The distribution of `'minutes'` when `'rating'` is missing is the same as the distribution of `'minutes'` when `'rating'` is not missing.
- Alternate Hypothesis: The distribution of `'minutes'` when `'rating'` is missing is not the same as the distribution of `'minutes'` when `'rating'` is not missing.
- Test Statistic: The absolute difference of mean in the minutes of the distribution of the group without missing ratings and the distribution of the group without missing ratings.
- Significance Level: 0.05

<!-- insert plot before log transformation-->
<iframe
    src="assets/fig_4.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

Because the range of the minutes are too significant, we are unable to see certain minutes on the graph. To better visualize the distribution and identify outliers, we can apply log transformation. Because there are values of 0 that exist in our `'minutes'` data, we use `log1p(x)` in Python, which computes `log(1+x)` to handle zeros.

<!-- insert plot after log transformation-->

<iframe
    src="assets/fig_5.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

1. Choosing a significance level.
    A significance level of 0.05 strikes a balance between rigor and practicality, minimizing the chance of incorrectly rejecting the null hypothesis while maintaining sensitivity to meaningful differences.

2. Choosing a test statistic. 
    Because the two distributions are roughly similar after the log transformation, we will use absolute difference in means as our test-statistic.

3. Run a permutation test and calculate the observed difference.
    We ran a permutation test by shuffling the missingness of rating for 500 times to collect 500 simulating mean differences in the two distributions.

    <!-- insert plot after log transformation-->

<iframe
    src="assets/fig_6.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

The observed statistic of 51.4524 is indicated by the red vertical line on the graph. 
    
Since the p_value that we found (0.126) is > 0.05 which is the significance level that we set, we fail to reject the null hypothesis. **The missingness of `'rating'` does not depend on the cooking time (`'minutes'`)**, which is proportion of saturated fat in the recipe.

# Hypothesis Testing
Back to our main research question, which is to investigate if **Recipes Tagged as “Healthy” Tend to Have Significantly Lower Proportion for Saturated Fat or Sugar Compared to Recipes Without this Tag?**

Since this involves two variables (saturated fat and sugar), we will conduct separate hypothesis tests for each variable. The steps are as follows:

### 1. "Healthy" Recipes and Sugar Proportion

The hypotheses and test statistic are as follows:

- Null hypothesis: Recipes tagged as "healthy" have the same proportion of sugar as recipes not tagged as "healthy".
- Alternate Hypothesis: Recipes tagged as "healthy" have a significantly lower proportion of sugar compared to recipes not tagged as "healthy".
- Test Statistic: The difference between the mean proportion of sugar in recipes tagged as "healthy" and the mean proportion of sugar in recipes not tagged as "healthy".
- Significance Level: 0.05

1. Choosing a significance level.
    A significance level of 0.05 strikes a balance between rigor and practicality, minimizing the chance of incorrectly rejecting the null hypothesis while maintaining sensitivity to meaningful differences.

2. Choosing a test statistic. 
    <!-- insert plot -->
<iframe
    src="assets/sugar_healthy_nonhealthy.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>
Based on the plot above, we could see that the two distributions are different. But we want to know which recipe has a lower proportion so we still chose the difference between the mean proportion of sugar in recipes tagged as "healthy" and the mean proportion of sugar in recipes not tagged as "healthy".

3. Run a permutation test and calculate the observed difference.
    We ran a permutation test by shuffling the missingness of rating for 500 times to collect 500 simulating mean differences in the two distributions.
    <!-- insert plot -->
<iframe
    src="assets/fig_7.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>
The observed statistic of 0.0516 is indicated by the red vertical line on the graph. 

Since the p-value that we found (1.0) is > 0.05 which is the significance level that we set, **we fail to reject the null hypothesis. Recipes tagged as "healthy" have the same proportion of sugar as recipes not tagged as "healthy."**

### 2. "Healthy" Recipes and Saturated Fat Proportion
    
The hypotheses and test statistic are as follows:

- Null hypothesis: Recipes tagged as "healthy" have the same proportion of saturated fat as recipes not tagged as "healthy".
- Alternate Hypothesis: Recipes tagged as "healthy" have a significantly lower proportion of saturated fat compared to recipes not tagged as "healthy".
- Test Statistic: proportion of saturated fat as recipes tagged as "healthy" - proportion of saturated fat as recipes not tagged as "healthy"
- Significance Level: 0.05

1. Choosing a significance level.
    A significance level of 0.05 strikes a balance between rigor and practicality, minimizing the chance of incorrectly rejecting the null hypothesis while maintaining sensitivity to meaningful differences.

2. Choosing a test statistic. 
    <!-- insert plot -->
<iframe
    src="assets/satfats_healthy_nonhealthy.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

We can see that the two distribution does not have similar shape. In contrast, we still chose the proportion of saturated fat as recipes tagged as "healthy" - proportion of saturated fat as recipes not tagged as "healthy" to determine which recipe has a lower proportion of saturated fats. 

3. Run a permutation test and calculate the observed difference.

    We ran a permutation test by shuffling the missingness of rating for 500 times to collect 500 simulating mean differences in the two distributions.

    <!-- insert plot after log transformation-->
<iframe
    src="assets/fig_8.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>

The observed statistic of -0.04128 is indicated by the red vertical line on the graph. 

Since the p-value that we found (0.0) is <= 0.05 which is the significance level that we set, **we reject the null hypothesis. Recipes tagged as "healthy" does not have the same proportion of saturated fat as recipes not tagged as "healthy"**. This indicates that the proportion of saturated fat in recipes tagged as "healthy" is significantly lower than the proportion of saturated fat in recipes not tagged as "healthy". This result supports the idea that recipes labeled as "healthy" are associated with lower saturated fat content.


# Framing a Prediction Problem

## Problem Identification

<!-- Clearly state your prediction problem and type (classification or regression). If you are building a classifier, make sure to state whether you are performing binary classification or multiclass classification. Report the response variable (i.e. the variable you are predicting) and why you chose it, the metric you are using to evaluate your model and why you chose it over other suitable metrics (e.g. accuracy vs. F1-score).

Note: Make sure to justify what information you would know at the “time of prediction” and to only train your model using those features. For instance, if we wanted to predict your final exam grade, we couldn’t use your Final Project grade, because the project is only due after the final exam! Feel free to ask questions if you’re not sure. -->

We will build a baseline model to predict whether a recipe is `is_healthy` using `saturated_fat` and `sugar` as predictor variables. This variable was chosen as the response because it provides a straightforward and meaningful classification problem, aligning with the objective to assess the healthiness of recipes based on measurable nutritional indicators. 

Since `is_healthy` is a nominal categorical variable with values either `True` or `False`, we will perform binary classification. 

Its performance was assessed through classification metrics, including precision, recall, F1-score, and accuracy, on both the training and testing datasets. 

- Precision was chosen to measure the proportion of correctly identified healthy recipes out of all recipes predicted as healthy, which is important if false positives (unhealthy recipes mislabeled as healthy) need to be minimized.
- Recall was included to evaluate the proportion of truly healthy recipes correctly identified by the model, which is crucial if missing healthy recipes (false negatives) is a significant concern.
- F1-score provides a balanced measure that considers both precision and recall, making it suitable when there is a trade-off between these metrics or when the classes are imbalanced
- Accuracy was also reported as it gives a general overview of model performance by showing the proportion of all correctly classified recipes. However, it may be less reliable if the dataset is imbalanced.

To ensure the model aligns with real-world prediction scenarios, we carefully selected features (`saturated_fat` and `sugar`) that are known or measurable at the "time of prediction". These features are derived directly from the recipe's nutritional information, which is available prior to determining whether the recipe is classified as healthy.


## Baseline Model

<!-- Describe your model and state the features in your model, including how many are quantitative, ordinal, and nominal, and how you performed any necessary encodings. Report the performance of your model and whether or not you believe your current model is “good” and why.

Tip: Make sure to hit all of the points above: many projects in the past have lost points for not doing so. -->

We utilized scikit-learn to create a unified Pipeline for preprocessing and model training. Quantitative features (`saturated_fat`, `sugar`) were scaled using `StandardScaler` to ensure uniformity across features, enhancing model performance.

For classification, we employed a `RandomForestClassifier` with a maximum depth of 10 and 100 estimators. These hyperparameter values were chosen arbitrarily as a starting point to establish a baseline model. While they may not be optimal, they provide a reasonable framework for initial testing and evaluation. 

Afterwards, the model was trained using an 80-20 train-test split.

<!-- The result of training performance -->
## Training Performance

| Class        | Precision | Recall | F1-Score | Support  |
|--------------|-----------|--------|----------|----------|
| 0            | 0.83      | 0.98   | 0.90     | 149,811  |
| 1            | 0.78      | 0.21   | 0.34     | 37,732   |
| **Accuracy** | **0.83**  |        |          | 187,543  |
| **Macro Avg**| 0.81      | 0.60   | 0.62     | 187,543  |
| **Weighted Avg**| 0.82   | 0.83   | 0.79     | 187,543  |

## Test Performance

| Class        | Precision | Recall | F1-Score | Support  |
|--------------|-----------|--------|----------|----------|
| 0            | 0.83      | 0.98   | 0.90     | 37,493   |
| 1            | 0.73      | 0.21   | 0.32     | 9,393    |
| **Accuracy** | **0.83**  |        |          | 46,886   |
| **Macro Avg**| 0.78      | 0.59   | 0.61     | 46,886   |
| **Weighted Avg**| 0.81   | 0.83   | 0.78     | 46,886   |


The baseline model achieves an overall accuracy of 83% on both training and test datasets, but the F1-scores reveal significant class imbalance. For class 0 ('not healthy'), the F1-score is high (0.90) on both sets, indicating strong precision and recall. However, for class 1 (healthy), the F1-score is much lower (0.32 on the test set), reflecting poor recall and modest precision. This imbalance suggests the model struggles to correctly identify healthy recipes and favors the majority class.

## Final Model

<!-- State the features you added and why they are good for the data and prediction task. Note that you can’t simply state “these features improved my accuracy”, since you’d need to choose these features and fit a model before noticing that – instead, talk about why you believe these features improved your model’s performance from the perspective of the data generating process.

Describe the modeling algorithm you chose, the hyperparameters that ended up performing the best, and the method you used to select hyperparameters and your overall model. Describe how your Final Model’s performance is an improvement over your Baseline Model’s performance.

Optional: Include a visualization that describes your model’s performance, e.g. a confusion matrix, if applicable. -->

Since the last model used arbitrary values for our hyperparameters, with 10 for our maximum depth and 100 for the number of our estimators, the model resulted to a significant class imbalance for F1-scores. This performance imbalance demonstrates that the model struggled to identify healthy recipes, favoring the majority class. 

Instead of using arbitrary values for hyperparameters, we can create a better model by tuning hyperparameters. Steps of creating our new model involve:

We used feature engineering to create 2 new features.

1.⁠ ⁠Feature 1: Interaction Term (⁠saturated_fat * sugar).
   This feature captures the combined effect of `⁠ saturated_fat ⁠` and ⁠sugar ⁠on whether a recipe is healthy. Recipes high in both saturated fat and sugar are less likely to be considered healthy, but this relationship might not be strictly additive; it could be multiplicative or more complex. By introducing the interaction term, the model can better capture this non-linear relationship between the two nutritional components, which may provide more meaningful insights into what constitutes a "healthy" recipe.

2.⁠ ⁠Feature 2: Binarized ⁠ saturated_fat ⁠(Low/High).
   By binarizing ⁠ saturated_fat ⁠ into "low" and "high" categories using the median value as a threshold, we introduce a categorical feature that simplifies the distinction between recipes with varying levels of saturated fat. This transformation reflects practical dietary guidelines, where saturated fat thresholds are often used to assess healthiness. It also helps the model differentiate between recipes with significantly different health implications due to their fat content.

3.⁠ ⁠Transform Existing Feature: Quantile Transformation on ⁠sugar.  
   The ⁠ sugar ⁠ feature is transformed using a quantile transformation to make it robust to outliers. Sugar content is often highly skewed in recipe datasets, as some recipes (e.g., desserts) have disproportionately high values. The quantile transformation ensures that these extreme values do not dominate the model's training process, allowing it to focus on meaningful patterns across the majority of the data.

Then we do Hyperparameter Tuning.

    Instead of using 10 for our maximum depth and 100 for our number of estimators, we performed hyperparameter tuning using `GridSearchCV` to systematically explore a range of parameter values and identify the combination that yields the best results.

    A 3-fold cross-validation was performed, splitting the training data into three subsets to evaluate each parameter combination's performance.

3. Evaluate the model based on the best estimator.
    <!-- Insert graph  -->
## Training Performance

| Class | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| 0     | 0.99      | 1.00   | 1.00     | 149,811 |
| 1     | 0.99      | 0.98   | 0.98     | 37,732  |
| **Accuracy** | **0.99** |        |          | 187,543 |
| **Macro Avg** | 0.99      | 0.99   | 0.99     | 187,543 |
| **Weighted Avg** | 0.99   | 0.99   | 0.99     | 187,543 |

## Test Performance

| Class | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| 0     | 0.96      | 0.97   | 0.97     | 37,493  |
| 1     | 0.89      | 0.85   | 0.87     | 9,393   |
| **Accuracy** | **0.95** |        |          | 46,886  |
| **Macro Avg** | 0.92      | 0.91   | 0.92     | 46,886  |
| **Weighted Avg** | 0.95   | 0.95   | 0.95     | 46,886  |


After fitting, make predictions, and evaluating the model based on the best estimator, we have found that the best maximum depth `max_depth` for our model is 55 and number of estimators or `n_estimators` is 150.

The final model demonstrates significant improvement over the baseline, achieving an overall test accuracy of 95% with well-balanced performance across both classes. On the training set, the model shows near-perfect precision, recall, and F1-scores (0.99 for both classes), which, while impressive, may indicate slight overfitting. On the test set, the model performs strongly for class 0 (not healthy), with an F1-score of 0.97, and for class 1 (healthy), with an F1-score of 0.87, marking a substantial improvement in identifying the minority class. The optimized hyperparameters (`max_depth` of 50 and 150 estimators) and the inclusion of engineered features. Overall, the final model is robust, with enhanced ability to classify both healthy and non-healthy recipes, making it a well-rounded and reliable improvement.

## Fairness Analysis
<!-- Clearly state your choice of Group X and Group Y, your evaluation metric, your null and alternative hypotheses, your choice of test statistic and significance level, the resulting p-value, and your conclusion.

Optional: Embed a visualization related to your permutation test in your website. -->

1. For our fairness analysis, we will divide the groups of sugar into two, 'high sugar' and 'low sugar' based on a threshold (the median).

2. We will add a new column called `prediction` to contain the model prediction result.

    <!-- insert dataframe of prediction -->

| Index   | saturated_fat | sugar  | sugar_group | prediction |
|---------|---------------|--------|-------------|------------|
| 44111   | 1.32e-02      | 0.03   | low sugar   | 0          |
| 139442  | 1.35e-01      | 0.06   | high sugar  | 0          |
| 46961   | 5.83e-02      | 0.04   | low sugar   | 0          |
| 130648  | 1.83e-02      | 0.11   | high sugar  | 0          |
| 82361   | 7.10e-03      | 0.23   | high sugar  | 0          |
| ...     | ...           | ...    | ...         | ...        |
| 127869  | 0.00e+00      | 0.00   | low sugar   | 0          |
| 219764  | 1.90e-02      | 0.03   | low sugar   | 0          |
| 114825  | 4.39e-02      | 0.01   | low sugar   | 0          |
| 82261   | 0.00e+00      | 0.00   | low sugar   | 0          |
| 213245  | 1.21e-01      | 0.15   | high sugar  | 0          |

**Total Rows**: 46,886


3. The chosen evaluation metric for this fairness analysis is **precision**, which measures the proportion of true positives among all positive predictions. Precision is particularly suitable for this context because it focuses on the correctness of positive predictions, such as labeling a recipe as "healthy," and minimizes false positives. This is important because false positives, where unhealthy recipes are incorrectly classified as healthy, can mislead users and have critical implications, especially in health-related scenarios. 

    The precision for high sugar and low sugar are 0.8827 and 0.8938, respectively. 

4. Now we will run a permutation test to evaluate whether the difference in precision between the high-sugar and low-sugar groups is statistically significant. 

    The hypotheses and test statistic are as follows:

    - Null Hypothesis: The model is fair, and the precision for high-sugar and low-sugar groups is the same. Any observed difference in precision is due to random chance.
    - Alternative Hypothesis: The model is unfair, and the precision for high-sugar recipes is higher from the precision for low-sugar recipes.
    - Test Statistic: The observed difference in precision between the high-sugar and low-sugar groups
    - Significance level: 0.05

5. Calculate the observed difference.
   Since we are using difference of precision between the high-sugar and low-sugar groups, we got around -0.01 as our observed difference. 

6. Run a permutation test
    After we run the permutation test, we got a p-value of 0.933. 

    <!-- graph -->
<iframe
    src="assets/interactive_permutation_test.html"
    width="800"
    height="600"
    frameborder="0"
    style="margin: 0; padding: 0; display: block;"
></iframe>
In conclusion, the p-value obtained from the permutation test is **0.965** which is much greater than the chosen significance level of 0.05. There is no statistically significant evidence to suggest that the model's precision differs between the high-sugar and low-sugar groups. Therefore, the model appears to perform fairly with respect to the sugar group attribute.



