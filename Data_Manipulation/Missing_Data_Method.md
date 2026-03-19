## Missing data Method- Missing at Random (MAR)
#### Missing data methods are the set of tools used when some observations are incomplete. Surveys, administrative records, and panel datasets frequently contain observations where some variables are not recorded. 
Handling missing data incorrectly can lead to biased estimates and misleading conclusions. One of the most important of these is Missing at Random (MAR). Under the MAR assumption, the probability that a value is missing depends only on observed variables, not on the missing value itself once those observed variables are taken into account. For example, if we want to estimate a simple wage regression model:

$$wage_i = \beta_0 + \beta_1 \text{education}_i + \beta_2 \text{experience}_i + \beta_3 \text{female}_i + u_i$$

where
	
	wage = hourly wage (USD)
	
	educ = years of education
	
	exper = years of work experience
	
	female = indicator variable (1 = female, 0 = male)

In many labour datasets, education is sometimes missing. However, the probability that education is missing may depend on observed characteristics such as experience or gender. In that situation, the missingness mechanism satisfies the MAR assumption.

#### Below is a dataset of 20 workers. Some values of educ are missing.

| id | wage | educ | exper | female |
| -: | ---: | ---: | ----: | -----: |
|  1 |   22 |   16 |     5 |      0 |
|  2 |   18 |   14 |     3 |      1 |
|  3 |   25 |   NA |     7 |      0 |
|  4 |   20 |   13 |     4 |      1 |
|  5 |   27 |   17 |     8 |      0 |
|  6 |   19 |   NA |     6 |      1 |
|  7 |   24 |   16 |     9 |      0 |
|  8 |   21 |   15 |     5 |      1 |
|  9 |   28 |   18 |    10 |      0 |
| 10 |   23 |   NA |     7 |      1 |
| 11 |   17 |   12 |     2 |      1 |
| 12 |   26 |   17 |     8 |      0 |
| 13 |   22 |   15 |     4 |      0 |
| 14 |   20 |   NA |     5 |      1 |
| 15 |   29 |   19 |    11 |      0 |
| 16 |   18 |   13 |     3 |      1 |
| 17 |   27 |   17 |     9 |      0 |
| 18 |   21 |   14 |     4 |      1 |
| 19 |   30 |   20 |    12 |      0 |
| 20 |   24 |   NA |     6 |      1 |

Here, the variable educ is missing in five observations.
In this dataset, the probability that education is missing depends on observable characteristics such as:

	lower experience levels
	
	some demographic differences
	
However, once we condition on experience and gender, the missingness does not depend on the true education value itself. Thus, the data satisfies the MAR assumption.
A common econometric approach under MAR is multiple imputation, which estimates missing values using regression models based on observed variables.

### Implementation
## R

We first construct the dataset and then perform multiple imputation using the mice package.
```
library(mice)

data <- data.frame(
id = 1:20,
wage = c(22,18,25,20,27,19,24,21,28,23,17,26,22,20,29,18,27,21,30,24),
educ = c(16,14,NA,13,17,NA,16,15,18,NA,12,17,15,NA,19,13,17,14,20,NA),
exper = c(5,3,7,4,8,6,9,5,10,7,2,8,4,5,11,3,9,4,12,6),
female = c(0,1,0,1,0,1,0,1,0,1,1,0,0,1,0,1,0,1,0,1)
)

md.pattern(data)
```

Next, we perform multiple imputation.

```
imp <- mice(data, m = 5, method = "pmm", seed = 123)

completed_data <- complete(imp)
```
Now we estimate the wage regression using the imputed dataset.

```
model <- lm(wage ~ educ + exper + female, data = completed_data)

summary(model)
```

### Interpretation

By imputing the missing education values using the MAR assumption, we avoid discarding observations and retain the full dataset. This improves statistical efficiency and produces more reliable estimates compared to simply removing observations with missing values.
Under the MAR assumption, multiple imputation allows researchers to recover plausible values for missing observations and proceed with standard econometric estimation methods.
