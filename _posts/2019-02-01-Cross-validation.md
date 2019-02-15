---
title: "Cross-Validation: Why and how to do it"
date: 2019-01-29
tags: [Model Selection, Data Science]
categories: Model-evaluation
comments: false
header:
  overlay_image: "/images/cvimage.jpeg"
  overlay_filter: 0.5
excerpt: "Data Science, Model Selection"
mathjax: "true"
classes: wide
---

One fine day you started working on this data set and you are planning to build a classification model(for the sake of discussion). You did data munging (cleaning, feature engineering, etc.) and you are now ready to split the data for training and testing. For the sake of simplicity, let us assume you have two classes in your data, A and B, with 70 of total data belonging to class A and rest to B. When you divide the data into 80:20 ratio of train and test 
data, random number of samples of class A and B will flow to train and test data(while maintaining the size ratio of train and test as 4:1). What if 65 of data belonging to class A and 5 of class B goes to training set. Training set will be dominated with samples from class A (65) while test set will only have (5). Since our training set has majority of data labeled as class 'A', 93% to be precise, model tries to overfit. When the same model is applied to testing data, which has 25 records labeled as 'B', they will be classified as 'A' resulting in horrible test score.

TL;DR, you split the data wrong and now your model overfit the data. Root cause of this is that we did not take percentages of labels into consideration during the split. Ideally we should same distribution of labels in test/validation set as the distribution in training set.  

## Importance of Validation set

The above mentioned scenario is just one such case where model performs well on training set and does badly on validation/testing set. Overfit or underfit can also occur when we chose an imperfect model to fit data. An imperfect model is something that is designed to work well on the kind of data we have, although it can perform well on other data sets. This happens because algorithms are built based on few assumptions on data formats, and is not supposed to fit any kind of data equally well.  

All our efforts will go in drain after spending lots of time on data cleaning, transformations and model building only to realize that the model is near to useless. We need 
to have some early indicator informing if the model is overfitting. This is where validation accuracy comes into picture and help us identify overfitting before its too late. Low accuracy on validation set upon fitting the model on it is an indicator of high variance in the model and it's time we either go for a different mode or re-engineer the data. We need a model that's good, not the one that looks good.

## Different ways to get CV data sets

sklearn's [`modelselection`][modelsel] class has various subclasses which can do the data partitions very effectively. In case of supervised learning where the test 
data is not provided, the data should be split into three sets for training, validation and testing purpose. If the test set already exists (provided), we only 
need to create training and validation sets.

In this article, I shall briefly discuss about some the commonly used Cross-Validation (CV) objects offered by sklearn and will explain their properties. 
There are three major kinds of splits, Linear, stratified and shuffle split. 

### Linear split

* Data is split based on the ratio passed as hyper-parameter 
* Indexes of data points in subsamples follow same order as that in original data
* Doesn't take distribution of target labels into consideration during split

Example : [`KFold split`][kfold]

```python
from sklearn.model_selection import KFold

n_points = 100
n_splits = 4
X = np.random.randn(100, 10)
"""
	Create sample data with 3 classes and 10 groups (Each sized 10)
"""
percentiles_classes = [.1, .3, .6]
y = np.hstack([[ii] * int(100 * perc)
               for ii, perc in enumerate(percentiles_classes)])

# Evenly spaced groups repeated once
groups = np.hstack([[ii] * 10 for ii in range(10)])

#create CV object and split the data
cv = KFold(n_splits)
cv.split(X=X,y=y, groups=groups)

```



<img src="{{ site.url }}{{ site.baseurl }}/images/cv_post/kfold.PNG" alt="K-fold split">


The obvious pitfall of this way of splitting is that it does not take into consideration the population of various target classes in the data. This could lead to one or more classes not appearing in training set thus enabling model to overfit on labels appearing in training set.

To overcome this we need have something that helps us to split the data according to the percentage of classes 
so that we have it evenly distributed in training and validation data. Stratified split helps us to achieve that.

### Stratified split

As mentioned above, [`stratified split`][skf](stratifiedKFold split to be precise) splits the data by keeping distribution of target variable into consideration. 

* Enhanced K-fold split where split is done while preserving the percentage of samples for each class

```python
# Create cross-validation object
cv_strat_split = StratifiedKFold(n_splits)
cv_strat_split.split(X=X,y=y, groups=groups)

```

<img src="{{ site.url }}{{ site.baseurl }}/images/cv_post/startkfold.PNG" alt="StratifiedK-fold split">


There is still the  issue of splitting data linearly. It would be better if we can somehow split the data while preserving 
the population of classes in training and validation sets.

### Shuffle split

* [`Shuffle split`][shspl] splits the by randomly picking indexes from pool of available data (not in order)
* [`Stratified shuffle split`][sssplit] shuffles the data like above while preserving the percentage of classes 
in the distributed data
* Stratified shuffle split is particularly helpful if there is large disparity between the population of classes in
 the dataset

 ```python

#Shuffle split
cv_shuff_split = ShuffleSplit(n_splits, test_size=.25, random_state=0)
cv_shuff_split.split(X=x)

#Stratified shuffle split
cv_start_shuff = StratifiedShuffleSplit(n_splits, test_size=0.25, random_state=0)
cv_start_shuff.split(X=x)

 ```

<figure class="half">
    <a href="{{ site.url }}{{ site.baseurl }}/images/cv_post/shuffsplit.png"><img src="{{ site.url }}{{ site.baseurl }}/images/cv_post/shuffsplit.png"></a>
    <a href="{{ site.url }}{{ site.baseurl }}/images/cv_post/stratsuff.png"><img src="{{ site.url }}{{ site.baseurl }}/images/cv_post/stratsuff.png"></a>
    <figcaption>Shuffle split (vs) StratifiedShuffle split </figcaption>
</figure>



[pd-doc]: http://pandas.pydata.org/pandas-docs/stable/
[modelsel]: https://scikit-learn.org/stable/modules/classes.html#module-sklearn.model_selection
[kfold]: https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.KFold.html#sklearn.model_selection.KFold
[skf]: https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedKFold.html#sklearn.model_selection.StratifiedKFold
[shspl]: https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.ShuffleSplit.html#sklearn.model_selection.ShuffleSplit
[sssplit]: https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedShuffleSplit.html#sklearn.model_selection.StratifiedShuffleSplit


