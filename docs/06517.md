# JavaScript 范围

> 原文：<https://dev.to/kayis/javascript-scopes>

如果你在 ES2015 发布之前就开始使用 JavaScript，你可能已经习惯了这样一个事实，JavaScript 变量是函数范围的并且*被提升*等等..借助 ES2015，您获得了一种定义块范围变量的新方法。

# es 2015 年之前

回到过去，`var`是定义局部变量的关键字，如果你不使用`var`并使用新的标识符赋值，你可以创建一个全局变量，如果你不使用`"use strict";`语句，有时甚至是偶然的。

```
function () {
    // Global variable
    newIdentifier = 5;

    // Local variable
    var variable = 6;

    // Assignment to the defined variable
    variable = 8;

    // Another global variable (by accident)
    varialbe = 8; 
} 
```

Enter fullscreen mode Exit fullscreen mode

您的局部变量由定义它们的函数限定范围。

```
// You could write something like this:
function (x) {
    if(x > 2) {
    var y = 10 * x
    }

    return y
}

// But behind the scenes it would be treated as this:
function (x) {
    var y
    if(x > 2) {
    y = 10 * x
    }

    return y
} 
```

Enter fullscreen mode Exit fullscreen mode

这导致许多开发人员将所有的局部变量都定义在函数的顶部，因为它们无论如何都会出现在那里。

# es 2015 年以来

随着 ES2015 的发布，JavaScript 获得了许多新功能，其中之一是*块作用域变量*。其中有两种，`let`和`const`变量。

```
// So this wouldn't work:
function (x) {
    if(x > 2) {
    let y = 10 * x
    }

    return y
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`y`只能在 if 块中访问，这是许多其他语言的默认行为。

这允许你在需要的地方定义变量，并用代码块来限定它们的范围。

```
// Every time you see a { and } you're creating a new block
// and in there you can create a new variable scoped to that block
while(...) {
    let x = 10;
}

for(...) {
    let x = 12;
}

if (...) {
    let x = 9;
} else {
    let x = 8;
}

try {
    let x = 1;
} catch (e) {
    let x = 99;
} 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以单独使用`{}`来确定作用域，以使变量尽可能地局部化。

```
function () {
    let varForTheWholeFunction = 10;

    {
    let scopedVar = getData();
    varForTheWholeFunction = parse(scopedVar);
    }
    // scopedVar isn't accessible here
    ...
    {
    let scopedVar = filterData(varForTheWholeFunction);
    varForTheWholeFunction = sortData(scopedVar);
    }

    return varForTheWholeFunction;
} 
```

Enter fullscreen mode Exit fullscreen mode

这也可以用于`switch`语句。

```
function () {
    let a;

    switch(x) {
    case "getData": {
        let y = getData();
        a = parse(y);
    } break;
    ...
    default: {
        let y = "defaultData";
        a = parse(y);
    }
    }

    return a;
} 
```

Enter fullscreen mode Exit fullscreen mode

那么`const`呢？嗯，它们的作用域类似于`let`，但是必须在定义时赋值*和*，以后不能更改，只有在声明局部变量的函数被再次调用时。

```
function (x) {
    if (x > 2) {
    // This will fail:
    const a;

    // This will also fail:
    const a = 1;
    a = 2;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以它是一个只能设置一次的变量，但是要小心:

```
function () {
    // This would work:
    const x = {y: 1};
    x.y = 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

间接作用不能从变异中被拯救出来。

# 结论

有了`let`和`const`，JavaScript 有了一种更细粒度地定义作用域的方法，这使得开发人员能够更多地限制代码依赖性。

这样做的代价是增加了语言的复杂性，但另一方面，您不必使用现有的所有功能:)