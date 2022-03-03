---
title: MicroRNA Signature for Myocardial Infarction Diagnosis
---

## What I want to do?

<p style='text-align: justify;'>  
Myocardial infarction is responsible for 85% of mortalities of cardiovascular diseases (CVD), as the leading cause of mortality worldwide. There are different approaches available for MI diagnosis including protein biomarkers. Biomarkers like cardiac troponins (cTn) are guiding treatment decisions for CVD and as a result of improved assay sensitivities, detection of minute changes in cTn levels became possible [2]. Therefore, for the first time, myocardial injury is defined as a different disease from myocardial infarction in the fourth universal definition of MI [3]. A drawback of the higher sensitivity is a higher possibility of false positives. In this respect, exploring new and novel biomarkers seems attractive. On the other hand, since the survival rate in MI cases strongly depends on fast diagnosis and treatment, discovering novel biomarkers for rapid and accurate diagnosis is of great importance [4].
</p>

<p style='text-align: justify;'>   
Noncoding RNAs are under investigation as alternative and complementary biomarker candidates [2]. Amongst noncoding RNAs, microRNAs (miRNAs) are of particular interest, since they regulate key elements of gene expression, and various biological processes [5]. MicroRNAs are small (18–24 nucleotides) single-stranded noncoding RNAs that are of great importance in cardiac pathophysiologies, such as hypertrophy, arrhythmia, and ischemia. Therefore, microRNAs are under special attention as possible CVD biomarkers [6]. Although, further systematic investigations should be conducted on their potential as novel biomarkers for MI diagnosis.
</p>
  
## What I had done before this repository?

<p style='text-align: justify;'> 
In the broader project, we aimed to identify a miRNA signature and manufacture a biosensor for their detection to diagnose MI. This repository is related to the signature identification step. Before this step, The GSE6174 dataset includes the expression profile of 866 miRNAs for 1049 samples, from which 62 samples are acute ST-elevation myocardial infarction cases and 94 samples are healthy controls has been downloaded. In total, 100 differentially expressed miRNAs were detected in samples from MI patients in comparison to healthy controls at an adjusted significance level of p-value < 0.05 and log2 FC < -1 or > 1. Histogram plots of the log2 FC and the adjusted p-values are presented in Fig. 1a, b. From whole differentially expressed miRNAs, 68 were upregulated, while only 32 were downregulated in MI patients. The expression profile for all 100 differentially expressed miRNAs have been extracted for all samples.
</p>
  
## What I'm doing in this repository?

<p style='text-align: justify;'> 
This repository is dedicated to trainig a model for classification of samples with the lowest number of miRNAs as possible. To do so, at the first step, Random Forest and Gradiant Boost classifiers were selected because of thier well-known power in handeling tabular data.
</p>
  
### Pre-Processing:

<p style='text-align: justify;'> 
The necessary libraries and the data have been loaded, and some minor edits have been made on the data. In the current format, '0' value in the SD column represents Healthy and '1' represents MI.
</p>

