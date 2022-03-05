---
title: MicroRNA Signature for Myocardial Infarction Diagnosis
---

## What I want to do?

<p style='text-align: justify;'>  
Myocardial infarction is responsible for 85% of mortalities of cardiovascular diseases (CVD), as the leading cause of mortality worldwide. There are different approaches available for MI diagnosis including protein biomarkers. Biomarkers like cardiac troponins (cTn) are guiding treatment decisions for CVD and as a result of improved assay sensitivities, detection of minute changes in cTn levels became possible <a href="https://pubmed.ncbi.nlm.nih.gov/32470385/">[1]</a>. Therefore, for the first time, myocardial injury is defined as a different disease from myocardial infarction in the fourth universal definition of MI <a href="https://www.acc.org/latest-in-cardiology/ten-points-to-remember/2018/08/24/00/09/fourth-universal-definition-of-mi-esc-2018#:~:text=The%20current%20(fourth)%20Universal%20Definition,is%20defined%20as%20myocardial%20injury.">[2]</a>. A drawback of the higher sensitivity is a higher possibility of false positives. In this respect, exploring new and novel biomarkers seems attractive. On the other hand, since the survival rate in MI cases strongly depends on fast diagnosis and treatment, discovering novel biomarkers for rapid and accurate diagnosis is of great importance <a href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2843115/">[3]</a>.
</p>

<p style='text-align: justify;'>   
Noncoding RNAs are under investigation as alternative and complementary biomarker candidates <a href="https://pubmed.ncbi.nlm.nih.gov/32470385/">[1]</a>. Amongst noncoding RNAs, microRNAs (miRNAs) are of particular interest, since they regulate key elements of gene expression, and various biological processes <a href="https://pubmed.ncbi.nlm.nih.gov/32517814/">[4]</a>. MicroRNAs are small (18–24 nucleotides) single-stranded noncoding RNAs that are of great importance in cardiac pathophysiologies, such as hypertrophy, arrhythmia, and ischemia. Therefore, microRNAs are under special attention as possible CVD biomarkers <a href="https://pubmed.ncbi.nlm.nih.gov/31782762/">[5]</a>. Although, further systematic investigations should be conducted on their potential as novel biomarkers for MI diagnosis.
</p>
  
## What I had done before this repository?

<p style='text-align: justify;'> 
In the broader project, we aimed to identify a miRNA signature and manufacture a biosensor for their detection to diagnose MI. This repository is related to the signature identification step. Before this step, The GSE6174 dataset includes the expression profile of 866 miRNAs for 1049 samples, from which 62 samples are acute ST-elevation myocardial infarction cases and 94 samples are healthy controls has been downloaded. In total, 100 differentially expressed miRNAs were detected in samples from MI patients in comparison to healthy controls at an adjusted significance level of p-value < 0.05 and log2 FC < -1 or > 1. <a href="#Hist"> Histogram plots of the log2 FC and the adjusted p-values </a> are presented. From whole differentially expressed miRNAs, 68 were upregulated, while only 32 were downregulated in MI patients. The expression profile for all 100 differentially expressed miRNAs have been extracted for all samples.
</p>
 
<p align="center">
  <img id="Hist" src="https://user-images.githubusercontent.com/95024166/156620677-3358bb76-04eb-437c-b4cd-c95f796bca43.png" />
</p>

## What I'm doing in this repository?

<p style='text-align: justify;'> 
This repository is about training a model for the classification of samples with the lowest number of miRNAs possible. To do so, at the first step, Random Forest and Gradient Boost classifiers were selected because of their well-known power in handling tabular data.
</p>
  
### Pre-Processing:

<p style='text-align: justify;'> 
 The necessary libraries and the data have been loaded, and some minor edits have been made to the data. In the current format, the '0' value in the SD column represents Healthy and '1' represents MI. were selected because of their well-known power in handling tabular data.
</p>

<a href="#Questions"> **Question 1:** </a> <code> As mentioned before there are 62 samples are available for MI and 94 samples for the healthy group. Is this an imbalance data? Generally, what are the criteria for detecting balanced and imbalanced data? and what is the best approach for handling this challenge? </code>


<p style='text-align: justify;'> 
In the beginning, the dataframe checked for highly correlated features (> 0.8 or < -0.8) and nothing has been found. To visualize the expression profile of all miRNAs, a boxplot is represented. ??. As it is clear from the <a href="#boxplot"> boxplot </a>, the data is not normalized and contain some outliers. 
</p>

<p align="center">
  <img id="boxplot" src="https://user-images.githubusercontent.com/95024166/155990729-5cddb914-0412-45d8-bf5a-a2ff7dd53e1f.png" />
</p>

<a href="#Questions"> **Question 2:** </a> <code> Based on the <a href="#RF"> boxplot, </a> is normalization and outlier handeling needed? </code>


