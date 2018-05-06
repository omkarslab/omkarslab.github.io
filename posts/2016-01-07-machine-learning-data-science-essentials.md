---
id: 560
title: 'Machine Learning : Data Science essentials'
date: 2016-01-07T09:13:28+00:00
author: Omkar
layout: post
guid: http://omkarslab.com/?p=560
permalink: /2016/01/07/machine-learning-data-science-essentials/
sfw_pwd:
  - KmPzm05bA0VJ
image: /wp-content/uploads/2016/01/2000px-DBSCAN-density-data.svg_.png
categories:
  - Cloud
tags:
  - Analytics
  - Azure ML
  - Machine Learning
---
Machine Learning is one of the top rated industry trends playing a key role in nearly every vertical. Big Data, and Cloud services being key enablers. This post is aimed at making it easy to explore the abilities of Machine Learning without going into too much detail. What I will be covering is merely the tip of ice-berg. Follow through, and feel free to post your feedback in comments, or get back to me on <a href="https://twitter.com/omtalk" target="_blank">twitter</a>.

### **The Big Data pipeline**

There is a simple pipeline proposed through which Data is processed and churned to produce meaningful / conclusive results.

  1. **Acquire Data:** Gather any information that is being generated from all available sources. These could be log files, SQL databases, Document storage, Excel sheets.
  2. **Extract, Clean, Annotate:** Extract all relevant data from the pool, clean it off erroneous or anomalous entries and  annotate or label the data appropriately.
  3. **Integrate, Aggregate, Represent:** Carry out necessary correlations and present the data in shape to suit the architecture. For e.g: flatten the data in a CSV or SQL.
  4. **Analysis / Modelling:** Run the purified data through a modelling algorithm.
  5. **Evaluate:** Compare the results with real world information to evaluate the accuracy of the model.

This pipeline is pretty standard. Anyone with a Computer Science background and enough exposure would intuitively follow a pipeline of this fashion.

### **The principle of Machine Learning
  
** 

Traditionally, we have been using computers or machine to produce a output (_O_) based on input (_I_). The relation between _O_ and _I_ is defined by _f_.

where, _O = f(I)_

Machine Learning is about using computers to understand the relation between _I_ and _O_, and produce _f_. This is what _modelling_ is about. There are a few modelling algorithms that help produce _f_ that we&#8217;ll take a look at next.

**Classification**
  
When the data is being used to create a model that predicts a category for the observation, Classification algorithms are used. Each observation is a set of vectors, and each parameter is weighed individually by the algorithm. Some sample scenarios would be identifying a Chair, Cat or a Car based on a picture. Sample data would include pictures labelled with Chair, Cat or Car.

  * Minimizing classifications errors can be a hard task.
  * Classification algorithms are susceptible to _imbalanced data_. A few observations for a Chair, and many observations for a Car in the training data will rarely predict a Chair.
  * Yes/No or Boolean (or with 2 categories) classification are done using  _Decision trees_ or _Binomial classification _algorithms.
  * Dataset with more than 2 categories are popularly modelled using _Multi-class classification_.

The quality of a classification model can be plotted on the _True Positive Rate_ (_TPR_) i.e. number of positives rated my the algorithm as positives divided by total number of positives; against False Positive Rates (FPR) i.e. total number of negatives classified by the algorithm as positives divided by the total number of negatives. This creates the _Receiver Operator Characteristic_ (_ROC_). The area under this curve is used to denote the accuracy of the model. The linear line represents 50% accuracy. More area under the curve denotes higher accuracy.

<img class="aligncenter size-full wp-image-572" src="http://omkarslab.com/wp-content/uploads/2016/01/sqr.png" alt="ROC" width="369" height="333" srcset="http://omkarslab.com/wp-content/uploads/2016/01/sqr.png 369w, http://omkarslab.com/wp-content/uploads/2016/01/sqr-300x271.png 300w" sizes="(max-width: 369px) 100vw, 369px" />

**Regression**
  
