# 问题解决过程

> 原文：<https://dev.to/mateuszniestroj/problem-solving-process>

# 解决问题的过程

在这篇文章中，我想向你展示一个解决问题的过程，叫做“PEDAC”问题解决过程。

这个过程中的一个原则是在我们开始写代码之前花一些时间来解决这个问题。

“PEDAC”过程的主要步骤有:
1)了解 **P** 问题
2) **E** 示例/测试用例
3) **D** 数据结构和算法
4) **C** ode

让我们在实践中尝试这些步骤。我将用三个例子来说明这个过程。

## 1)旋转矩阵

**问题** : *写一个函数，把任意一个矩阵作为数组，顺时针旋转 90 度*

### 第一步——了解问题:

我们流程的第一步是理解问题。这里我们要做的第一件事是定义什么是输入和输出。

输入:数组的数组
输出:数组的旋转数组

但这实际上意味着什么呢？

让我们来看一些矩阵:

3 4 1
9 7 5
6 2 8

当我们把这样一个矩阵轮廓旋转 90 度，我们会得到

6 9 3
2 7 4
8 5 1

当我们把矩阵写成一个数组时，我们会有:
[[3，4，1]，[9，7，5]，[6，2，8]] = > [[6，9，3]，[2，7，4]，[8，5，1]]
用这个例子我可以写一些规则:

规则/要求:

*   矩阵是一个嵌套数组的数组，嵌套数组是矩阵的行
*   输入矩阵的第一行成为输出矩阵的最后一列
*   嵌套数组具有相同的长度
*   矩阵的列由具有相同索引的嵌套数组中的元素构成
*   输出矩阵的列数等于输入矩阵的行数
*   输出矩阵的行数等于矩阵数组的长度
*   最后一个嵌套数组中的元素成为每个嵌套输出数组的第一个元素

### 第二步-示例和测试用例:

```
 rotate90( [[3, 4, 1],[9, 7, 5],[6, 2, 8]])  // => [[6, 9, 3],[2, 7, 4],[8, 5, 1]]
 rotate90([[3, 7, 4, 2], [5, 1, 0, 8]]);  // =>  [[5, 3], [1, 7], [0, 4], [8, 2]] 
```

Enter fullscreen mode Exit fullscreen mode

测试用例帮助我们澄清一个理解问题。我们应该问一些关于边缘情况的问题。举个例子，我们的第二个例子不是一个正方形矩阵。

### 第三步-数据结构和算法

在这个问题中，我们的数据结构是在问题描述中定义的。这将是一个数组的数组。

所以是时候写一些算法了。

算法:

*   创建长度等于嵌套数组长度的结果数组
*   用空数组填充结果数组
*   对于输入数组长度-1 到 0，取嵌套数组
    *   获取当前数组的元素，并将其元素推送到结果矩阵的每个嵌套数组中
    *   取第一个数字，并将其推入第一个嵌套数组
    *   获取第二个数组，并将其推至第二个嵌套数组

我解决这个问题的算法不是分成小的细节步骤。
算法步骤应该尽可能详细到你需要解决的问题。
如果你不知道如何解决其中的一些步骤，试着简单地做这些步骤，并把它分成更简单的步骤

### 第四步-代码

```
function rotate90(matrix) {
  var result = [];

 for (var row = 0; row < matrix[0].length; row++) {
    result.push([]);
  }

  for (var i = matrix.length-1; i >= 0; i--) {
    for (var j = 0; j < matrix[i].length; j++) {
      result[j].push(matrix[i][j]);
    }
  }

  return result;
}

var matrix1 = [
  [3, 4, 1],
  [9, 7, 5],
  [6, 2, 8]
]

var matrix2 = [
  [3, 7, 4, 2],
  [5, 1, 0, 8]
]

var newMatrix1 = rotate90(matrix1);
var newMatrix2 = rotate90(matrix2);
var newMatrix3 = rotate90(rotate90(rotate90(rotate90(matrix2))));

console.log(newMatrix1); // [[6, 9, 3], [2, 7, 4], [8, 5, 1]]
console.log(newMatrix2); // [[5, 3], [1, 7], [0, 4], [8, 2]]
console.log(newMatrix3); // matrix2 
```

Enter fullscreen mode Exit fullscreen mode

对于算法中的第一点，我创建了一个空数组，然后用 For 循环将一些空数组推送到结果数组中。嵌套空数组的数量等于嵌套数组的长度。为结果矩阵创建了一个框架。

下一步是第二个 for 循环，然后是另一个嵌套循环，该循环从输入矩阵中取出一个嵌套数组，并将该元素推送到每个结果嵌套数组中。

最后一步返回一个结果数组。

## 2) 1000 光

**问题**:你面前有一排开关，从 1 到 n，每个开关都连接到一个最初关闭的灯上。你走过那排开关，拨动每一个开关。你回到开始，在第二遍，你拨动开关 2，4，6，等等。在第三遍时，你再次回到开始，拨动开关 3、6、9 等等。你重复这个过程，并继续下去，直到你已经通过 n 次重复。

写一个程序，它接受一个参数，一个开关的总数，并在 n 次重复后返回一个灯的数组。

### 第一步——理解问题

Input:一个整数
Output:灯亮的数量的数组；

规则:

*   开始时所有灯都熄灭
*   有 n 次重复，其中 n 是灯的数量
*   在第一轮，我们打开所有的灯
*   在第二次运行中，我们从第一个灯到最后一个灯切换 2 的幂的灯
*   在第三次运行中，我们切换的灯是 3 的下一次幂
*   在 n 轮中，我们切换 n 次方的灯

