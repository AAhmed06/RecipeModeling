# Cooking Time & Ratings: An Exploratory Analysis and Predictive Model

Author: Ali Ahmed

## Introduction

Food is an integral part of everyone's lives, and understanding what we cook and eat can have major impacts on our health, wellness, and happiness. As such, when it comes to cooking, it is useful to know what factors go into making one recipe better than another.
Additionally, time is an important factor in choosing what dishes we cook, as the finite amount of time we have in a day impacts 
the decisions that we make, which also applies to the food we eat and cook.
This project aims to answer the question of how time related features impact a recipe's rating.
In order to do this, I am analyzing two datasets containing recipes and online interactions with said recipes.

The first dataset I am using in my analysis is  `recipes` and contains 83,782 rows as well as 10 columns that contain the following information:

| Column | Description |
| :--- | :--- |
| `name` | Recipe name |
| `id` | Recipe ID |
| `minutes` | Minutes to prepare recipe |
| `contributor_id` | User ID|
| `submitted` | Date recipe was submitted |
| `tags` | informational tags for recipe |
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]|
| `n_steps` | Number of steps in the recipe |
| `steps` | Text for recipe steps, in order |
| `description` | User-provided description |
| `ingredients` | Text for recipe ingredients |
| `n_ingredients` | Number of ingredients in recipe |

The second  dataset is `interactions` and contains 731,927 rows and 5 columns with the following information:

| Column | Description |
| :--- | :--- |
| `user_id` | User ID |
| `recipe_id` | Recipe ID |
| `date` | Date of interaction |
| `rating` | Rating given |
| `review` | Review text |

Given the information in the datasets, my goal is to find out whether recipes are rated differently depending on the length and time they take.

## Data Cleaning and Exploratory Analysis

In order to ensure that our dataset is suitable for analysis, the first step is to clean it through the following steps:

1. Merge `recipes` and `interactions` on id and recipe_id

2. Fill all ratings of 0 with np.NaN

3. Add a column `avg_rating` containing the average rating per recipe

4. Split values in  the `nutrition` column into individual columns

5. Create a new dataframe `filtered_df` by dropping rows with missing values and removing outliers from the `minutes` column

6. Add a column `is_long` which assigns True if a recipe is over 60 minutes long, otherwise false

7. Reset the index of filtered_df

This results in the following dataset, with the first five rows here:
<iframe
  src="assets/df_head.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Univariate Analysis