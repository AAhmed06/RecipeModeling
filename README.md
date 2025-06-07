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

5. Drop the columns which are not useful to the analysis, these being `review`, `steps`, `description` , and `nutrition` (which is now redundant)

5. Create a new dataframe `filtered_df` by dropping rows with missing values and removing outliers from the `minutes` column using the IQR method

6. Add a column `is_long` which assigns True if a recipe is over 60 minutes long, otherwise false

8. Reset the index of filtered_df

This results in the following dataset, with the first five rows here:

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                        |   n_steps | ingredients                                                                                                                                                                    |   n_ingredients |          user_id |   recipe_id | date       |   rating |   avg_rating |   calories |   total_fat_pct |   sugar_pct |   sodium_pct |   protein_g |   sat_fat_pct |   carbs_pct | is_long   |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|------------:|:-----------|---------:|-------------:|-----------:|----------------:|------------:|-------------:|------------:|--------------:|------------:|:----------|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] |        10 | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 | 386585           |      333281 | 2008-11-19 |        4 |            4 |      138.4 |              10 |          50 |            3 |           3 |            19 |           6 | False     |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                               |        12 | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 | 424680           |      453467 | 2012-01-26 |        5 |            5 |      595.1 |              46 |         211 |           22 |          13 |            51 |          26 | False     |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |  29782           |      306168 | 2008-12-31 |        5 |            5 |      194.8 |              20 |           6 |           32 |          22 |            36 |           3 | False     |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |      1.19628e+06 |      306168 | 2009-04-13 |        5 |            5 |      194.8 |              20 |           6 |           32 |          22 |            36 |           3 | False     |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 | 768828           |      306168 | 2013-08-02 |        5 |            5 |      194.8 |              20 |           6 |           32 |          22 |            36 |           3 | False     |

### Univariate Analysis

For the first piece of univariate analysis, I examined the distribution of minutes a recipe takes. As the plot shows, the distribution is slightly skewed to the left, with most recipes in the filtered dataset less than 60 minutes.

<iframe
  src="assets/dist_minutes_univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

For the second piece of univariate analysis, I examined the distribution of average ratings in the dataset. As you can see, the dataset is clearly skewed to the right, with most of the recipes being 4.75-5 stars, with the rest trailing significantly.

<iframe
  src="assets/rating_dist_univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

For this piece of bivariate analysis, I examined the relationship between a recipe's preparation time and its average rating. As the plot shows, there is no simple linear trend; instead, the vast majority of highly-rated (4-5 star) recipes are concentrated at lower preparation times, suggesting convenience is a key factor in a recipe's success.

<iframe
  src="assets/bivariate_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

|   minutes |   ('mean', 'n_steps') |   ('median', 'n_steps') |   ('min', 'n_steps') |   ('max', 'n_steps') |
|----------:|----------------------:|------------------------:|---------------------:|---------------------:|
|         0 |               1       |                     1   |                    1 |                    1 |
|         1 |               3.3772  |                     3   |                    1 |                   49 |
|         2 |               3.36367 |                     3   |                    1 |                   30 |
|         3 |               3.97939 |                     4   |                    1 |                   22 |
|         4 |               5.12569 |                     4   |                    1 |                   38 |
|         ...|               ...|                     ...|                    ...|                   ...|
|       115 |              14.7774  |                    13   |                    5 |                   46 |
|       116 |              25       |                    25   |                   13 |                   37 |
|       117 |               9       |                     9   |                    9 |                    9 |
|       118 |              10       |                    10   |                   10 |                   10 |
|       120 |              15.6149  |                    14   |                    1 |                   57 |
