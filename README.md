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
<iframe
  src="assets/bivariate_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

For this piece of bivariate analysis, I examined the relationship between a recipe's preparation time and its average rating. As the plot shows, there is no simple linear trend; instead, the vast majority of highly-rated (4-5 star) recipes are concentrated at lower preparation times, suggesting convenience is a key factor in a recipe's success.


|   minutes |   ('mean', 'n_steps') |   ('median', 'n_steps') |   ('min', 'n_steps') |   ('max', 'n_steps') |
|----------:|----------------------:|------------------------:|---------------------:|---------------------:|
|         0 |               1       |                     1   |                    1 |                    1 |
|         1 |               3.3772  |                     3   |                    1 |                   49 |
|         2 |               3.36367 |                     3   |                    1 |                   30 |
|         3 |               3.97939 |                     4   |                    1 |                   22 |
|         4 |               5.12569 |                     4   |                    1 |                   38 |
|         5 |               4.07427 |                     3   |                    1 |                   60 |
|         6 |               6.54815 |                     5   |                    1 |                   48 |
|         7 |               5.83727 |                     5   |                    1 |                   26 |
|         8 |               6.0968  |                     5   |                    1 |                   25 |
|         9 |               6.7987  |                     6   |                    2 |                   20 |
|        10 |               5.49009 |                     5   |                    1 |                   44 |
|        11 |               7.34493 |                     7   |                    1 |                   21 |
|        12 |               7.51816 |                     7   |                    1 |                   44 |
|        13 |               7.7831  |                     7   |                    2 |                   21 |
|        14 |               8.07186 |                     7   |                    1 |                   27 |
|        15 |               6.92652 |                     6   |                    1 |                   73 |
|        16 |               8.69365 |                     8   |                    1 |                   38 |
|        17 |               8.08131 |                     7   |                    1 |                   33 |
|        18 |               9.11121 |                     9   |                    2 |                   86 |
|        19 |               9.98127 |                     9   |                    2 |                   27 |
|        20 |               8.38881 |                     8   |                    1 |                   42 |
|        21 |               9.536   |                     9   |                    2 |                   31 |
|        22 |               9.40856 |                     9   |                    2 |                   49 |
|        23 |               9.06459 |                     8   |                    2 |                   67 |
|        24 |              10.401   |                    10   |                    1 |                   34 |
|        25 |               8.92832 |                     8   |                    1 |                   38 |
|        26 |              10.6364  |                    10   |                    3 |                   27 |
|        27 |              10.394   |                    10   |                    1 |                   37 |
|        28 |              10.7282  |                    10   |                    1 |                   26 |
|        29 |              11.5294  |                    10   |                    3 |                   60 |
|        30 |              10.0793  |                     9   |                    1 |                   51 |
|        31 |              11.5455  |                    10   |                    3 |                   38 |
|        32 |              11.4607  |                    10   |                    1 |                   41 |
|        33 |              11.9369  |                    11   |                    1 |                   30 |
|        34 |              13.038   |                    12   |                    4 |                   28 |
|        35 |              10.4959  |                    10   |                    1 |                   76 |
|        36 |              11       |                    11   |                    1 |                   33 |
|        37 |              11.0346  |                    10   |                    4 |                   28 |
|        38 |              12.6302  |                    12   |                    3 |                   50 |
|        39 |              12.1652  |                    11   |                    4 |                   33 |
|        40 |              11.0655  |                    10   |                    1 |                   61 |
|        41 |              13.3951  |                    11   |                    5 |                   39 |
|        42 |              13.7906  |                    13   |                    1 |                   68 |
|        43 |              10.8372  |                     9   |                    4 |                   28 |
|        44 |              18.3276  |                    18.5 |                    4 |                   57 |
|        45 |              11.6912  |                    11   |                    1 |                   58 |
|        46 |              13.2772  |                    11   |                    4 |                   37 |
|        47 |              11.3114  |                     9   |                    2 |                   30 |
|        48 |              14.9082  |                    13   |                    2 |                   34 |
|        49 |              15.9674  |                    16   |                    5 |                   23 |
|        50 |              11.5678  |                    11   |                    1 |                   55 |
|        51 |              12       |                    11   |                    5 |                   32 |
|        52 |              15.0312  |                    12   |                    3 |                   34 |
|        53 |              16.2248  |                    15   |                    4 |                   27 |
|        54 |              12.0196  |                    10   |                    8 |                   18 |
|        55 |              11.631   |                    11   |                    1 |                   47 |
|        56 |              11.6078  |                    11   |                    6 |                   30 |
|        57 |              14.125   |                    15   |                    6 |                   31 |
|        58 |              13.879   |                    15   |                    5 |                   22 |
|        59 |              17.8421  |                    18   |                   15 |                   27 |
|        60 |              12.8806  |                    12   |                    1 |                   80 |
|        61 |              10.0426  |                     9   |                    1 |                   34 |
|        62 |              13.1774  |                    12   |                    1 |                   27 |
|        63 |              11.4     |                    11   |                    2 |                   32 |
|        64 |              11.25    |                     8   |                    2 |                   39 |
|        65 |              11.7918  |                    10   |                    1 |                   51 |
|        66 |              13.2059  |                    11.5 |                    3 |                   33 |
|        67 |              12.8936  |                    13   |                    2 |                   24 |
|        68 |              14.2658  |                    12   |                    5 |                   39 |
|        69 |              11.15    |                    11   |                    6 |                   19 |
|        70 |              11.4896  |                    10   |                    1 |                   47 |
|        71 |              10.3684  |                     8   |                    4 |                   17 |
|        72 |              15.6884  |                    15.5 |                    3 |                   32 |
|        73 |              15.4516  |                     9   |                    4 |                   32 |
|        74 |               9.27451 |                     8   |                    3 |                   27 |
|        75 |              13.1155  |                    12   |                    1 |                   59 |
|        76 |              11.6429  |                    12.5 |                    6 |                   15 |
|        77 |              12.7143  |                    13   |                    5 |                   19 |
|        78 |              13.4194  |                    12   |                    7 |                   36 |
|        79 |              13.5135  |                    11   |                    6 |                   46 |
|        80 |              12.7575  |                    11   |                    1 |                   49 |
|        81 |               7.88462 |                     5   |                    5 |                   31 |
|        82 |              14.9268  |                    14   |                    5 |                   44 |
|        83 |              24       |                    25   |                    7 |                   33 |
|        84 |              15.4091  |                    17   |                    7 |                   26 |
|        85 |              13.5362  |                    12   |                    4 |                   57 |
|        86 |              16.0833  |                    19   |                   10 |                   22 |
|        87 |              12.5     |                    14   |                    6 |                   23 |
|        88 |              11.875   |                    11   |                   10 |                   20 |
|        89 |              14.2941  |                    13   |                    9 |                   30 |
|        90 |              14.1038  |                    13   |                    2 |                   65 |
|        91 |              35.6     |                    15   |                    7 |                   76 |
|        92 |              11.8     |                    10   |                    5 |                   22 |
|        93 |              21.1702  |                    25   |                    5 |                   25 |
|        94 |              15.6364  |                    10   |                    2 |                   47 |
|        95 |              14.4977  |                    13   |                    2 |                   45 |
|        96 |              11.4     |                     9   |                    8 |                   22 |
|        97 |              13.0833  |                    11.5 |                    4 |                   24 |
|        98 |              11.0526  |                    11   |                    7 |                   22 |
|        99 |              19.3922  |                    19   |                    7 |                   26 |
|       100 |              14.2092  |                    13   |                    1 |                   55 |
|       101 |              10.6     |                     9   |                    9 |                   17 |
|       102 |              20.4286  |                    18   |                    6 |                   40 |
|       103 |              22       |                    19   |                   18 |                   28 |
|       104 |               9.92308 |                     9   |                    9 |                   14 |
|       105 |              14.1502  |                    14   |                    2 |                   62 |
|       106 |              16       |                    13   |                   13 |                   25 |
|       107 |              13.2857  |                    14   |                    9 |                   14 |
|       108 |              23       |                    19   |                   19 |                   31 |
|       109 |              13.1875  |                    13   |                    6 |                   18 |
|       110 |              14.6205  |                    14   |                    1 |                   42 |
|       111 |               8       |                     8   |                    8 |                    8 |
|       112 |              11.8462  |                    12   |                    6 |                   15 |
|       113 |               8       |                     8   |                    8 |                    8 |
|       114 |              17.6     |                    15   |                   13 |                   27 |
|       115 |              14.7774  |                    13   |                    5 |                   46 |
|       116 |              25       |                    25   |                   13 |                   37 |
|       117 |               9       |                     9   |                    9 |                    9 |
|       118 |              10       |                    10   |                   10 |                   10 |
|       120 |              15.6149  |                    14   |                    1 |                   57 |
