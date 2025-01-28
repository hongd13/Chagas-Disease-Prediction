# Chagas-Disease-Prediction

<i> This README file contains a comprehensive summary of the development process, results analysis, and key findings from the Jupyter Notebook. </i>

## Introduction
This machine-learning project aims to produce a 
classification model to detect the presence of Chagas’ disease, using a domain 
centric dataset, containing linear B-cell epitope readings captured by state-of-the-art technology, remarked by its high-dimensional data space and complexity.

## Exploratory Data Analysis and Preliminary Data Pre-processing
The initial inspection of the dataset shows that the dataset consists of 10 information columns, 1281 (1291 - 10) feature variables and 49606 total entries. All feature variables are in the float-64 datatypes, and there is a significant class imbalance within the dataset.
| Class       | Total Count |Ratio|
|-------------|-------------|-----|
| Class 1     | 809         |1|
| Class -1    | 48,797      |60|
