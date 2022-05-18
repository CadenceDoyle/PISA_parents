# PISA_parents
Data mining class' final project, examining which parental attributes impacted a child's PISA math assessment
---

## Introduction
The goal of this project was to find out whether a parent’s educational background, spoken language, and involvement in their child’s academics has any influence on their child’s performance on a math assessment. This topic interested me because examining which factors shape child math skills will ultimately help society understand what we must do if we want to increase math ability among young people as a whole. As a math student myself, I understand how math knowledge can provide crucial capabilities for a number of disciplines. Thus I believe it is important for children to grow up learning these skills. Further, if we as a society know what circumstances affect math performance in children, we can provide better resources and instruction to children in order to improve overall math performance among children in the US. In the context of this study, if we can figure out how parents can shape their children’s math abilities, we can make recommendations to parents on how they can help to improve their child’s academic capabilities.

## Data Overview
The Programme for International Student Assessment (PISA) is a standardized way to measure education outcomes. While PISA measures international academic achievement, I looked solely at data from the United States, containing 4,838 rows. I utilized data from the student questionnaire filled out by the student prior to taking the PISA since I wanted to
look particularly at the questions related to a student’s parents. 

The initial data set I used had a total of 1,119 attributes, but I extracted columns from the data whose descriptions had the word “mother” or “father.” This refined data set had only 25 columns in total, but we still needed to account for instances of missing data. I noticed that some of the columns in this data set had only NA values, so I removed all columns that consisted entirely of NA values. I ended up with a data set consisting of 17 columns and 4,838 rows

These columns consist of variables such as the level of education that the mother and father have received, the languages they speak, their involvement in their child’s academics through helping with schoolwork, and their socioeconomic background. Additionally, two variables are dedicated to the mother’s and fathers ISEI, or their International Socio-Economic Index of Occupational Status. This measure on the PISA questionnaire is meant to “[capture] the attributes of occupations that convert parents’ education into income” (OECD, 2003).

Some of the variables involve ISCED qualifications, which are measurements of education levels for the parents. ISCED Level 4 means “post-secondary non-tertiary education,” ISCED Level 5 indicates “short-cycle tertiary education,” and ISCED Level 6 means “Bachelor’s degree or equivalent tertiary education level” (The World Bank, 2021). In particular, ISCED Level 5A indicates a course that will lead to “advanced research programs and professions with high skill requirements,” whereas Level 5B denotes a vocational school (NCES, 2007, p. 62). 

## Methodology 
Joining the parental attributes with the student's math score, we sought to understand how characteristics of their parents can influence a child's math skills. To prepare the dataset, I needed to remove potential multicollinearity issues. 

Once I ran the correlation plot, I noticed that our Usual_Lang_M and Usual_Lang_F variables (usual language the mother and father speak with the children) were very highly correlated with our Lang_Stu_M and Lang_Stu_F variables (language the mother and father speak with other students at school), repectively. I could simply drop Usual_Lang_F and Lang_Stu_M, that would solve both of our colinearity issues. However, I noticed that Lang_Stu_F also has many NAs, so we drop all three columns. 

