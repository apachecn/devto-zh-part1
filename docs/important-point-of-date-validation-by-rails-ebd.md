# 通过 Rails 进行日期验证的要点

> 原文：<https://dev.to/msky026/important-point-of-date-validation-by-rails-ebd>

我描述了如何排除这种非法日期参数' 1930/2/30 '。

# 问题

它不能像 f.date_select 那样在模型层进行验证。

# TL；速度三角形定位法(dead reckoning)

您不能使用 date_select 辅助方法。

# 为什么

当通过下拉获取年、月和日时，参数存储分为以下三种类型。

```
rails new datetest
rails g scaffold user name:string birthday:date 
```

Enter fullscreen mode Exit fullscreen mode

```
<%= f.date_select :birthday %> 
```

Enter fullscreen mode Exit fullscreen mode

当这个参数被传递给控制器时，它被分成以下参数。

*   params[:生日]["日期(1i)"] #年份
*   params[:生日]["日期(2i)"] #月份
*   params[:生日]["日期(3i)"] #天

在存储到模型中时，这种未授权的数据(1930/2/30)将被自动补偿，并且将是 1930 年 3 月 2 日。

```
fuga = Fuga.new(params)
fuga.birthday # March 2, 1930 
```

Enter fullscreen mode Exit fullscreen mode

# 仍待验证

您可以验证为分割的三个参数(params[:hoge]["date(1i)"]，params[:hoge]["date(2i)"]，params[:hoge])。
然而，这种方法将在控制器中实现。

```
require 'date'
year = params[:birthday]["date(1i)"]
month = params[:birthday]["date(2i)"]
day = params[:birthday]["date(3i)"]
Date.valid_date?(year, month, day) 
```

Enter fullscreen mode Exit fullscreen mode