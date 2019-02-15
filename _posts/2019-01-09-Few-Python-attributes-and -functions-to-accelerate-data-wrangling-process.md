---
title: "Useful Python attributes and functions to accelerate data wrangling"
date: 2019-01-12
tags: [data wrangling, data science, messy data]
categories: Data pre-processing
comments: true
header:
  overlay_image: "/images/code.png"
  overlay_filter: 0.5
excerpt: "Data Wrangling, Data Science, Messy Data"
mathjax: "true"
---

Building a model that can well generalize the given data can be a challenging task. There is no single approach to get the best working model given the data.
A widely popular approach is to start working on our data, make it more meaningful and then choose a model that will best suite the current data. 

One of the crucial step in building a predictive model is data wrangling. It involves cleaning the data, transforming or creating new features for better results.
Pandas library offers many built in functions and attributes that can come handy during this step. These below mentioned functions or attributes are commonly used 
during data wrangling. There are many more functions in pandas that can help us to work on data, these can be refered in official [pandas documentation][pd-doc]. 

We will divide this into three section based on where these functions are commonly used

1. Column trasformation
2. Feature creation
3. Selection of specific data



<div class="notice">
  <h4>Note</h4>
  <p>For the sake of discussion, we will be using dummy data and we shall call the dataframe as df. All the coding will be done using Python</p>
</div>

### Column transformation



Columns in a dataframe can be casted to different data type using [`astype(dtype)`][1] function on the dataframe. This will be handy when the default data type 
of the source data is wrong and there is a need to change the dtype. Another useful function is [`replace(x,y)`][2] which when applied  to a column will replace all 
the 'x' values with 'y'. In place filling null or missing values on a column can be done using [`fillna(val)`][3] function fills all the missing values with the 
function argument.

{% highlight python %}
#=> 'col' is any column which will be converted to type int

#Cast the column type to int (or any other type)
df['col'] = df['col'].astype(int)

#replace some values with others
#all the 'x' values will be replaced with 'y'
df['col'] = df['col'].replace(x,y)

#fill missing values
df['col'] = df['col'].fillna(val)

{% endhighlight %}


### Feature creation

Very often during predictive modelling features from raw data do not usually fit the requirements of model we are trying to build. For example while building 
a linear regression model for binary classification, algorithm  expects the data to be in integer format. So all the columns should have integer data. Some 
of the features, like for example gender, will have strings in them. Thus we need to create new features to map the values to integers. In case of gender, 
we can create two new features 'is_male' and 'is_female' which contains boolean values. 

Some of the most commonly used functions that help with feature creation are mentioned below. [`map(fn/dict)`][4] takes a function or a dictionary as an argument 
and will apply the function, if the argument is a function, or map the 'key' data to 'value' data in case of dictionary. This is particularly useful when we 
want iterate over data and bin them or remove values based on certain conditions. This is hands down one of the most useful funtion that every data scientist 
or a data analyst should know.

Other useful functions are [`pd.cut()`][5] and [`pd.qcut()`][6] which bins the columns based on number bins or quintiles. They are very handy when we want to bin 
the column values based on requirement or their distribution. Some of the common examples are binning of ages, Salaries, ticket price etc. 

{% highlight python %}
#=> 'col' is any column which will be converted to type int

"""
    will apply passed function/dict to each element in df
    fn => An user defined function
    dict => items in 'keys' will me mapped(converted) to 'values'
"""
df['col'] = df['col'].map(fn)
df['col'] = df['col'].map(dict)

#Simillar to map and applies user defined/OOTB function(fn)
df['col'] = df['col'].apply(fn)

#binning the values
#qcut will cut the values on quantile basis
df['col2'] = pd.cut(df['col1'], bins, labels=None)
df['col2'] = pd.qcut(df['col1'], q, labels=None)

{% endhighlight %}

### Selection of specific data

We may sometimes need to select a subset of data based on a condition to either modify or to do a basic data analysis. Pandas has [`df.loc[]`][7] and [`df.iloc[]`][8] 
which takes labels and indexes as input respectively and access elements that match the given input.

{% highlight python %}

#Returns a series of booleans for index that matches given condition
#example
df['col'] < 100

#Returns all the rows of data frame that matches given expression
#example
df[df['col'] < 100]

#Access a group of rows and columns by label(s) or a boolean array
#'row' -> list of rows that will be matched and returned (rows are matched with exact row names and not the index)
#'col' -> list of cols you want from df
df.loc[row, col]

#similar to above but rows are matched picked based not index. Obviously only expects integers for row indexes
df.iloc[row, col] 

{% endhighlight %}

## Conclusion

Pandas is a well-developed and well maintained library that offers many functions to help us during data analysis. Above mentioned functions and attributes are 
frequently used among the variety of available methods. Being familiar with them will greately improve the efficiency and accelerate data wrangling process.

[pd-doc]: http://pandas.pydata.org/pandas-docs/stable/
[1]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.astype.html
[2]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.replace.html
[3]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.fillna.html
[4]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.map.html
[5]: https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.cut.html
[6]: https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.qcut.html
[7]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.loc.html
[8]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.iloc.html

