# JavaScript 日期方法指南

> 原文：<https://dev.to/mahmoudelmahdi/javascript-date-methods-guide-345>

## getDate 方法

`getDate()`方法根据本地时间返回给定日期中的某一天(*从 1 到 31* )。

#### 用法

```
/*
 * Representing the day of the month
 * @return {Number} - An integer number, between 1 and 31
 */

const date = new Date()
date.getDate(); 
```

Enter fullscreen mode Exit fullscreen mode

> 浏览器兼容性: *Chrome，Firefox (Gecko)，IE，Opera，Safari*

## getDay 方法

`getDay()`方法根据本地时间返回指定日期的星期几(*从 0 到 6* )，其中(*星期日*为 0，*星期六*为 6)。

#### 用法

```
/*
 * Representing the day of the week
 * @return {Number} - An integer between 0 and 6
 */

const date = new Date()
const weekDays = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];

weekDays[date.getDay()]; // String 
```

Enter fullscreen mode Exit fullscreen mode

> 浏览器兼容性: *Chrome，Firefox (Gecko)，IE，Opera，Safari*

## today testring 法

`toDateString()`方法将 date 对象的日期部分(*而不是时间*)返回为人类可读的形式。

#### 用法

```
/*
 * Representing the Date object in human readable
 * @return {String} - Date portion of the given Date object
 */

const date = new Date()
date.toDateString(); 
```

Enter fullscreen mode Exit fullscreen mode

> 浏览器兼容性: *Chrome，Firefox (Gecko)，IE，Opera，Safari*