[**Question 1:**](#Questions:) <code> As mentioned before there are 62 MI and 94 healthy samples. Is this an imbalence data? Generally what are the criteria for detecting balanced and imbalanced data? and what is the best approch for hadeling this challege? </code>


<p style='text-align: justify;'> 
In the begining the dataframe checked for highly corelataed features (> 0.8 or < -0.8) and nothing has been found. In order to visulizing the expression profile of all miRNAs, a boxplot is represented. ??. As it is clear from the boxplot, the data is not normalized and contain some outliers. 
</p>
                                                                                      
![Expression Profile of 100 differentially Expressed MiRNAs](https://user-images.githubusercontent.com/95024166/155990729-5cddb914-0412-45d8-bf5a-a2ff7dd53e1f.png)


 **Question 2:** <code> Based on the boxplot, is normalization and outlier handeling needed? </code>


### Training Models:
  
The data has been split into train and test sets by 0.7:0.3 ratio. All mtrices have been calculated just by the train set.

 **Question 3:** <code> As we will see in the following section cross_validate has been used for model training. When using this method, is it nessesary to split the data to train and test set? It seems that the method does it automatically and returen metrices for both train and test data seperatly. What about nested cross-validation? </code>

  
#### Random Forest:

<p style='text-align: justify;'> 
A basic random forest model has been trained using all 100 miRNAs. A list of 6 miRNAs has with the highest <code> feature_importance_ </code> been extracted and a random forest model hase been trained with their expression profile. For finding the best hyper parameters for the model a grid search has been conducted with the following <code> param_grid </code> on all 100 miRNAs:
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
  Scoring and k for cross validation has been set to accuracy and 10, repectivly. The best parameters for this grid search was n_estimators=100, max_depth=15, max_features=3, criterion='entropy'. Again 6 miRNAs has with the highest <code> feature_importance_ </code> been extracted and a random forest model hase been trained with their expression profile using above-mentioned hyper-parameters.
</p>

<p style='text-align: justify;'> 
In order to test another approch for hyper-parameter tunning, a grid search on the whole dataset with one hyper-parameter in the <code> param_grid </code> at a time, while other parameters were left at their defult value. Every time the best value for the cheking parameter has been passed to a final model and at last, the model were traind with n_estimators=100, max_depth=9, max_features=6, criterion='entropy'. A model with the exact parameters has been trained with 6 miRNAs has with the highest <code> feature_importance_ </code> from the model trained with the whole dataset.
 </p> 


<p style='text-align: justify;'> 
 At last a nested cross-validation has been conducted on the whole dataset with <code> n_splits=5 </code> for inner cv and <code> n_splits=10 </code> for outer cv. The <code> param_grid </code> was as above and the soring for the grid search step was accuracy. 
 </p> 
 
 **Question 4:** <code> Three different approchs have been used for hyper-tunning. Grid search for all parameters of interest at one step, tunning a parameter at a time and nested cross-validation as combination of grid search and cross validation. Which hyper-tunning techniqu is the correct or the better one? </code>
 
<p style='text-align: justify;'> 
  All models have been trained with <code> cross_validate </code> with <code> cv=10 </code> and the following <code> scoring </code>:
 </p> 
 
 ```
 scoring = ['precision_macro', 'recall_macro', 'f1_macro', 'accuracy']
 ```
 <p style='text-align: justify;'> 
 The metices in the scoring for both train and test data is represented in the following table, and also, a visiual comparison between different models scores on the test set is represented in the barplot.
  </p> 
 
|Model|No\. of miRs|test precision macro|train precision macro|test recall macro|train recall macro|test f1 macro|train f1 macro|test accuracy|train accuracy|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Basic Random Forest|100|0\.684|1\.0|0\.686|1\.0|0\.664|1\.0|0\.762|1\.0|
|Basic Random Forest|6|0\.723|1\.0|0\.712|1\.0|0\.701|1\.0|0\.763|1\.0|
|Random Forest Best parameters by Grid Search|100|0\.742|1\.0|0\.686|1\.0|0\.669|1\.0|0\.762|1\.0|
|Random Forest Best parameters by Grid Search|6|0\.719|1\.0|0\.715|1\.0|0\.713|1\.0|0\.745|1\.0|
|Random Forest Best parameters by Grid Search \(one parameter at a time)|100|0\.678|1\.0|0\.627|1\.0|0\.617|1\.0|0\.708|1\.0|
|Random Forest Best parameters by Grid Search \(one parameter at a time)|6|0\.719|1\.0|0\.715|1\.0|0\.713|1\.0|0\.745|1\.0|
|Random Forest Nested CV|100|0\.725|0\.931|0\.707|0\.853|0\.699|0\.876|0\.77|0\.899|

<p align="center">
  <img src="https://user-images.githubusercontent.com/95024166/156600735-1b5af45a-7bc9-446c-bd5a-b11f5f4d090e.png" />
</p>

#### Gradiant Boost:

<p style='text-align: justify;'> 
For gradiant boost algorithm, the exact same procedure as the procedure for random forest has been followed. The <code> param_grid </code> have used for grid search was as follow:
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
 The only difference between random forest and gradiant boost procidures is that in gradiant boost the nested cross-avlidation has not be conducted yet.
  </p> 

|Model|No\. of miRs|test precision macro|train precision macro|test recall macro|train recall macro|test f1 macro|train f1 macro|test accuracy|train accuracy|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Basic Gradiant Boost|100|0\.736|1\.0|0\.684|1\.0|0\.686|1\.0|0\.744|1\.0|
|Basic Gradiant Boost|6|0\.789|1\.0|0\.735|1\.0|0\.733|1\.0|0\.771|1\.0|
|Gradiant Boost Best parameters by Grid Search|100|0\.796|1\.0|0\.715|1\.0|0\.705|1\.0|0\.773|1\.0|
|Gradiant Boost Best parameters by Grid Search|6|0\.671|0\.992|0\.684|0\.984|0\.663|0\.988|0\.715|0\.989|
|Gradiant Boost Best parameters by Grid Search \(one parameter at a time)|100|0\.796|1\.0|0\.745|1\.0|0\.753|1\.0|0\.799|1\.0|
|Gradint Boost Best parameters by Grid Search \(one parameter at a time)|6|0\.662|1\.0|0\.661|1\.0|0\.656|1\.0|0\.708|1\.0|

<p align="center">
  <img src="https://user-images.githubusercontent.com/95024166/156606040-f79b43ad-ded1-40b5-8ab8-0cb78b57e7a9.png" />
</p>

## Discussion:

The first 


## Questions:

1. As mentioned before there are 62 samples are available for MI and 94 samples for healthy group. Is this a imbalence data? Generally what are the criteria for detecting balanced and imbalanced data? and what is the best approch for hadeling this challege?
2. Based on the boxplot, is normalization and outlier handeling needed?
3. As we will see in the following section <code> cross_validate </code> has been used for model training. When using this method, is it nessesary to split the data to train and test set? It seems that the method does it automatically and returen metrices for both train and test data seperatly. What about nested cross-validation?
4. Three different approchs have been used for hyper-tunning. Grid search for all parameters of interest at one step, tunning a parameter at a time and nested cross-validation as combination of grid search and cross validation. Which hyper-tunning techniqu is the correct or the better one?