### Training Models:
  
The data has been split into train and test sets by 0.7:0.3 ratio. All matrices have been calculated just by the train set.

 <a href="#Questions"> **Question 3:** </a> <code> As we will see in the following section cross_validate has been used for model training. When using this method, is it necessary to split the data to train and test set? It seems that the method does it automatically and return matrices for both train and test data separately. What about nested cross-validation? </code>

  
#### Random Forest:

<p style='text-align: justify;'> 
A basic random forest model has been trained using all 100 miRNAs. A list of 6 miRNAs with the highest <code> feature_importance_ </code> has been extracted and a random forest model has been trained with their expression profile. For finding the best hyperparameters for the model a grid search has been conducted with the following <code> param_grid </code> on all 100 miRNAs:
</p>

```
param_grid = {
    'n_estimators' : [50, 100, 200, 300],
    'criterion' : ['gini', 'entropy'],
    'max_depth' : [3, 6, 9, 15],
    'max_features' : [2, 3, 4, 5, 6]
}
```

<p style='text-align: justify;'> 
  Scoring and k for <code> cross validation </code> has been set to accuracy and 10, repectivly. The best parameters for this grid search was n_estimators=100, max_depth=15, max_features=3, criterion='entropy'. Again 6 miRNAs has with the highest <code> feature_importance_ </code> been extracted and a random forest model hase been trained with their expression profile using above-mentioned hyperparameters.
</p>

<p style='text-align: justify;'> 
To test another approach for hyperparameter tuning, a grid search has been conducted on the whole dataset with one hyperparameter tuning at a time (while other parameters were left at their default value). Every time the best value for the checking parameter has been passed to a final model and at last, the model was trained with n_estimators=100, max_depth=9, max_features=6, criterion='entropy'. A model with the exact parameters has been trained with 6 miRNAs with the highest <code> feature_importance_ </code> from the model trained with the whole dataset.
 </p> 


<p style='text-align: justify;'> 
 At last a nested cross-validation has been conducted on the whole dataset with <code> n_splits=5 </code> for inner cv and <code> n_splits=10 </code> for outer cv. The <code> param_grid </code> was as above and the soring for the grid search step was accuracy. 
 </p> 
 
<a href="#Questions"> **Question 4:** </a> <code> Three different approaches have been used for hyper-tunning. Grid search for all parameters of interest at one step, tunning a parameter at a time, and nested cross-validation as the combination of grid search and cross-validation. Which hyper-tunning technique is the correct or the better one? </code>
 
<p style='text-align: justify;'> 
  All models have been trained with <code> cross_validate </code> with <code> cv=10 </code> and the following <code> scoring </code>:
 </p> 
 
 ```
 scoring = ['precision_macro', 'recall_macro', 'f1_macro', 'accuracy']
 ```
 <p style='text-align: justify;'> 
 The metices in the scoring for both train and test data is represented in the following table, and also, a visiual comparison between different models scores on the test set is represented in the following <a href="#RF"> barplot </a>.
  </p> 



|Model|No\. of miRs|test precision macro|train precision macro|test recall macro|train recall macro|test f1 macro|train f1 macro|test accuracy|train accuracy|
|:---:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|Basic Random Forest|100|0\.684|1\.0|0\.686|1\.0|0\.664|1\.0|0\.762|1\.0|
|Basic Random Forest|6|0\.723|1\.0|0\.712|1\.0|0\.701|1\.0|0\.763|1\.0|
|Random Forest Best parameters by Grid Search|100|0\.742|1\.0|0\.686|1\.0|0\.669|1\.0|0\.762|1\.0|
|Random Forest Best parameters by Grid Search|6|0\.719|1\.0|0\.715|1\.0|0\.713|1\.0|0\.745|1\.0|
|Random Forest Best parameters by Grid Search \(one parameter at a time)|100|0\.678|1\.0|0\.627|1\.0|0\.617|1\.0|0\.708|1\.0|
|Random Forest Best parameters by Grid Search \(one parameter at a time)|6|0\.719|1\.0|0\.715|1\.0|0\.713|1\.0|0\.745|1\.0|
|Random Forest Nested CV|100|0\.725|0\.931|0\.707|0\.853|0\.699|0\.876|0\.77|0\.899|

  
<p align="center">
  <img id="RF" src="https://user-images.githubusercontent.com/95024166/156600735-1b5af45a-7bc9-446c-bd5a-b11f5f4d090e.png" />
</p>

#### Gradiant Boost:

<p style='text-align: justify;'> 
For the gradient boost algorithm, the same procedure as the procedure for random forest has been followed. The <code> param_grid </code> have used for grid search was as follow:
  </p> 
  
  