Regression operates on observations that predict numerical values. For example, temperature based on observations of city, date, time, humidity. It is necessary to handle over-fitting and under-fitting of the model against supplied observations.

  * When evaluating the training data with test data, it is important to note the difference between _f(I)_ and actual _O_. So, _[O &#8211; f(I)]_ should be close to zero.
  * Computer calculations work better with smooth functions, as opposed to absolute values. Hence, _[O &#8211; f(I)]<sup>2</sup>_ should be close to zero.
  * When this is applied to training and test data the summation of all errors ?_[O &#8211; f(I)]<sup>2</sup>_ also known as _sum of squares error_ (_SSE_) should be close to zero.
  * Regression algorithms minimize the _SSE_ by adjusting values of baseline variables in the function.

The choice of Regression algorithms is very critical. The choice not only depends on the nature of _O_, but also the relation and distribution of _O_. Some popular regression algorithms would be:
  
<strong style="line-height: 1.5;">Simple Linear Regression:</strong> <span style="line-height: 1.5;">A simple linear regression model has a single factored </span><em style="line-height: 1.5;">I</em><span style="line-height: 1.5;">. Usually used when finding a relation between two continuous datasets.</span>

  1. **Ridge Regression:** Ridge Regression is used when dealing with multiple _I&#8217;s_. This method is susceptible to over-fitting. Especially when dealing with large number of parameters.
  2. **SVM Regression:** Support Vector Machine Regression uses threshold ranges to give a zero error. The error grows in a linear fashion beyond threshold.

The two types of validations for Regression algorithms.

  1. **Cross-validation:** With cross-validation, n-folds of dataset are created and a model is trained on n-1 folds. The model is then tested on the 1 remaining fold. This process is repeated to test with n-folds.
  2. **Nested cross-validation:** Popular for tuning parameters, especially tricky ones. This process is simply following Cross-validation for every possibility of parameter K.

To sum it up, a good regression model is neither under-fitted nor over-fitted to training data. A good model is one that is simple and suits the data in a reasonable manner. Some error tolerances are accounted for.
  
<img class="aligncenter size-medium_large wp-image-568" src="http://omkarslab.com/wp-content/uploads/2016/01/fitting-768x215.png" alt="An Ideal Model" width="768" height="215" srcset="http://omkarslab.com/wp-content/uploads/2016/01/fitting-768x215.png 768w, http://omkarslab.com/wp-content/uploads/2016/01/fitting-300x84.png 300w, http://omkarslab.com/wp-content/uploads/2016/01/fitting-1024x286.png 1024w, http://omkarslab.com/wp-content/uploads/2016/01/fitting.png 1083w" sizes="(max-width: 768px) 100vw, 768px" />

**Clustering
  
** As the name suggests, clustering is used to group similar observations together. Examples being, grouping customers with similar buying behaviours. Most scenarios of clustering lack ground truth, making it very difficult to validate during application. The only way to track ground truth, is based on test data.

  1. **K-means clustering:** The K-means algorithm accepts the number of clusters to be created, and simulates a clustering behaviour on the observations based on randomly selected centers. As the simulation progresses, the centers move towards actual centers of newly forming clusters. This is the most popular clustering algorithm.
  2. **Hierarchical Agglomerative Clustering:** This algorithms accepts points in their own cluster, and the simulation grows these clusters based the distance between two closest points.

In all clustering algorithms, distance metrics play a very important role and have a huge impact on the result. User adaptive distance metrics that consider local density of the data are important.

**Recommendation System**
  
The recommender system uses matrix factorization. It is primarily used to recommend items to a user based on the user&#8217;s own behaviour and the behaviour of users falling the similar category. We won&#8217;t delve into the details of recommender algorithms as there are a wide variety of approaches, each suiting their own application.

This ends a a very brief overview of Machine Learning algorithms. Feel free to post your feedback or discussions in comments, or get in touch on twitter.

**Useful links**

  * <a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf" target="_blank">Machine Learning Cheat sheet for Azure ML<br /> </a>
  * <a href="https://courses.edx.org/courses/course-v1:Microsoft+DAT203x+3T2015/info" target="_blank">EDX Course: Machine Learning Essentials and Azure ML</a>

&nbsp;

&nbsp;