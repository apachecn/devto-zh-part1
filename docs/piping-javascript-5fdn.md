# 管道 JavaScript

> 原文：<https://dev.to/kayis/piping-javascript-5fdn>

*[Flickr 上 arbyreed 的封面图片。](https://www.flickr.com/photos/19779889@N00/)T3】*

JavaScript 获得了越来越多的函数式编程特性，一个现有的特性是新的[管道操作符](https://github.com/tc39/proposal-pipeline-operator)。

操作符是`stage-1`所以不会马上包含在标准中，但是你已经可以在[巴别](https://github.com/babel/babel/tree/master/packages/babel-plugin-proposal-pipeline-operator)的帮助下使用它了。

## 为什么

如果你来自面向对象的背景，你可能经常使用点运算符。许多库使用它来实现小型 DSL，通常称为流畅接口，帮助您以良好的可读性完成工作。

例如 [Moment.js](https://momentjs.com/)

```
moment().add(2, "days").substract(10, "hours").toString(); 
```

Enter fullscreen mode Exit fullscreen mode

另一个函数是 [date-fns/fp](https://github.com/date-fns/date-fns/blob/master/docs/fp.md) ，但是正如您所看到的，嵌套的函数调用使它有点难以理解。

```
format("D MMMM YYYY", subHours(10, addDays(2, new Date()))); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果函数代码也具有这种从左到右的可读性，岂不是很好？

## 什么

这就是新管道操作符派上用场的地方，它允许您使这种代码更具可读性。

```
f(10); 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
10 |> f; 
```

Enter fullscreen mode Exit fullscreen mode

所以上面的`date-fns/fp`例子变成了

```
const add2Days = addDays(2);
const sub10Hours = subHours(10);
const customFormat = format("D MMMM YYYY");

new Date() |> add2Days |> sub10Hours |> customFormat; 
```

Enter fullscreen mode Exit fullscreen mode

或者简而言之:

```
new Date()
|> addDays(2)
|> subHours(10)
|> format("D MMMM YYYY"); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，操作符需要一个只需要*一个参数*的函数。当只使用一个参数调用时，`date-fns/fp`函数返回这样的函数。

这也适用于异步函数，因为它们建立在承诺之上，承诺返回一个结果。

```
"USERID_123"
|> await loadUserFromApi // async
|> extractUserImageUrl   // sync
|> await cropUserImage;  // async 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

JavaScript 再次向 FP 方向迈进了一步，并建立在它已经拥有的基础之上。特别是服务器端的数据转换代码将从以这种方式编写的代码中受益。