![Figure 1 - Correlation Plot](https://user-images.githubusercontent.com/105803833/169141564-93e49da8-801e-4110-bd30-d7273db9ad63.JPG)

After going through the data in the columns, I realized the numbers in these fields were actually multiple choice, or yes/no answers, therefore the numbers should in fact be factors. When modeling, I will treat these values as discrete factors rather than continuous numbers. 

To start, I split our data into testing and training data sets, with the training set comprising of 80% of the data. I also set an index for resampling the training data set. Then, I checked our train and test set to ensure they have approximately similar distributions for student scores. From here is where we start our modeling.

## Results

The first model I chose is a Support Vector Machine (SVM) regression. A Support Vector Machine is a generalization of the maximal margin classifier. At first, I tried SVM regression to predict the exact score of the student based on the attributes of the parents. I plot our results, and hope we get a decent OLS regression line.

![Figure 2 - SVM Regression](https://user-images.githubusercontent.com/105803833/169142791-d00738bb-cd6b-4ca2-b756-446a20a1c3b2.JPG)

With an R-squared value of 0.151311, I decided to shift the question. Instead of predicting the exact PISA score, I now want to predict whether a student will perform above or below average based on the characteristics of their parents. I turned the regression question into a classification problem. 

I tested several different classification models to explore which model would give me the best prediction. After running SVM classification, I received an accuracy score of 0.6298, and an F1 score of 0.6312. While these results were not necessarily groundbreaking, they did indicate the question was better suited to a classification model than a regression model. It was after running SVM classification that I decided to test other models to see if I could improve
on that accuracy score. 

Within our classification models, KNN on average performed the worst. The scores for accuracy, precision, recall, and F1 were less than 0.6. Decision trees had the best precision out of all of our models at 0.6505, but had the worst
recall at 0.4926. With such a low recall score, this model had the worst F1 score. I did expect random forest to perform better than decision trees, since random forest aggregates many decision trees. This hypothesis proved
correct, as the random forest model outperformed the decision tree model in all categories except precision.

![Figure 3 - Classification Model Results Table](https://user-images.githubusercontent.com/105803833/169145963-93ebbb78-946a-4ca2-8cda-75257b77c318.JPG)

![Figure 4 - Classification Model Results Table](https://user-images.githubusercontent.com/105803833/169145998-e47a884f-e36a-4b91-85cd-d974f082ddf3.JPG)

![Figure 5 - Classification Model ROC Curves](https://user-images.githubusercontent.com/105803833/169146648-690312c0-bc35-44d5-9fdd-843e50141fa6.JPG)

My resampling uncovered some truly interesting patterns. In each model, I opted to include a 10-fold cross-validation. 10-fold cross validation splits the dataset into 10 groups, or folds. The first fold is treated as a validation set, or “hold out” set, and the algorithm is fit on the remaining 9 folds. I then have 10 validation models which can indicate how the algorithm performs on independent, or unseen, data (James, Witten, Hastie, and Tibshirani, 2021, p. 203 -
205). Figure 7 below demonstrates just how strong the random forest models could be. It is almost startling not to have a model less than 95% accurate when our test data had an accuracy score of 62%.

![Figure 6 - Classification Model Resampling Results Table](https://user-images.githubusercontent.com/105803833/169146664-4e9095cd-fc70-4253-933b-ce4a11cef221.JPG)


While cross-validation is not necessarily an evaluation metric for our models, since it is used entirely on our training data set, it does typically indicate how well a model will perform on an independent data set. The fact that the cross-validation accuracy scores have such a large discrepancy from the test set accuracy scores is unusual. Typically, the disparity between resampling statistics and testing would indicate that the initial test and training/validation sets had very different variances. 

However, I took preemptive measures to counter this possibility. When first splitting the data set with numeric math score, the six number summary looked nearly identical across both testing and training data sets. 

When splitting the data with classification groupings, the proportion of high and low scores were interchangeabl. There is a possibility that the amount of variation across the variables used in the prediction. For example, all low socioeconomic status parents could be in our training dataset and all high socioeconomic status parents could be in the test set. The validation models (resampling) would perform well on the training data but the overall model would perform poorly on the test data.

## Conculsion
Though my classification models were nearly identical in their results, they did perform much better than our regression model. I can conclude that for this dataset, it would be more appropriate to predict whether or not a child performs below or above average in a math assessment based on the parent's educational background, spoken language, and involvement in their child’s academics, rather than estimating the exact score. However, none of the models gave us better than 65% accuracy, which is only slightly better than a flip of a coin. My results imply that parental characteristics alone cannot predict a child’s score on their math exam. There clearly is some correlation between parent’s education, language, and involvement in their childs’ academic pursuits with mathematical ability, given that my models gave over a 50% accuracy score for every model. However, there are likely other factors impacting the child’s score on a math assessment besides their parents.
