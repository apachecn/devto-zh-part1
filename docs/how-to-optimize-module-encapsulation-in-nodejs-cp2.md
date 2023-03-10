# 如何优化 Node.js 中的模块封装

> 原文：<https://dev.to/mtovmassian/how-to-optimize-module-encapsulation-in-nodejs-cp2>

## node . js 中的标准封装

### 模块导出

Node.js 模块中声明的任何表达式都可以导出，并在整个应用程序中可用。导出机制依赖于关键字`exports`的使用，我们给它分配了一个变量名和一个表达式。例如，如果在我的`oscar.js`模块中，我想导出`sayMyName()`和`sayMyAge()`函数，我这样做:

```
// oscar.js
exports.sayMyName = function() {
    let name = 'Oscar';
    console.log(`My name is ${name}`);
}

exports.sayMyAge = function() {
    let birthDate = '1990-09-19';
    let age = Date.now() - new Date(birthDate) / 31556952000;
    console.log(`I am ${age} years old`);
} 
```

Enter fullscreen mode Exit fullscreen mode

这种机制非常有用，因为它可以很好地管理对函数和变量的访问。事实上，前面没有关键字`exports`的每个表达式仍然是私有的。`exports`关键字指的是包含需要导出的表达式的对象。这个对象可以通过`module.exports`关键字直接操作，而不是一个接一个地添加表达式。使用这个关键字，我们可以这样重构`oscar.js`:

```
// oscar.js
module.exports = {

    sayMyName: function() {
        let name = 'Oscar';
        console.log(`My name is ${name}`);
    },

    sayMyAge: function() {
        let birthDate = '1990-09-19';
        let age = Date.now() - new Date(birthDate) / 31556952000;
        console.log(`I am ${age} years old`);
    }

}; 
```

Enter fullscreen mode Exit fullscreen mode

### 模块导入

导入机制依赖于使用`require`函数，将我们想要导入的模块的相对路径作为参数。一旦被调用，这个函数返回`module.exports`对象，然后就可以通过键访问它包含的表达式。例如，如果在我的`index.js`模块中，我想导入`oscar.js`模块并调用`sayMyName()`和`sayMyAge()`函数，我会这样进行:

```
// index.js
let oscar = require('./oscar');

oscar.sayMyName();
oscar.sayMyAge(); 
```

Enter fullscreen mode Exit fullscreen mode

## 标准封装的局限性

让我们想象一下，我的`sayMyName()`和`sayMyAge()`函数现在需要一个客户机来将`name`和`birthDate`值读入数据库。并且这个客户端在`index.js`模块中被实例化为单例。如果我保持标准封装，我需要这样重写我的模块:

```
// oscar.js
module.exports = {

    sayMyName: function(clientDb) {
        let name = clientDb.getOscarName();
        console.log(`My name is ${name}`);
    },

    sayMyAge: function(clientDb) {
        let birthDate = clientDb.getOscarBirthDate()
                let age = Date.now() - new Date(birthDate) / 31556952000;
        console.log(`I am ${age} years old`);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

```
// index.js
let clientDb = require('./clientDb');
let oscar = require('./oscar');

oscar.sayMyName(clientDb);
oscar.sayMyAge(clientDb); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这种封装是可行的，并且没有遇到任何功能限制，但是它在这一点上损失了优化，因为数据库客户机的注入不是相互化的，并且每次调用导入的函数时都必须重复。一旦我们实现了也需要使用外部参数的私有表达式，这种优化的损失就会被放大。为了举例说明，让我们更新`oscar.js`模块中的函数`sayMyAge()`，现在变量`age`是名为`calculateAge()`的私有函数的结果。

```
// oscar.js
function calculateAge(clientDb) {
  let birthDate = clientDb.getOscarBirthDate()
  return Date.now() - new Date(birthDate) / 31556952000;
}

module.exports = {

    sayMyName: function(clientDb) {
        let name = clientDb.getOscarName();
        console.log(`My name is ${name}`);
    },

    sayMyAge: function(clientDb) {
        let age = calculateAge(clientDb);
        console.log(`I am ${age} years old`);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，需要访问数据库的是`calculateAge()`函数，而不再是`sayMyAge()`函数。由于`calculateAge()`函数是私有的，我现在被迫将`clientDb`参数传递给`sayMyAge()`公共函数，只是为了让它转换到`calculateAge()`函数。关于组件的分解和共同化，这个解决方案远不是最佳的。

## 优化封装

为了克服标准封装的局限性，可以实现这种设计模式:

```
// Design Pattern
module.exports = function(sharedParameter) {

    function privateFunction() {}

    function publicFunctionA() {}

    function publicFunctionB() {}

    return {

        publicFunctionA: publicFunctionA,
        publicFunctionB: publicFunctionB

    };

}; 
```

Enter fullscreen mode Exit fullscreen mode

这里`module.exports`不再返回一个对象而是一个全局函数。我们模块的表达式就是在其中声明的。然后全局函数返回一个对象，在这个对象中映射了我们想要公开和导出的函数。通过这种方式，共同化不再是一个问题，因为参数可以作为变量传递给全局函数，并且可以被每个私有或公共表达式访问。

如果我将这种设计模式应用到我的例子中，我的两个模块现在看起来是这样的:

```
// oscar.js
module.exports = function(clientDb) {

    function sayMyName() {
        let name = clientDb.getOscarName();
        console.log(`My name is ${name}`);
    }

    function calculateAge() {
        let birthDate = clientDb.getOscarBirthDate()
        return Date.now() - new Date(birthDate) / 31556952000;
    }

    function sayMyAge() {
        let age = calculateAge();
        console.log(`I am ${age} years old`);
    }

    return {

        sayMyName: sayMyName,
        sayMyAge: sayMyAge

    };

}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// index.js
let clientDb = require('./clientDb');
let oscar = require('./oscar')(clientDb);

oscar.sayMyName();
oscar.sayMyAge(); 
```

Enter fullscreen mode Exit fullscreen mode