```
param_grid = {
    'loss' : ['deviance', 'exponential'],
    'learning_rate' : [0.01, 0.1, 0.5],
    'n_estimators' : [50, 100, 200, 300],
    'criterion' : ['friedman_mse', 'squared_error', 'mse', 'mae'],
    'max_depth' : [3, 6, 9, 15],
    'max_features' : [2, 3, 4, 5, 6],
}
```

<p style='text-align: justify;'> 
  The grid search best parameters for the method that whole parameters are searching together were criterion='mae', max_depth=3, max_features=5, n_estimators=300, learning_rate=0.5, loss='exponential' and for grid search for one parameter at a time were criterion='friedman_mse', max_depth=3, max_features=3, n_estimators=200, learning_rate=0.1, loss='deviance'.
  </p> 
  
<p style='text-align: justify;'> 
 The only difference between random forest and gradient boost procedures is that in gradient boost the nested cross-validation has not been conducted yet. The results and a visual comparison of it are represented in the following table and <a href="#GB"> barplot </a>.
  </p> 

|Model|No\. of miRs|test precision macro|train precision macro|test recall macro|train recall macro|test f1 macro|train f1 macro|test accuracy|train accuracy|
|:---:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|Basic Gradiant Boost|100|0\.736|1\.0|0\.684|1\.0|0\.686|1\.0|0\.744|1\.0|
|Basic Gradiant Boost|6|0\.789|1\.0|0\.735|1\.0|0\.733|1\.0|0\.771|1\.0|
|Gradiant Boost Best parameters by Grid Search|100|0\.796|1\.0|0\.715|1\.0|0\.705|1\.0|0\.773|1\.0|
|Gradiant Boost Best parameters by Grid Search|6|0\.671|0\.992|0\.684|0\.984|0\.663|0\.988|0\.715|0\.989|
|Gradiant Boost Best parameters by Grid Search \(one parameter at a time)|100|0\.796|1\.0|0\.745|1\.0|0\.753|1\.0|0\.799|1\.0|
|Gradint Boost Best parameters by Grid Search \(one parameter at a time)|6|0\.662|1\.0|0\.661|1\.0|0\.656|1\.0|0\.708|1\.0|

<p align="center">
  <img id="GB" src="https://user-images.githubusercontent.com/95024166/156606040-f79b43ad-ded1-40b5-8ab8-0cb78b57e7a9.png" />
</p>

## Discussion:

<p style='text-align: justify;'> 
  The first drawback of all models is the very high <b> overfitting </b> in all models. It would be because of imbalanced data. Therefore, I should ask:
</p>

<a href="#Questions"> **Question 5:** </a> <code> What are the reasons for overfitting in all models? What is the solution to overcome this challenge? </code>
 
<p style='text-align: justify;'> 
 As it is clear from the <a href="#RF"> barplot </a> for random forest algorithm's matrices, in all three models which have been trained using all 100 miRNAs and 6 miRNAs with the highest <code> feature_importance_ </code>, the model trained by fewer features had the better performance. In contrast, it's except for the basic gradient boost model, the two other models had better performance using all features (<a href="#GB"> barplot </a> for gradient boost algorithm's matrices). Hence, another question arises:
  </p>

<a href="#Questions"> **Question 6:** </a> <code> Is it the best way to select features by their importance score? What about using more advanced feature selection techniques like wrapper? </code>

<p style='text-align: justify;'> 
  Moreover, using grid search for hyper tuning the random forest model lead to even lower scores in all matrices, while it is the opposite in gradient boost models. The only exception in nested cross-validation for the random forest that leads to better performance.
 </p>
 
<a href="#Questions"> **Question 7:** </a> <code> Why does grid search leads to even worth performance on random forest algorithm? Is it because of the values that are selected as the search area? And which of these three approaches is the best for hyper tuning? What about other techniques? </code>


## Questions:
<div id="Questions">
</div>

1. As mentioned before there are 62 samples are available for MI and 94 samples for the healthy group. Is this an imbalance data? Generally, what are the criteria for detecting balanced and imbalanced data? and what is the best approach for handling this challenge?
2. Based on the <a href="#boxplot"> boxplot </a>, is normalization and outlier handeling needed?
3. As we will see in the following section <code> cross_validate </code> has been used for model training. When using this method, is it necessary to split the data to train and test set? It seems that the method does it automatically and return matrices for both train and test data separately. What about nested cross-validation?
4. Three different approaches have been used for hyper-tunning. Grid search for all parameters of interest at one step, tunning a parameter at a time, and nested cross-validation as the combination of grid search and cross-validation. Which hyper-tunning technique is the correct or the better one?
5. What are the reasons of <b> overfitting </b> in all models? What is the solution to overcome this challenge?
6. Is it the best way to select features by their importance score? What about using more advanced feature selection techniques like <em> wrapper </em>?
7. Why does grid search leads to even worth performance on random forest algorithm? Is it because of the values that are selected as the search area? And which of these three approaches is the best for hyper tuning? What about other techniques?