### 第二步-示例/测试用例

```
console.log(lightsOn(5)); // [1,4];
console.log(lightsOn(100)); // [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]; 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:数据结构/算法

为了解决这个问题，我们使用数组

根据最后一条规则，我们知道我们总是打开与那一轮的能量相等的光。我们的输出数组将包含自然数的下次方，直到我们的输入数。

基于这一点，我对这个问题算法是这样的:

*   创建一个长度为自然数个数的数组，上至输入数
*   用随机数填充数组
*   映射该阵列
    *   获取元素的每个索引
    *   将索引增加 1
    *   指数的回归力
*   返回一个数组

### 第四步:编码

首先，我创建了一个灯光阵列。我得到输入数字的平方根，并向下取整。返回给我数组中的一些元素。然后我用随机数填充它。

```
var lights = Array(Math.floor(Math.sqrt(number))).fill(0); 
```

Enter fullscreen mode Exit fullscreen mode

下一步是映射我的数组。在每次迭代中，我首先增加索引号。让我得到自然数，并得到这个数的幂。

```
return lights.map(function(element, index) {
    index += 1;
    return Math.pow(index, 2)    
  }); 
```

Enter fullscreen mode Exit fullscreen mode

我返回映射的结果，并得到问题的结果。

```
function lightsOn(number) {
  var lights = Array(Math.floor(Math.sqrt(number))).fill(0);
  return lights.map(function(element, index) {
    index += 1;
    return Math.pow(index, 2)    
  });
}

console.log(lightsOn(100)); // => [1, 4, 9, 16, 25, 36, 49, 64, 81, 100] 
```

Enter fullscreen mode Exit fullscreen mode

## 3)斐波那契数(记忆化)

我的第三个问题是记忆化的斐波那契算法。

编写一个计算第 n 个斐波那契数的递归函数，其中 n 是该函数的一个参数。使用记忆来提高性能。

### 第一步:理解问题

斐波纳契数列是从 1 和 1 开始的数字序列，其中每个数字是前两个数字的和。所以第三个元素等于 2，第四个元素等于 3，依此类推。记忆是保存先前计算的答案以备再次使用的过程。

输入:位于斐波那契数列中整数；记忆早期输出的缓存。

Output:一个整数，它是输入位置的数值

规则:

*   第一个和第二个元素等于 1
*   下一个元素等于前两个元素之和
*   保存计算出的答案以重复使用

### 第二步:示例/测试用例

```
fibonacci(1)   // 1
fibonacci(2)   // 1
fibonacci(3)   // 2
fibonacci(4)   // 3
fibonacci(5)   // 5
fibonacci(12)  // 144
fibonacci(20)  // 6765 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:数据结构/算法

为了解决这个问题，我使用了数组和递归。
递归函数是一开始对我来说相当复杂的概念。
递归函数是调用自身的函数，具有结束条件，并且该函数的结果用于下一步。

算法:

*   检查输入缓存是否包含一些元素
    *   如果为 false，则创建一个空缓存
    *   如果为真，则将输入缓存分配给新变量
*   检查缓存中是否有输入位置的编号
    *   如果为真，则返回该数字
*   否则，检查该位置是否小于 3
    *   如果为真，则返回 1；
*   否则，对位置减少 1 和相同函数减少 2 的相同函数求和
*   将上述结果保存到缓存中
*   返回称为缓存的位置

### 第四步:编码

在我的算法的第一步，我检查输入缓存是否有一些内容，并重新分配缓存变量

```
cache = cache || []; 
```

Enter fullscreen mode Exit fullscreen mode

我会检查缓存中是否有被调用函数的位置的编号。

```
 if (cache[position]) {
    return cache[position];
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果缓存中没有该元素函数，则移动 1，并检查位置是否小于 3。我们知道斐波纳契数列的前两个元素是 1 和 1。

```
if (position < 3) {
      return 1; 
```

Enter fullscreen mode Exit fullscreen mode

如果位置小于 3，那么函数返回 1。

如果位置大于 2，下一步就是递归。所以这个函数用位置参数减 1 来调用自己，然后用位置减 2 来调用自己。
斐波那契函数的结束条件是返回 1 的位置小于 3。所以函数调用自己，直到子函数返回 1。然后，它对结果求和，并将其分配给我们的缓存数组。

```
if (position < 3) {
      return 1;
    } else {
      cache[position] = fibonacci(position-1, cache) + fibonacci(position-2, cache);
    } 
```

Enter fullscreen mode Exit fullscreen mode

完整代码是这样的:

```
function Fibonacci(position, cache) {
  cache = cache || [];
  if (cache[position]) {
    return cache[position];
  }
  else {
    if (position < 3) {
      return 1;
    } else {
      cache[position] = fibonacci(position-1, cache) + fibonacci(position-2, cache);
    }
  }
  return cache[position];  
}

console.log(fibonacci(1));  // 1
console.log(fibonacci(2));  // 1
console.log(fibonacci(3));  // 2
console.log(fibonacci(4));  // 3
console.log(fibonacci(5));  // 5
console.log(fibonacci(12));  // 144
console.log(fibonacci(20));  // 6765 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

这就是全部的 PEDAC 过程。不要急着写你的代码。
花时间了解你的问题，写一些测试案例、边缘案例、例子来理清你的理解。如果你很好地理解了一个问题，选择适当的数据结构并编写一个算法来帮助你写代码。按照你的理解要求尽量详细。然后开始写代码。

那是一篇相当长的文章。希望对解决你的程序员问题有所帮助。