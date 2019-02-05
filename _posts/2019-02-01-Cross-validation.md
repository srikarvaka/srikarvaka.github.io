---
title: "Cross-Validation: Why and how to do it"
date: 2019-01-29
tags: [data wrangling, data science, messy data]
categories: Data pre-processing
header:
  image: "/images/cvimage.jpeg"
excerpt: "Data Wrangling, Data Science, Messy Data"
mathjax: "true"
---

One fine day you started working on this data set and you are planning to build a classification model(for the sake of discussion). You did data munging 
(cleaning, feature engineering, etc.) and you are now ready to split the data for training and testing. For the sake of simplicity, let us assume you have
two classes in your data, A and B, with 70 of total data belonging to class A and rest to B. When you divide the data into 80:20 ratio of train and test 
data, random number of samples of class A and B will flow to train and test data(while maintaining the size ratio of train and test as 4:1). What if 65 of 
data belonging to class A and 5 of class B goes to training set. Training set will be dominated with samples from class A (65) while test set will only 
have (5). Since our tarining set has mojority of data labelled as class 'A', 93% to be precise, model tries to overfit. When the same model is applied 
to testing data, which has 25 records labelled as 'B', they will be classified as 'A' resulting in horrible test score.

TL;DR, you split the data wrong and now your model overfit the data. Root cause of this that we did not take distribution of labels into consideration 
druing the split. Ideally we should same distribution of labels in test/validation set as the distribution in training set.  

### Importance of Validation set

The above mentioned scenario is just one such case where model performes well on training set and does badly on validation/testing set. Overfit or underfit 
can also occur when we chose an imperfect model to fit data. An imperfect model is anything that is not supposed to work well on the kind of data we have, 
although it can perfor well on other data sets. This happens because algorithems are built with some specific data format in mind, and is not supposed to 
fit any kind of data equally well.  

All our efforts will go in drain after spending lots of time on data munging and model building only to realize that the model is near to useless. We need 
to have some early indicator informing if the model is overfitting. This is where validation accuracy comes into picture and helps identify overfitting before 
its too late. Low accuracy on validation set upon fitting the model on it is an indicator of overfitting and it's time we either for a different mode or 
engineer the data again.

### Different ways to get CV data sets

sklearn's [`modelselection`][modelsel] class has various subclasses which can do the data partitions very effectively. In case of supervised learning where the test 
data is not provided, the data should be split into three sets for training, validation and testing purpose. If the test set already exists (provided), we only 
need to create training and validation sets.

In this article, I shall briefly discuss about some the commonly used Cross-Validation (CV) objects offered by sklearn and will explain their properties. 
There are majorly three kinds of splits, Linear, shuffle and stratified split. 

#### Linear split

* Data is split based on the ration passed as hyper-parameter 

* Indexes of data points in subsamples follow same order as that in original data

* Doesn't take distribution of target labels into consideration during split

example : [`KFold split`][kfold]

{% highlight python %}

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

{% endhighlight %}


![kfold]({{ site.url }}{{ site.baseurl }}/images/kfold.PNG){: .align-center}


Check text


[pd-doc]: http://pandas.pydata.org/pandas-docs/stable/
[modelsel]: https://scikit-learn.org/stable/modules/classes.html#module-sklearn.model_selection
[kfold]: https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.KFold.html#sklearn.model_selection.KFold
[4]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.map.html
[5]: https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.cut.html
[6]: https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.qcut.html
[7]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.loc.html
[8]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.iloc.html

