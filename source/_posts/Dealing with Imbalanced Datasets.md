---
title: Dealing with Imbalanced Datasets
url: deal-imbalanced-data
date: 2021-05-07 13:54:00
description: 不平衡数据集处理
categories: Computer Science
tags: [Machine Learning]
---

## Motivation
The Imbalanced Datasets are very common in our life such as illegal users or illness check. The machine learning model always performs bad on these datasets if there are no specific dealings, especially the prediction accuracy of minority class. For example, if the data is highly imbalanced such as 9995(negative):5(positive), then if your model just let every instance to be negative and you can get an acc of 99.95% but the result is meaningless. Another example is that misclassifying the minority is very severe. Assume that you misclassify the patient as normal. Oh my god!

So researchers proposed two kinds of methods for this problem:

 - Cost Sensitive Learning  
When **training** your model, it will give different classes different weights in the **loss function** thus let the model focus more on the minority class. In sklearn, there are `class_weight` and `sample_weight` for you. For `class_weight`, you can specify the weights for different classes such as `{0:0.1,1:0.9}` or you can set it to `balanced` then weights will be computed by $\frac{\#samples}{\#classes\ *\ np.bincount(y)}$. For `fit(sample_weight=)`, you give **every instance** different weights. When computing the loss for the instance, it will be `class_weight` * `sample_weight` * `loss`.
 - Sampling  
Sampling means that we will change the original dataset rather than giving them different weights.

## Sampling Methods
Over-sampling means to increment the minority class.

 - Random Over Sampling  
To sample from minority class with replacement to let the number of each class is 1:1. Overfitting on minority class.
 - Synthetic Minority Oversampling Technique (SMOTE)  
$$
x_{new}=x_i+\lambda(x_{zi}-x_i)
$$
First you find the `k_neighbors` of $x_i$ in the minority class, then just select one $x_{zi}$ randomly and produce the new one. There are some variants such as borderline SMOTE, SVM SMOTE and KMeans SMOTE.
 - Adaptive Synthetic (ADASYN)  
The difference between SMOTE and ADASYN is that SMOTE will generate new samples for random minority data until 1:1. But ADASYN will automatically decide the number of new points generated for each $x_i$. There will be more points generated if there are more majority data around $x_i$.

Under-sampling means to decrease the majority class.

 - RUS  
Data waste.
 - 

## Example
