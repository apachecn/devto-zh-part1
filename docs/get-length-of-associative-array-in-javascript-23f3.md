# 获取 JavaScript 中关联数组的长度

> 原文：<https://dev.to/adamkdean/get-length-of-associative-array-in-javascript-23f3>

我刚刚学到的一点是 JavaScript 对数组很有趣。除非使用数字索引，否则数组元素实际上会成为底层对象的属性。这很糟糕，但也有点道理。

这意味着如果你有一个多维数组(类似于`array[x][y]`):

```
[
    a: [a, b, c],
    b: [a, b, c],
    c: [a, b, c]
] 
```

Enter fullscreen mode Exit fullscreen mode

它可以像这样建造:

```
var chars = ['a', 'b', 'c'],
    arr = [],
    x, y;

for(x = 0; x < 3; x++) {
    arr[chars[x]] = [];
    for(y = 0; y < 3; y++) {
        arr[chars[x]][chars[y]] = true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后如果你想得到这个数组第一维的长度，`arr.length`是行不通的。你会得到`0`。如果您扩展了`Object`原型，您可以添加一个简单的键计数方法，如下所示:

```
Object.prototype.getLength = function() {
    var len = 0;
    for (var key in this) {
        if (this.hasOwnProperty(key)) {
            len++;
        }
    }  
    return len;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所见，这非常有效:

```
> console.log(arr.length, arr.getLength());

0, 3 
```

Enter fullscreen mode Exit fullscreen mode

另一种可能更安全的方法是将它放入函数中，而不是扩展对象原型:

```
var getAssocArrayCount = function(array) {
    var len = 0;
    for (var key in array) {
        if (array.hasOwnProperty(key)) {
            len++;
        }
    }  
    return len;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后简单地这样称呼它:

```
> console.log(arr.length, getAssocArrayCount(arr));

0, 3 
```

Enter fullscreen mode Exit fullscreen mode