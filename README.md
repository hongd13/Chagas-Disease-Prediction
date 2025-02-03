# Chagas-Disease-Prediction

<i> This README file contains a comprehensive summary of the development process, results analysis, and key findings from the Jupyter Notebook. </i>

## Introduction
This machine-learning project aims to produce a 
classification model to detect the presence of Chagas’ disease, using a domain 
centric dataset, containing linear B-cell epitope readings captured by state-of-the-art technology, remarked by its high-dimensional data space and complexity.

## Exploratory Data Analysis and Preliminary Data Pre-processing
The initial inspection of the dataset shows that the dataset consists of 10 information columns, 1281 (1291 - 10) feature variables and 49606 total entries. All feature variables are in the float-64 datatypes, and there is a significant class imbalance within the dataset.

<div align="center">

  |ID |Info_PepID	|Info_organism_id	|Info_protein_id	|Info_pos	|Info_AA	|Info_epitope_id	|Info_nPos	|Info_nNeg	|Info_cluster	|Class	|feat_esm1b_0|...|feat_esm1b_1280|
  |-| -	|-	|-	|-	|-	|-	|-	|-	|-	|-	|-	|-|-|
  |0	|XP_809948.1:15	|5693	|XP_809948.1	|218	|E	|276453	|1	|0	|18	|1	|	0.294185 |... |0.571763|
  |...|...|...|...|...|...|...|...|...|...|...|...|...|
  |49605	|XP_814147.1:10	|5693	|XP_814147.1	|190	|E	|309252	|0	|1	|70 |-1 | 0.097965 |...	| 0.237317|
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/class_distribution.png?raw=true"/>
  
  | Class       | Total Count |Ratio|
  |-------------|-------------|-----|
  | Class 1     | 809         |1    |
  | Class -1    | 48,797      |60   |

</div>

### Dataset splitting with Info_cluster

The dataset manual specifies that entries sharing the same **Info_cluster** label should be grouped during the train-test split and cross-validation processes. Analysing the population and class distribution of each Info_cluster label reveals an imbalance; some clusters are solely composed of class -1 entries, while others feature an excessive number of class 1 entries, and some contain significantly more entries than others. These characteristics and constraints introduce difficulties and complexities that need to be addressed when performing any form of dataset-splitting operations.  

<div align="center">
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/info_cluster_population.png?raw=true"/>
  
</div>

### Missing values

Upon checking for missing values, we see that **`feat_esm1b_148`** is missing in almost all entries (44655 entries) while counts of missing values for every other feature appear to be rather consistent, all ranging from 75 to 88, as shown below. 

<div align="center">

  ||Counts of NaN|
  |-|-|
  |feat_esm1b_148|44655|
  |feat_esm1b_646|88|
  |...|...|
  |feat_esm1b_257|75|
  |...|...|
  |Class|0|
  
</div>

In further investigation, it appears that there is no clear correlation between the **`Class`** distribution and the missing **`feat_esm1b_148`** values. As shown below, roughly 90% of the observations are missing the feature, where no noteworthy correlations between the class labels were visible. Therefore, it is possible to safely dismiss this feature as redundant and remove it.

<div align="center">

  | Class                |   -1  |   1  |
  |----------------------|-------|------|
  | feat_esm1b_148_nan   |       |      |
  | False (not missing)  |  4875 |  76  |
  | True (missing)       | 43922 | 733  |
  
</div>

After removing the 'feat_esm1b_148' column, we inspected the missing values in the remainder of the dataset. As mentioned, the count of missing values in each feature appeared rather consistent. After further investigation, we can see that there are exactly 88 entries that contain missing values, each of them is missing 1215 features exactly, with 4 entries missing 1216 values. Entries missing almost the entire feature values likely do not hold any useful information in them, therefore they are also removed. 

### Data ranges

Upon inspecting the feature ranges, we see that the main body of ranges lies between 1.5 to 3. Where outliers are visible as shown below, this indicates feature scaling may be beneficial. 'Min-Max Scaler' is then applied to bring the data ranges to between 0 to 1, however, the testing later reveals feature scaling introduces performance inconsistencies in dimensionality reduction, the precise performance changes will be explored after fitting the model.  

<div align="center">
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/data_ranges.png?raw=true"/>
  
</div>

