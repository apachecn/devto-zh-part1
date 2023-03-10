# 如何用对象制作一个 JavaScript 数组的真实副本(没有引用)

> 原文：<https://dev.to/rsschouwenaar/how-to-make-a-real-copy-of-a-javascript-array-with-objects-without-a-reference-5md>

经常会发生这样的情况，你想要复制一个变量，带有一个对象或数组，来操作数据并在以后使用它。但是，当我们创建一个用户数组并在一个新变量中创建一个副本时，就像这样:

```
var originalObject = [
  {"first":"Gretchen","last":"Kuphal","email":"greenwolf54@gmail.com","address":"416 Lesch Road","created":"March 1, 2012","balance":"$9,782.26"},
{"first":"Morton","last":"Mayer","email":"salmonsquirrel25@gmail.com","address":"1602 Bernhard Parkway","created":"April 29, 2017","balance":"$6,596.11"},
{"first":"Catalina","last":"Daugherty","email":"Catalina.Daugherty@filomena.name","address":"11893 Kali Vista","created":"October 16, 2008","balance":"$6,372.86"},
{"first":"Orpha","last":"Heaney","email":"turquoisewolf22@gmail.com","address":"8090 Chris Stream","created":"November 21, 2015","balance":"$9,596.26"},
{"first":"Reva","last":"Mohr","email":"Reva.Mohr@oda.net","address":"0291 Kailyn Stravenue","created":"November 6, 2014","balance":"$4,768.37"},
{"first":"Loma","last":"Keeling","email":"turquoisegiraffe09@gmail.com","address":"84460 Samson Knoll","created":"June 13, 2017","balance":"$9,361.16"}
];

var duplicateObject = originalObject; 
```

它将保存从“**重复对象**到“**原始对象**的引用。这也称为浅拷贝。

为了向您展示它将保留其引用，我们将更改“ **originalObject** ”中第一个用户的名字。

```
originalObject[0].first = "Ray"; 
```

然后记录“**原始对象**和“**重复对象**，结果会显示两者都被更改！很多情况下，你都不希望这种事情发生！！

#### 那么我们如何解决这个问题呢？

我们可以用 JSON.stringify 和 JSON.parse 方法来实现。

```
var duplicateObject = JSON.parse(JSON.stringify( originalObject )); 
```

如果我们现在更改 originalObject 中第一个用户的名字，副本将不会更改！

```
originalObject[0].first = "Ray"; 
```

通过控制台查看，记录“**原始对象**”和“**重复对象**”；

*最初发表于* [*雷蒙·肖韦纳尔*](https://www.raymonschouwenaar.nl/make-real-copy-javascript-array-objects-without-reference/) *。*