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

The dataset manual specifies that entries sharing the same **Info_cluster** label should be grouped during train-test split and cross-validation processes. Analysing the population and class distribution of each Info_cluster label reveals an imbalance; some clusters are solely composed of class -1 entries, while others feature an excessive number of class 1 entries, and some clusters contain significantly more entries than the other. These characteristics introduce 

<div align="center">
  
  <img src="https://github.com/hongd13/Chagas-Disease-Prediction/blob/master/pictures/info_cluster_population.png?raw=true"/>
  
</div>

