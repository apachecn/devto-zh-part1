# “R 图书馆将在 2018 年帮助您学习数据科学”

> 原文：<https://dev.to/lornamariak/r-libraries-to-aid-you-to-learn-data-science-in-2018-53oc>

2018 已经来了！2017 年是怎样的一年！对于今年晚些时候开始学习数据科学的人来说，这一年感觉很短。R 学习曲线可能看起来很陡，但是持续接触不同的工具和库/包可以让你的体验更简单。
在本文中，我将与您分享数据科学不同分支下的 R 包，这些包使我的学习之旅迄今为止都是值得的。

### 数据可视化

This is a very instrumental part of data science, for a data science newbie the ability to create great visualizations gives you the hope that you are on the right track.With great data visualizations comes a sense of appreciation for your work especially from none data scientists.The following packages will come in handy while visualizing in R.

#### 1.ggplot2

This is an R package that a makes all that work of visualization much easy. It is known as the grammar of graphics and will take care of plotting details, has different graphical options and does great graph layering.
It is available on CRAN. Here is a great ggplot2 cheat sheet to get you started: [ggplot2 cheat sheet](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)

#### 闪亮

This an R package that gives users the power to explore dashboards and web apps.Shiny helps a lot with data collection and manipulation in real time as it handles reactivity in a great way.shiny apps can make use of HTML widgets, CSS themes and javascript actions to interface with R scripts.It is an awesome library for someone interested in data storytelling on their website.
shiny is available on CRAN.

### 数据争论

One of the goals of every data scientist should be maximizing the data analysis time.To achieve this one needs to ensure the data they are working with is as clean as possible and can be subjected to manipulation easily.Data wrangling is the process of cleaning up data, removing redundancy and organizing it in a way that makes analysis much easier.The following packages are great and simple data wrangling tools.

#### 1.tidyr

From the tidyr [website](http://tidyr.tidyverse.org/),tidy data is defined as data where
*   每个变量都在一列中。*   每个观察值都是一行。*   Each value is a cell. tidyr makes use of simple verbs as R functions like gather()to carry out quick data tidying operations on large datasets. tidyr is available on CRAN.

    #### 2.dplyr

    While dealing with data, there are common manipulations that have to be carried out and dplyr helps solve these by providing verb functions to carry out these manipulations.This helps you filter your data and carry out operations that can group the data for deeper meaning. dplyr is s available on CRAN.

    ### 数据挖掘

    This is one of the biggest challenges for data science newbies.Although very many websites are full of open data sets and are free, It is also an accomplishing feeling for a data science newbie to learn how to extract a data set from the numerous sources of information on and off the web. The following libraries will do the magic:

    #### 1.httr

    This package will enable you access data via modern web APIs. It makes use of HTTP verb functions, requests return JSON data that can be parsed as R objects and it supports Oauth. This makes it easy for a newbie working with APIs in R. This package is available on CRAN

    #### 2.rvest

    An R package for web scraping. It reads HTML docs through URLs, selects parts of the document using the CSS selectors and parses HTML tables as data frames in R. This package is available on CRAN

    ### 结论

    数据科学的最初几天可能会有点混乱，但是专注于这些分支中的每一个可以帮助您逐步理解数据科学。祝你 2018 年学习愉快。不要停止学习。
    请随时通过 twitter [@lornamariak](https://twitter.com/lornamariak) 联系我。我很乐意帮忙，并给予一些宣传/支持。编码快乐！