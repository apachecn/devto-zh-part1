# JavaScript 中的函数式编程？是的，请吧。

> 原文：<https://dev.to/tiffany/functional-programming-in-javascript-yes-please>

*这篇文章最初是[发表在我的博客](https://tiffanywhite.tech/functional-javascript/)上的*

现在 web 开发界的热门话题之一是 web 语言 JavaScript 中的函数式编程。

函数式编程包含了大量的数学性质和现象，这超出了本文的范围，但是我在这里要解决的是*如何*用名义函数式编程编写一些函数。

这将是一个系列。我目前正在研究这些课题，我的发现让我兴奋。我将深入研究这些主题，一点一点地研究每个数组方法。你可以在史蒂夫·史密斯的博客 [Funky JavaScript](http://funkyjavascript.com/) 上找到更多这方面的内容。

## 重要概念:高阶函数

JavaScript 最好和最差的地方之一是可以将函数传递给其他函数。这可能会导致漂亮的表达代码，有时还会导致错误。

为什么可以这样做？因为，像大多数函数式编程语言中的 <sup id="fnref1">[1](#fn1)</sup> ，*函数是值*，就像 JavaScript 中的任何其他值一样。

取此代码:

```
// Named function or
// function declaration
function double(x) {
  return x * 2;
}

// Anonymous function or
// Function expression
let double = function(x) {
  return x * 2;
}

let cat = double;
// Function call
cat(60); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将函数命名为`double`。它有一个参数，`x`,当你调用它时，它会返回你在函数调用中指定的 x 值。

JavaScript 的不同之处在于，你可以将它传递给一个变量，并从该变量调用函数。这是因为，*函数是值*。

高阶函数有利于构图取较小的函数，使之成为较大的函数。一分钟后会有更多的介绍。

## 回车。过滤器()

`.filter()`函数是一个数组函数，它接受一个回调函数来创建一个新的数组过滤版本。

取这个对象数组:

```
const animals = [
  { name: ‘Catticus Finch’, species: ‘cat’ },
  { name: ‘Peaches’,         species: ‘fish’ },
  { name: ‘Bobby’,          species: ‘dog’ },
  { name: ‘Lucifer’,        species: ‘cat’ },
  { name: ‘Beatrix’,        species: ‘rabbit’ },
  { name: ‘Cerulean’,        species: ‘fish’ }
]; 
```

Enter fullscreen mode Exit fullscreen mode

假设我想过滤掉数组中所有的猫。我们可以使用可靠的`for`循环:

```
const cats = [];

for (let i = 0; i < animals.length; i++) {
  if (animals[i].species === ‘cat’) {
     cats.push(animals[i]);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上只是在数组中循环，对于每一个`for`循环找到的猫，它都将其推入空的`cat`数组。

现在，我们可以*过滤*。

Filter 接受回调，遍历数组中的每一项，并将其传递回回调函数。`.filter()`需要一个布尔值，然后返回过滤后的数组。

```
const cats = animals.filter(function(animal) {
  return animal.species === ‘cats’;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里，如果`animals`数组中的`species`属性的值是一个`cat`，它将在一个过滤后的数组中返回这些猫的名字。

我们也可以编写一个匿名函数，并在其中添加一个过滤函数，就像这样:

```
const isCat = function(animal){
  return animal.species === ‘cats’;
});

const cats = animals.filter(isCat); 
```

Enter fullscreen mode Exit fullscreen mode

## 这有多酷？

编写小函数可以让我们重用。在 for 循环中，我们将 cat 放入数组，但是 filter 本身会处理这个问题。

Filter 和 callback 是在相互啮合的情况下组成的。它不仅在语法上令人愉快，而且代码行*更少*，这总是一件好事。

## 接下来

接下来我想处理一下`.map()`函数，回到我的 Chrome DevTools 系列。

* * *

1.  尽管 JavaScript 不是纯粹的函数式编程语言。 [↩](#fnref1)