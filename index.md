# Finding a MicroRNA Signature for Myocardial Infarction Diagnosis

### What I want to do?

<p style='text-align: justify;'>  
Myocardial infarction is responsible for 85% of mortalities of cardiovascular diseases (CVD), as the leading cause of mortality worldwide. There are different approaches available for MI diagnosis including protein biomarkers. Biomarkers like cardiac troponins (cTn) are guiding treatment decisions for CVD and as a result of improved assay sensitivities, detection of minute changes in cTn levels became possible [2]. Therefore, for the first time, myocardial injury is defined as a different disease from myocardial infarction in the fourth universal definition of MI [3]. A drawback of the higher sensitivity is a higher possibility of false positives. In this respect, exploring new and novel biomarkers seems attractive. On the other hand, since the survival rate in MI cases strongly depends on fast diagnosis and treatment, discovering novel biomarkers for rapid and accurate diagnosis is of great importance [4].
</p>

<p style='text-align: justify;'>   
Noncoding RNAs are under investigation as alternative and complementary biomarker candidates [2]. Amongst noncoding RNAs, microRNAs (miRNAs) are of particular interest, since they regulate key elements of gene expression, and various biological processes [5]. MicroRNAs are small (18–24 nucleotides) single-stranded noncoding RNAs that are of great importance in cardiac pathophysiologies, such as hypertrophy, arrhythmia, and ischemia. Therefore, microRNAs are under special attention as possible CVD biomarkers [6]. Although, further systematic investigations should be conducted on their potential as novel biomarkers for MI diagnosis.
</p>
  
### What I had done before this repository?

<p style='text-align: justify;'> 
In the broader project, we aimed to identify a miRNA signature and manufacture a biosensor for their detection to diagnose MI. This repository is related to the signature identification step. Before this step, The GSE6174 dataset includes the expression profile of 866 miRNAs for 1049 samples, from which 62 samples are acute ST-elevation myocardial infarction cases and 94 samples are healthy controls has been downloaded. In total, 100 differentially expressed miRNAs were detected in samples from MI patients in comparison to healthy controls at an adjusted significance level of p-value < 0.05 and log2 FC < -1 or > 1. Histogram plots of the log2 FC and the adjusted p-values are presented in Fig. 1a, b. From whole differentially expressed miRNAs, 68 were upregulated, while only 32 were downregulated in MI patients. The expression profile for all 100 differentially expressed miRNAs have been extracted for all samples.
</p>
  
### What I'm doing in this repository?

<p style='text-align: justify;'> 
This repository is dedicated to trainig a model for classification of samples with the lowest number of miRNAs as possible. To do so, at the first step, Random Forest and Gradiant Boost classifiers were selected because of thier well-known power in handeling tabular data.
</p>
  
#### Pre-Processing:

<p style='text-align: justify;'> 
The necessary libraries and the data have been loaded, and some minor edits have been made on the data. In the current format, '0' value in the SD column represents Healthy and '1' represents MI.
</p>

```
<p style='text-align: justify;'> 
[**Question 1:**](#Questions:) As mentioned before there are 62 samples are available for MI and 94 samples for healthy group. Is this a imbalence data? Generally what are the criteria for detecting balanced and imbalanced data? and what is the best approch for hadeling this challege?
</p>
```

<p style='text-align: justify;'> 
In the begining the dataframe checked for highly corelataed features (> 0.8 or < -0.8) and nothing has been found. In order to visulizing the expression profile of all miRNAs, a boxplot is represented. ??. As it is clear from the boxplot, the data is not normalized and contain some outliers. 
</p>
                                                                                      
![Expression Profile of 100 differentially Expressed MiRNAs](https://user-images.githubusercontent.com/95024166/155990729-5cddb914-0412-45d8-bf5a-a2ff7dd53e1f.png)

```
<p style='text-align: justify;'> 
> **Question 2:** Based on the boxplot, is normalization and outlier handeling needed?
 </p>
```

#### Training Models:

##### Random Forest:

<p style='text-align: justify;'> 
A basic random forest model has been trained using all 100 miRNAs. A list of 6 miRNAs has been extracted using *feature_importance_* and a random forest model hase been trained with their expression profile. For finding the best hyper parameters for the model a grid search has been conducted. 
</p>



  
## Questions:
```
<p style='text-align: justify;'> 
1. As mentioned before there are 62 samples are available for MI and 94 samples for healthy group. Is this a imbalence data? Generally what are the criteria for detecting balanced and imbalanced data? and what is the best approch for hadeling this challege? </p>

<p style='text-align: justify;'> 
 2. Based on the boxplot, is normalization and outlier handeling needed?
</p>
```


|index|Model|No\. of miRs|test\_precision_macro|train\_precision_macro|test\_recall_macro|train\_recall_macro|test\_f1_macro|train\_f1_macro|test\_accuracy|train\_accuracy|
|---|---|---|---|---|---|---|---|---|---|---|
|0|Basic Random Forest|100|0\.6844300144300144|1\.0|0\.6860119047619048|1\.0|0\.664421101774043|1\.0|0\.7627272727272728|1\.0|
|0|Basic Random Forest|6|0\.7232088744588745|1\.0|0\.7127976190476192|1\.0|0\.7013296580440543|1\.0|0\.7636363636363638|1\.0|
|0|Random Forest Best parameters by Grid Search|100|0\.7426713564213564|1\.0|0\.6869047619047619|1\.0|0\.669257254183725|1\.0|0\.7627272727272729|1\.0|
|0|Random Forest Best parameters by Grid Search|6|0\.7194642857142857|1\.0|0\.7151785714285713|1\.0|0\.7130631868131868|1\.0|0\.7454545454545455|1\.0|
|0|Random Forest Best parameters by Grid Search \(one parameter at a time)|100|0\.6782467532467532|1\.0|0\.6279761904761905|1\.0|0\.6178431372549019|1\.0|0\.7081818181818182|1\.0|
|0|Random Forest Best parameters by Grid Search|6|0\.7194642857142857|1\.0|0\.7151785714285713|1\.0|0\.7130631868131868|1\.0|0\.7454545454545455|1\.0|
|0|Random Forest Nested CV|100|0\.7256313131313131|0\.9311934924182168|0\.7079365079365079|0\.8530518171344784|0\.6995301593831006|0\.8764355668318613|0\.77|0\.8991032776747062|
