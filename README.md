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

### Interesting Aggregate

In this section, I explored the relationship between a recipe's cooking time (minutes) and its complexity, measured by the number of steps (n_steps). I first ensured the cooking time data was clean by filtering out extreme outliers. After grouping the cooking time and the number of steps into the pivot table shown below, I created a data visualization to better interpret the trends.

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

The resulting visualization shows a clear positive correlation: as the cooking time for a recipe increases, the average number of steps also tends to increase. However, the plot also highlights significant variability, evidenced by the large range between the minimum and maximum number of steps for any given cooking time.

## Assessment of Missingness

Three columns in the merged dataset have missing values, these being `date`, `rating`, and `review`.

### NMAR Analaysis

I believe that the missingess of the `review` column is not missing at random, as oftentimes individuals will be willing to put in the effort to rate a recipe, but writing a review is often excessive to some. Because of this, people are more likely to leave reviews if they feel strongly positive or negative about the recipe, with opinions in between not contributing a review.

### Missingness Dependancy
To further understand the nature of the missing data, I moved on to test the dependency of missingness in the `rating` column. Specifically, I investigated whether the missingness of rating depends on the recipe's cooking time (`minutes`) or its complexity (`n_steps`).

> Minutes and Rating

**Null Hypothesis**: The missingness of ratings does not depend on the cooking time of the recipe in minutes.

**Alternate Hypothesis**: The missingness of ratings does depend on the cooking time of the recipe in minutes.

**Test Statistic**: The absolute difference in the mean cooking time (minutes) between the group with missing ratings and the group with non-missing ratings.

**Significance Level**: 0.05

I ran a permutation test by shuffling the minutes values 10,000 times to simulate a distribution of mean differences under the null hypothesis.

The observed statistic of approximately 52.0 is indicated by the red vertical line on the graph. This observed difference is extremely far in the tail of the simulated distribution. The resulting p-value was effectively 0.0, which is less than the 0.05 significance level. Therefore, I reject the null hypothesis. The evidence strongly suggests the missingness of rating does depend on the cooking time of the recipe.

<iframe
  src="assets/missing_minutes_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> Steps and Rating

**Null Hypothesis**: The missingness of ratings does not depend on the number of steps in the recipe.

**Alternate Hypothesis**: The missingness of ratings does depend on the number of steps in the recipe.

**Test Statistic**: The absolute difference in the mean number of steps (n_steps) between the group with missing ratings and the group with non-missing ratings.

**Significance Level**: 0.05

I ran another permutation test, this time shuffling the n_steps values 10,000 times to collect a sample of simulated mean differences.

The observed statistic of approximately 1.35 is indicated by the red vertical line on the graph. As with the previous test, this result is an extreme outlier compared to the differences generated under the null hypothesis. The calculated p-value was effectively 0.0, which is less than the 0.05 significance level. Therefore, I reject the null hypothesis and conclude that the missingness of rating also depends on the number of steps in the recipe.

<iframe
  src="assets/missing_steps_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing

As previously mentioned, I am interested in finding out whether the length of time a recipe takes impacts the way people rate it. As such, I ran a permutation test with the `is_long` column.

**Null Hypotheis**: All recipes are rated the same way, regardless of time.

**Alternative Hypothesis**: Longer recipes are rated lower than short recipes.

**Test Statistic**: Difference in mean between short and long recipes.

**Significance Level**: 0.05

I chose to run this permutation test as it draws long and short recipes as if they were from the same population, without having to assume a normal distribution. I believe that a difference in rating exists because depending on perspective, longer recipes can either be seen as a hassle, or a rewarding project.

The **observed statistic** for the sample was **-0.007**. This indicates that the average rating for long recipes was a bit lower than short recipes. The **P-Value** for the hypothesis test was **0.0**.

Becuase the P-value is less than the significance level, we **reject the null hypothesis**. There is statistically significant evidence to support the claim that longer recipes are rated lower than short recipes.

<iframe
  src="assets/hypothesis_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>