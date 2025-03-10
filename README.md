# Vector-born Disease Prediction
**Author**: Duarte Oliper

**Date**: March 2025

## Introduction

This repository contains a [Vector-born Disease Prediction](notebook/exercise.ipynb) notebook. In it, you'll find tasks covering data selection, feature engineering, model development, and performance monitoring. Please reference back to this repository if you take inspiration for one of your projects.

I spent 3-4 hours in Data Loading and Cleaning, 2 hours in Feature Engineering, and 1 hour in Modelling. Making some final adjustments and preparing the documentation for submission took me roughly 2 hours, so roughly **8 hours of total work time**. 

I would need to allocate some extra time to refactor and organize the code into scripts and pipelines, modularizing the code when needed, to facilitate reusage and deployment.

## Dataset
**Dataset:** Tabular dataset for AI-based vector-borne disease prediction

**Link:** https://data.mendeley.com/datasets/cf49v47z4c/1

This French dataset gathers clinical information about patients diagnosed with malaria, dengue, yellow fever, and typhoid fever, as well as patients excluded from these diseases. Therefore, our **target** should correspond to the disease that the patient was diagnosed with, being:
1. Malaria
2. Dengue
3. Yellow Fever
4. Typhoid

The dataset includes 300 records. Among the consultations recorded, 150 are from DAFRA and 150 from DO. The data was preprocessed, and sensitive information such as name, surname, or place of birth was removed. The form contained 109 retained questions. 

The data includes two CSV files:
* "data.csv" - contains the data
* "description.xls" - contains the attribute descriptions
 
Some attributes are present in the description but not in the data, as they were removed due to lack of information.

## Data Cleaning
![Figure 1. Missing values report](/figures/missing_values.png)
*Figure 1. Missing values report*

Missing values were removed through an iterative approach of several steps, detailed in the notebook. At the end of these steps, remaining missing values were replace with mean in the case of numeric, or empty string in the case of categorical.

Similarly, object type variables were carefully analyzed and cleaned to have only numerical, categorical or boolean variables for modelling. It is worth pointing out the case of blood pressure, were fractions needed to be separated into numerator and denominiator to accomodate this condition.

## Feature Engineering
The key conditions found to influence target diseases were diabetes, asthma, pneumonia, cardiac conditions, kidney conditions, sickle cell, liver diseases, and immune system conditions. Therefore, I attempted to capture these in variables through string matching.

## Exploratory Data Analysis (EDA)
Using `ydata_profiling` (docs: https://docs.profiling.ydata.ai/latest/), we gather that there are a lot of variables that highly correlate with the target (see `Alerts`), that we'll need to drop.

We can also see scatter plots of one variable against another and check correlations. This could be used to understand important patterns within the data and try to reduce feature dimensionality if needed.

![Figure 2. Target Category Distribution](/figures/category_distribution.png)

*Figure 2. Target Category Distribution (before upsampling)*

In Figure 2., There are very few cases of yellow fever and typhoid, which will impact performance further ahead. We could target this issue in a few ways:
* Upsample minority classes (most recommended)
* Join minority classes (loss of utility)
* Downsample majority classes (least recommended, dataset is already small)
  
I decided to move on with **upsampling the minority classes**. This was arguably one of the most important steps to improve results in the end. Here are a few important reasons showing **why resampling is used for upsampling minority classes** keep in mind:

**Balance the Dataset:** Many machine learning models perform better when classes are balanced. Upsampling helps avoid bias toward the majority class.

**Keep the Original Data Distribution (Sort Of):** While upsampling duplicates data points, it retains the overall characteristics of the minority class.

## Modelling - Results and Discussion
Considering that the main goal of the model is to predict diseases in a population, it is imperative to **avoid False Negatives (FP)**. Therefore, **Recall (sensitivity)** is particularly important.

I chose to train and compare 3 models based on https://scikit-learn.org/stable/machine_learning_map.html. From these, SVC had the best performance overall. I also used cross validation to improve robustness.

![Figure 3. SVC classification report](/figures/SVC_report_final.png)

*Figure 3. SVC classification report*

In Figure 3, note the test accuracy of 92%, a promising result. The macro averages of F1-score 50% and recall 53%, although not very exciting, could yet be improved through further hyperparameter tuning/optimization. Furthermore, the lack of samples for diseases other than Malaria impacts learning and evaluating these. 

Looking at the confusion matrix, it seems that the few missclassifications that occurred were more pronounced in Malaria, which makes sense as it was the majority class originally, with a wider distribution of variables. 

It's also worth mentioning that the least represent class - typhoid and yellow fever - were practically perfectly identified, which could be a bi-product of upsampling. To get a more representative distribution, more samples would be needed for these specific cases, to ensure the reliability of these results in new scenarios. 

Co-occurring diseases in the same patient may also lead to misslabeling, which should be addressed in a future implementation - maybe by selecting a priority of detection, or adding multiple disease identifications as an option for modelling.


![Figure 2. Feature Importance report](/figures/feature_importance.png)
*Figure 2. Feature Importance report*

It seems that the most relevant features include age and a few symptoms, such as blood pressure, muscle pain, dizziness, and fever.

## Monitoring Considerations

I envision this model being used in an hypothetical hospital setting as a tool to assist in diagnosis. Patient parameters can be introduced from clinical records to detect early signs of specific infectious diseases and automatically trigger an alarm for clinical appointment, preventing further spread of disease.

As mentioned during the report, the recall is an important metric to keep track of, as it may imply that the model does not detect every suspicious case. It should still be balanced with precision, to prevent excessibe alarms. Moving further, it should be ensured that predictions align with the annotated data through continuous monitoring of these metrics.

## Conclusion

The use case I chose allowed me to experiment within a familiar Biomedical Field, while showcasing the data science skills that were the target of this exercise. The results achieved with the SVC model were promising, with a test accuracy of 92%, even without further optimization.

The dataset had a few issues, mainly the class umbalance between Malaria (majority) and the remaining. Also all patients in the dataset were ill, so there was no control group to compare to. My solution also has the limitation of co-occurring diseases not showing in the target, which could be fixed by having a model for each disease - which I chose not to do to keep the solution leaner and easy to follow. 

I would also need to allocate some extra time to refactor and organize the code into scripts and pipelines, modularizing the code when needed, to facilitate reusage and deployment.

## Reference
 Please reference back to this repository if you take inspiration for one of your projects.