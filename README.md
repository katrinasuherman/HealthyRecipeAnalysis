# Do Recipes Tagged as “Healthy” Tend to Have Significantly Lower Proportion for Saturated Fat or Sugar Compared to Recipes Without this Tag?

Authors: Katrina Suherman & Rheka Narwastu

## Overview
This data science project, conducted at UCSD, focuses on investigating whether recipes tagged as “healthy” tend to have a significantly lower proportion of saturated fat or sugar compared to recipes without this tag. 

## Introduction

This project uses the `RAW_recipes` and `RAW_interactions` datasets, which provide detailed information about thousands of recipes and user interactions on [food.com.](https://www.food.com) The `RAW_recipes` dataset includes attributes such as number of ingredients, steps, nutritional values, tags like "healthy," and more. Meanwhile, the `RAW_interactions` dataset offers insights into user reviews and ratings. Together, these datasets enable us to explore whether recipes tagged as “healthy” truly reflect better nutritional quality. Our central question is: **Do recipes tagged as “healthy” tend to have significantly lower proportions of saturated fat or sugar compared to recipes without this tag?** 

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

After gathering the recipes and interaction datasets, we left merged the two datasets and created a new column `'rating_avg'` containing the average rating per recipe. In the merged dataset, we filled all ratings of $0$ with `np.nan`. A rating of $0$ can indicate missing or unsubmitted ratings rather than an actual evaluation. By replacing these values with `np.nan`, we treat them as missing data, which aligns with their likely intent. Including $0$ as a valid rating could also significantly lower the calculated average ratings for recipes, especially if a large number of $0$ values are present ($15035$ ratings of $0$).

Based on our question, we want to compare the nutritional values (sugar and saturated fat) for "healthy" tagged and no "healthy" tagged recipes. To investigate this, the 'nutrition' column was broken down into seperate components, including `'calories (#)'`, `'total fat (PDV)'`, `'sugar (PDV)'`, and others. The percent daily value (PDV) represents how much a nutrient in a serving contributes to a person's daily dietary needs. We then created a new column called `'sugar'`, which calculates the proportion of calories from sugar in relation to the total calories of a recipe. We also want to analyze the saturated fat; resulting to a second new column called `'saturated_fat'` that calculates the proportion of calories from saturated fat in relation to the total calories of a recipe. 

In addition, we also added `'is_healthy'` column to the dataframe by checking if the tag of the recipe contains 'healthy' with values 'True' if it contains 'healthy' and 'False' if it does not contain 'healthy' tag.

Doing these steps enable us to create a robust framework, allowing it easier to analyze the relationship between nutritional values and recipe characteristics. 

## Step 2: Data Cleaning and Exploratory Data Analysis



