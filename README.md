Data supported recommendations for school boards:
Research question: Do literacy rates have any correlation with book bans?

1. Data
Banned Books
8th grade literacy
4th grade literacy

2. Method
Problem identification
Data wrangling and cleaning
Exploratory analysis
Descriptive summary statistics
Data visualizations
Correlation matrices
Machine learning models
Regression (linear, logistic, multivariate)
Classification (random forest, kmeans, support vector machines)
Model performance metrics and comparison
Predictive analysis
Recommendations
3. Data cleaning
Problem 1: Book ban dates are in different formats
Solution: I converted all dates to one common format. For entries without specific dates listed, I used the midpoint date of the season or year listed. For “Fall 2021” I used November 7th, 2021, as this is the midway point between the start and end of Fall. I then converted this to a datetime object like the entries with specific dates given.

Problem 2: Combining two datasets, one of bookbans and one of reading scores, some districts were named differently than in the other dataset
Solution: I standardized capitalization and spelling of commonly used district names. Then I referenced ID number, district, and state to be sure of matching districts then changed the district name to match before merging datasets.

Problem 3: Some district proficiency scores seem to be misrecorded
Solution: With such a large dataset (all public school districts in the country), I chose to drop any districts that had non-numeric and negative reading assessment scores recorded. As I was not using math assessments, I kept districts where math scores may have been wrong but dropped the column for math scores.

4. Exploratory data analysis

5. Developing and comparing algorithms

Model	r2	MAE	MSE	avg_cv
0	mean_train	0.000000	0.299667	0.149833	-2.480000e-04
1	mean_test	-0.000637	0.312088	0.156044	-2.697000e-03
2	lr_tr	0.885923	0.058184	0.017093	-3.265740e+23
3	lr_te	0.878168	0.061115	0.019011	7.875750e-01
4	ppln_lr_tr	0.871842	0.061202	0.019202	7.670300e-01
5	ppln_lr_te	0.862477	0.064859	0.021460	7.490550e-01
6	ppln_kbest_tr	0.882158	0.056304	0.017657	7.743630e-01
7	ppln_kbest_te	0.876071	0.058888	0.019338	7.526110e-01
8	rnd_for_reg_tr	0.999937	0.040559	0.866197	9.993520e-01
9	rnd_for_reg_te	0.999881	0.077457	1.789218	9.978910e-01
10	log_reg_tr	0.091146	34.096198	12458.836097	8.561610e-01
11	log_reg_te	0.999881	0.077457	1.789218	8.391340e-01

model	accuracy	precision	recall	f1	cv
0	random_forest_class_tr	1.000000	1.000000	1.000000	1.000000	1.000000
1	random_forest_class_te	1.000000	1.000000	1.000000	1.000000	1.000000
2	svc_tr	0.885923	0.999439	0.985724	0.987723	0.987448
3	svc_te	0.984610	0.999546	0.981705	0.990545	0.976549
Based on the analysis of each model's performance along with some logical decisions about which best fit the data and research question, the model that I chose to use was a support vector machine.

Tuning hyperparameters improved the model. The following were the best hyperparameters for this model:

{'C': 1, 'gamma': 1, 'kernel': 'rbf'} SVC(C=1, gamma=1)

The final model:
model = make_pipeline(

StandardScaler(),
LinearSVC(dual=False, C=1, verbose = 3)
)

6. Machine learning modeling
Modeling scenario 1:
Is a school district with higher than average reading proficiency more likely to have book bans than a district with lower reading proficiency?

Result: Regardless of how high (or low) I made input average reading proficiency, the algorithm did not result in a book ban. The proportion of the dataset with book bans appears to too small to output a predicted ban (or at least very rarely).

Modeling scenario 2:
Does the number of students in a district have any correlation with book bans?

Result: Yes! A theoretical school district having 50,000 students was the first hypothetical scenario with increasing numbers of students which output a book ban. The number of students has a positive correlation with book bans. That means that the greater the number of students in a district, the more likely our model is to predict that that district will have at least one book ban. It appears that 50,000 students is approximately the threshold at which the algorithm predicts a book ban.

Modeling scenario 3:
Are some of the largest school districts in the country likely to have book bans?

Result: Yes, it appears as though some of the largest districts do have book bans, both according to our model and according to the data. Using data from some of the 20 largest real school districts in our country, the algorithm predicted at least one book ban for each district. However, when these results are compared against our book ban data, we find that only 1 of those 4 districts did in fact have banned books. This is a reminder that, while predictive algorithms can be useful, the insights from these models are not always accurate. However, one in four correct predictions is significantly better than random chance in predicting whether a district will have a book ban, and therefore, I call this modeling scenario slightly successful.

7. Conclusions and recommendations
a. Slight positive correlation between reading proficiency and book bans
This means that a district with at least one book ban tends to have higher than average student reading proficiency rates compared with districts without any book bans.

b. Larger school districts are more likely to have at least one book ban
District size, in terms of number of students, is positively correlated with book bans. Books are more frequently banned in districts with a large number of student than in districts with smaller numbers of students.

c. Recommendations for school boards and administrators
Without further research, we cannot make conclusions on cause or effect. However, I would recommend conducting further research to determine what additional variables might affect both literacy rates and whether a district bans books. Importantly, I do not recommend banning books, as this data does not suggest that this causes any impact on reading literacy. I suspect that extraneous variables are more likely to cause increases in both book bans and reading proficiency rates rather than either being the cause of an increase in the other. As larger school districts are more likely to have book bans without necessarily having higher than average reading proficiency rates, I would not recommend increasing school district sizes. However, without more information that might offer causal insights, I would not necessarily recommend decreasing district sizes. Further research would be required in order to offer such recommendations.

8. Future work
Before beginning this research, I imagined that were there any correlation, it would be a negative one. That is to say that I hypothized that where more books were banned there might be lower than average reading proficiency. However, my research indicates the opposite. I cannot make any larger predictions or stronger guesses about causation, but these findings do leave me curious to look more closely and to examine how these results may change when adding additional variables to the analysis.

For future research, I would include reading proficiency over a longer time period to establish broader patterns for each district. I would also be interested in examining other possibly relevant variables, particularly economic environment, political views, religious views, parental involvement, and graduation rates for each district. If I included all these factors, I could develop algorithms to examine which, if any, variable(s) most correlate with book bans. This may allow for seeing a more complete picture. It might be that reading rates and book bans correlate only because they are a reflection of other patterns in school districts. More research would be necessary to discover whether this could be the case.

One possible first step in examining possible extraneous variables would be to return to the original dataset for reading proficiency and look at the variables for student demographics for each district. I decided to exclude those variables in this project in order to focus on the features I was considering in the research question and to remain in the scope of the intended project. However, this may be a great way to add issues of poverty, race, and other possible social and societal variables to this research. Considering whether these sorts of demographics reflect difference in district with book bans and also different than average reading proficiency, we might be able to develop algorithms that offer stronger recommendations for school boards and administrators.
