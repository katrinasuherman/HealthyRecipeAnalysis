# Do Recipes Tagged as “Healthy” Tend to Have Significantly Lower Proportion for Saturated Fat or Sugar Compared to Recipes Without this Tag?

Authors: Katrina Suherman & Rheka Narwastu

## Overview
This data science project, conducted at UCSD, focuses on investigating whether recipes tagged as “healthy” tend to have a significantly lower proportion of saturated fat or sugar compared to recipes without this tag. 

## Introduction

This project uses the `RAW_recipes` and `RAW_interactions` datasets, which provide detailed information about thousands of recipes and user interactions on [food.com.](https://www.food.com) The `RAW_recipes` dataset includes attributes such as number of ingredients, steps, nutritional values, tags like "healthy," and more. Meanwhile, the `RAW_interactions` dataset offers insights into user reviews and ratings. Together, these datasets enable us to explore whether recipes tagged as “healthy” truly reflect better nutritional quality. Our central question is: **Do recipes tagged as “healthy” tend to have significantly lower proportions of saturated fat or sugar compared to recipes without this tag?** 

This question matters because tags like "healthy" are often used to guide dietary decisions, yet their reliability is rarely scrutinized. With increasing focus on health-conscious eating, understanding whether these tags align with nutritional standards empowers individuals to make informed food choices and encourages transparency in recipe labeling. By examining this question, we aim to provide insights for health-conscious individuals, nutritionists, and recipe platforms, fostering better accountability and informed decisions in the culinary space.

The first dataset, `RAW_recipes.csv`, contains 83782 rows, indicating 83782 unique recipes, with 10 columns recording the following information:

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

