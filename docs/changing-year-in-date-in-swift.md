# 在 Swift 中更改年份日期

> 原文：<https://dev.to/onmyway133/changing-year-in-date-in-swift>

原帖[https://github.com/onmyway133/blog/issues/54](https://github.com/onmyway133/blog/issues/54)

今天我试图在 Swift 中将一个`Date`对象的年份改为 2000 年。

```
let date = Date() 
```

Enter fullscreen mode Exit fullscreen mode

首先，我用`date(bySetting:)`试了试，但去年它不起作用。它简单地返回零

```
Calendar.current.date(bySetting: .year, value: 2000, of: date) 
```

Enter fullscreen mode Exit fullscreen mode

其次，我用`dateComponents`试了试。`component.year`变了，但是`calendar`还是返回原来的日期，很奇怪！！。无论我使用什么时区和日历，它仍然有这个问题

```
var component = calendar.dateComponents(in: TimeZone.current, from: base)
component.year = year
Calendar.current.date(from: component) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我试着说得更明白些，这很管用ðÿž‰

```
var component = calendar.dateComponents([.year, .month, .day, .hour, .minute, .second], from: base)
component.year = year
Calendar.current.date(from: component) 
```

Enter fullscreen mode Exit fullscreen mode