### Duplicate entries

No duplicated entries were found.

### Outliers

Outlier was tested on the training set using Isolation Forest. After applying Isolation Forest, roughly 5% of entries were identified as outliers. Since they are a minority, it is viable to simply remove them or leave them, however, quantile capping was tested for its performance. After applying quantile capping on the outliers, the overall data skewness improved by roughly 8%, however, sometimes gets worse. Therefore, decisions will be made based on the outcome, if an improvement was made, the training set is updated, if it got worse, remove the outliers from the training set. 

## Feature reduction

Various dimensionality reductions techniques were explored, including Mutual Information score, PCA and FA.

After applying 'Mutual information' on the features, results show a declining CDF normal distribution. Features are then filtered based on the distribution percentile, where features with MI scores greater than or equal to mean+std are kept, leaving roughly 15% (190) features remaining.  

<div align="center">
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/mi_score_dist.png?raw=true"/>
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/mi_score.png?raw=true"/>
  
</div>

Then PCA was applied. Using the cumulative variance explained ratio, we can see that between 200 to 600 components are most suitable.

<div align="center">
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/evr.png?raw=true"/>
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/pca.png?raw=true"/>
  
</div>

FA is then analysed. The results are rather unsatisfactory as shown below. 

<div align="center">
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/fa1.png?raw=true"/>
  
</div>

This suggests perhaps FA is not the best option in this context. However, recall that data normalisation caused a significant change in the DR analysis. Below is the FA analysis before data normalisation and after the MI filter. It had shown a much more promising result compared to where data is scaled. 

<div align="center">
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/fa2.png?raw=true"/>
  
</div>

## Modelling and assessment

Various models were tested. Their performances were measured and compared, with emphasis on recall rate, specifically on the -1 class. This is due to the consideration of the given problem context, detecting the presence of disease, where failure to identify the disease may result in the death of patients, whereas an overly sensitive model is much more forgivable.  

Each model is tested with grid search and hyperparameter tuning. 

<div align="center">
  
  | ID  | Scalar       | DR                       | Classifier | Sampler  | -1 Recall | 1 Recall |
  |---- |------------- |------------------------- |----------- |--------- |-----------|----------|
  | A   | Min-Max(0-1) | PCA(400)                 | SVC        | NearMiss | 78.1%     | 26%      |
  | B   | None         | KBest MI(200)+PCA(130)   | SVC        | NearMiss | 98.2%     | 18.7%    |
  | C   | Min-Max(0-1) | KBest-MI(150)            | SVC        | NearMiss | 69.8%     | 28.6%    |
  | D   | Min-Max(0-1) | PCA(400)                 | SVC        | NearMiss | 77.6%     | 25.5%    |
  | E   | Min-Max(0-1) | FA(110)                  | SVC        | NearMiss | 93.6%     | 8%       |
  | F   | Min-Max(0-1) | PCA(250)                 | SVC        | None     | 11.9%     | 99.1%    |
  | G   | Min-Max(0-1) | PCA(400)                 | SVC        | NearMiss | 78.1%     | 25.5%    |
  | H   | None         | PCA(400)                 | SVC        | ADASYN   | 37%       | 96.2%    |
  | I   | Min-Max(0-1) | PCA(200)                 | RandForest | NearMiss | 87.2%     | 13.4%    |
  | J   | Min-Max(0-1) | PCA(500)                 | KNN        | NearMiss | 73.1%     | 31.5%    |
  | K   | Min-Max(0-1) | PCA(400)                 | LogiReg    | NearMiss | 95.7%     | 20.6%    |
  | M   | Min-Max(0-1) | PCA(500)                 | MLP        | ADASYN   | 22.8%     | 97.1%    |
  
</div>

## Conclusion and discussion

Choosing a winning solution was difficult. In the end, the model was chosen based on 2 considerations: 
  - -1 Recall
  - Overall model behaviour accountability

As explained before, -1 recall is highly prioritised. However, a model that predicts only -1 is not of any practical use. The model should exhibit the ability to make elaborate predictions better than randomly guessing. Meaning, I am looking for the highest combined recall, while maximising -1 recall.  

While model H has the best performance, it does not exhibit the ideal behaviour. Therefore, the winning model is model B, having the highest -1 recall while retaining some understanding of class 1.
