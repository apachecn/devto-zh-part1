# 每个问题都有另一个解决方案

> 原文：<https://dev.to/mateuszniestroj/every-problem-has-another-solution>

# 许多解决问题的方法

当我处理编程问题时，我倾向于用第一种方法来解决我脑海中的问题。有时候这是可以的，对我来说，这也是解决问题最简单的方法。但是大多数时候这不是最好的也不是唯一的方法。因此，今天我将提出三个简单的问题，并给出三种不同的解决方案，向大家展示总有几种方法可以提供另一种解决方案。

## 1)第一个问题:双字符

我们在这个练习中的目标是找到一个函数，它将一个字符串作为参数，并返回新的字符串，其中每个字符都加倍。

让我们来看看测试用例

```
repeater("Hello") // => "HHeelllloo"
repeater("We've been doubled") // => "WWee''vvee  bbeeeenn  ddoouubblleedd" 
```

Enter fullscreen mode Exit fullscreen mode

解决这个问题的第一种方法是将输入字符串分割成一个数组，然后使用`repeat`函数映射该数组的元素。

```
function repeater(string) {
  return string.split("").map(function(char) {
    return char.repeat(2);
  }).join("");
}
repeater("Hello") // => "HHeelloo" 
```

Enter fullscreen mode Exit fullscreen mode

`String.prototype.repeat`是一个函数，返回被调用的字符串的副本。这些副本等于作为参数传递的整数。
使用 repeat 后，我们的字符串中的元素变成了 doubled string，所以我们将其连接成一个字符串。

另一个解决方案可以是使用 for 循环和`Array.prototype.push`函数的解决方案。

```
function repeater(string) {
  var stringArr = string.split("")
  var result = [];
  for (var i=0; i < stringArr.length; i++) {
    result.push(stringArr[i] + stringArr[i])
  }
  return result.join("")
}

repeater("Hello") // => "HHeelloo" 
```

Enter fullscreen mode Exit fullscreen mode

像在第一个解决方案中一样，我们将输入字符串分割成一个字符数组，然后遍历该数组，对于每个元素，我们将 doubled 元素推送到`result`数组。我们返回连接的结果数组，并得到预期的结果。

解决这个问题的最后一个方法是一行程序。

```
function repeater(string) {
  return string.replace(/./g, "$&$&");
}
repeater("We've been doubled") // => "WWee''vvee  bbeeeenn  ddoouubblleedd" 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用数组时，我们有正则表达式要处理。所以在这里我们使用了`String.protorype.replace`,它将一个正则表达式作为第一个参数，而作为第二个参数，它将一个字符串替换指定为第一个参数的 regexp。

根据 JavaScript docs `$&`模式插入匹配的子串，从而得到我们期望的结果。

## 2)第二个问题:向上计数

我们有字符串，所以让我们取一些数字。

我们的目标是编写一个函数，它接受一个整数参数，并返回一个包含所有整数的数组，按顺序排列，介于 1 和参数之间，假设我们的输入总是大于 0 的有效整数。

```
sequence(5) // [1, 2, 3, 4, 5]
sequence(3) // [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

作为输入，我们有一个整数，作为输出，我们希望有一个数组。所以我们必须找到创建元素序列的方法。

一种方法是使用循环。

```
function sequence(integer) {
  var i = 1;
  var result = [];
  while (i <= integer) {
    result.push(i);
    i++;
  }
  return result;
}
sequence(5) // [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

这次我们使用 while 循环。首先我们创建一个空的结果数组，然后用迭代值设置一些变量。这个变量应该等于 1，因为我们要返回从 1 到函数输入整数的序列。

之后，我们在循环中使用`Array.prototype.push`,直到迭代值等于函数输入。最后，我们返回我们的结果。

在下一个解决方案中，首先，我们用适当数量的空元素创建一个数组。

```
function sequence(number) {
  return Array(number).fill(1).map(function(v, i) {
    return v + i
  });
}
sequence(5) // [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

为了创建空表，我们使用了`Array(number)`,它创建了一些与数值相等的元素。之后，我们使用`Array.prototype.fill`功能。我们传递了 1 作为输入。`fill`将数组元素的值作为第一个参数，所以我们用 1 填充数组，以便通过将迭代值添加到元素值(在我们的例子中是 1)来映射元素。所以在每次迭代后，我们将值增加 1。

第三种解决方案可以用字符串来完成。

```
function sequence(number) {
  return "1".repeat(number).split("").map(function(_,i){
    return ++i;
  });
}
sequence(5) // [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

该解决方案首先随机抽取一个字符串，该字符串重复多次，其中数字是我们的输入。然后，该字符串被按字符分割成一个数组，然后，该数组的每个元素被映射为递增的第一次迭代次数的值。

## 3)第三个问题:名字互换

在此任务中，我们编写了一个函数，它将名字、空格和姓氏作为单个字符串参数传递，并返回一个包含姓氏、逗号、空格和名字的字符串。

```
swapName('Bill Gates') // 'Gates, Bill' 
```

Enter fullscreen mode Exit fullscreen mode

解决这个问题的第一个方法很简单，使用我们之前用过的方法。

```
function swapName(string) {
  var nameArr = string.split("  ");
  return nameArr[1] + ", " + nameArr[0];
}
swapName('Bill Gates') // 'Gates, Bill' 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案将字符串拆分为单词数组，然后简单地返回该字符串的第一个第二个元素，后面跟着逗号，再后面跟着拆分后的字符串的第一个元素。

第二种解决方案使用了`Array.prototype.reverse`功能。

```
function swapName(name) {
  return name.split(' ').reverse().join(', ');
}
swapName('Bill Gates') // 'Gates, Bill' 
```

Enter fullscreen mode Exit fullscreen mode

和第一个例子一样，我们将字符串拆分成单词。因为我们知道在一个数组中只有两个元素，我们可以用`reverse`函数反转它，然后用逗号和空格作为分隔符连接数组元素。

最后一个解决方案类似于第一个。在这种情况下，我们用正则表达式和`String.protoype.match`函数得到我们的数组。

```
function swapName(string) {
  var nameArr = string.match(/\w+/g);
  return `${nameArr[1]}, ${nameArr[0]}`;
}
swapName('Bill Gates') // 'Gates, Bill' 
```

Enter fullscreen mode Exit fullscreen mode

函数将一个正则表达式作为参数，并返回一个包含整个匹配结果的数组。在我们收到一个数组后，我们返回一个包含该数组交换元素的字符串，就像第一个例子一样。

为了增加这个例子的多样性，我使用了 ES6 标准中的模板字符串技术，它允许我们将表达式嵌入到字符串中。

## 总结

这是一个非常简单的问题，有一个非常简单的解决方案，但我希望我能向你们展示，即使是简单的问题也有很少的解决方法。这通常是能让我们学到新东西的方法。