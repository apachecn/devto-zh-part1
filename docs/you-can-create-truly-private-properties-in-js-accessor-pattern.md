# 您可以在 JS(访问器模式)中创建私有属性

> 原文：<https://dev.to/guitarino/you-can-create-truly-private-properties-in-js-accessor-pattern>

JavaScript 开发人员声称不可能在一个对象**上创建真正封装的属性和方法并在它的原型**上使用它们是非常常见的[。](https://stackoverflow.com/questions/436120/javascript-accessing-private-member-variables-from-prototype-defined-functions/436147#436147)

在 ES6 中，有几种方法可以轻松实现私有属性而不会造成内存泄漏。例如，您可以使用 ES6 **符号**:

```
// Closure
var SomeClass = function() {
    var priv_prop = Symbol();
    var SomeClass = function() {
        this[priv_prop] = 200;
    };
    SomeClass.prototype.test = function() {
        console.log(this[priv_prop]);
    };
    return SomeClass;
}();
var instance = new SomeClass();
instance.test(); // `200` logged 
```

或者，你可以使用一个武器地图:

```
// Closure
var SomeClass = function() {
    var priv_prop1 = new WeakMap();
    var priv_prop2 = new WeakMap();
    var SomeClass = function() {
        priv_prop1.set(this, 100);
        priv_prop2.set(this, 200);
    };
    SomeClass.prototype.test = function() {
        console.log(priv_prop1.get(this));
        console.log(priv_prop2.get(this));
    };
    return SomeClass;
}();
var instance = new SomeClass();
instance.test(); // `100` and `200` logged 
```

**符号**方法的问题在于，你仍然可以使用`Object.getOwnPropertySymbols`来访问那些属性。在这两种情况下，您可能都必须在生产代码中包含庞大的聚合填充。

在 ES6 之前，没有明显的方法来创建原型上可用的私有属性。据称，您要么放弃这个想法，要么使用内存泄漏的**映射**(或者，您可以使用 2 个数组)。但是，如果我告诉你，实际上有一种方法可以做到这一点，它是跨浏览器的，不需要 polyfills 或 ES6，并且不会产生内存泄漏，那会怎么样呢？

## 想法

我没见过谁用过这个方法(**编辑**:有人向我指出，我不是第一个想出这个方法的人。 **[此处阅读更多](https://medium.com/front-end-hacking/achieving-complete-data-encapsulation-in-javascript-5454a6b7410b)** ，所以我更愿意称之为**存取器模式**。想法是创建一个*闭包*，在闭包内创建一个*键*，并为私有属性创建一个*存储，只有提供了正确的键才能访问私有属性。下面是实现它的方法:*

```
/* Here's how you can create truly private
   properties in JS and use them on prototype */

// Closure
var SomeClass = function() {
    var key = {};

    var private = function() {
        var obj = {};
        return function(testkey) {
            if(key === testkey) return obj;
            // If the user of the class tries to access private
            // properties, they won't have the access to the `key`
            console.error('Cannot access private properties');
            return undefined;
        };
    };

    var SomeClass = function() {
        this._ = private(); // Creates a private object
        this._(key).priv_prop = 200; // this._(key) will return the private object
    };

    SomeClass.prototype.test = function() {
        console.log(this._(key).priv_prop); // Using property from prototype
    };

    return SomeClass;
}();

var instance = new SomeClass();
instance.test(); // `200` logged

var wrong_key = {};
instance._(wrong_key); // undefined; error logged 
```

很简单，是吧？`private`函数创建*私有存储*并返回私有访问函数，如果提供了正确的*键*，该函数将只返回*存储*。然后，在构造函数中，我们将这个私有访问函数分配给可以在原型上轻松使用的`this._`，前提是原型属性也可以访问*键*。基本上，没有正确的*密钥*就无法访问*私有存储器*。因此，如果用户试图用任何参数调用`this._`，也许用一个`wrong_key`，那么这个尝试将会失败，错误将会被记录，并且用户将会得到所有未定义的东西。

这种方法的优点:

*   很简单。你所需要的就是创建一个私有存储库，你可以在一个构造器/原型中访问所有私有属性。
*   它允许您创建可以从原型访问的真正私有的属性。该类的用户将无法访问它们。

这种方法的缺点:

*   这稍微污染了名称空间，因为您必须将私有存储分配给`this._`或类似的东西，但是可能没有其他方法可以做到这一点。

## 一个问题

这种方法的一个小问题是，在原型继承的情况下，如果子代和父代对私有访问函数使用相同的属性名(在这个例子中是`this._`，那么父代的私有属性不能在父代的原型中被访问，因为`this._`将引用子代的私有访问函数。我的意思是，

```
// Note: this Gist is to show a problem with accessor pattern
// and inheritance. Do not use!

var private = function(key) {
    var obj = {};
    return function(testkey) {
        if(key === testkey) return obj;
        console.error('Cannot access private properties');
        return undefined;
    };
};

var ParentClass = function() {
    var key = {};
    var ParentClass = function() {
        this._ = private(key);
        this._(key).priv_prop = 100;
    };
    ParentClass.prototype.parent_test = function() {
        console.log(this._(key).priv_prop);
    };
    return ParentClass;
}();

var ChildClass = function() {
    var key = {};
    var ChildClass = function() {
        ParentClass.call(this);
        this._ = private(key);
        this._(key).priv_prop = 200;
    };
    ChildClass.prototype = Object.create(
        ParentClass.prototype
    );
    ChildClass.prototype.test = function() {
        console.log(this._(key).priv_prop);
    };
    return ChildClass;
}();

var instance = new ChildClass();
instance.test(); // `200` is correctly logged
instance.parent_test(); // ERROR! (expected result: `100`) 
```

当调用`instance.parent_test`时，其中的`this._`将引用子进程的私有访问函数，因此，`key`将不匹配，错误将被记录。然而，这个问题很容易解决。

## 最终解决

最好的解决方案是命名空间，并确保父和子对于它们的私有访问函数有不同的属性名。这是最终的解决方案:

```
/* Here's how you can create truly private
   properties in JS and use them on prototype */

// Creates private storage, secures with a key, and
// returns a private access function
var private = function(key) {
    var obj = {};
    return function(testkey) {
        if(key === testkey) return obj;
        console.error('Cannot access private properties');
        return undefined;
    };
};

// Create closure
var ParentClass = function() {
    var priv = '_ParentClass' + Math.random(); // Namespace
    var key = {}; // Create key withing closure
    var ParentClass = function() {
        this[priv] = private(key); // Create private storage
        this[priv](key).priv_prop = 100; // Modify any private data
    };
    ParentClass.prototype.parent_test = function() {
        console.log(this[priv](key).priv_prop); // Access private data
    };
    return ParentClass;
}();

var ChildClass = function() {
    var priv = '_ChildClass' + Math.random();
    var key = {};
    var ChildClass = function() {
        ParentClass.call(this);
        this[priv] = private(key);
        this[priv](key).priv_prop = 200;
    };
    ChildClass.prototype = Object.create(
        ParentClass.prototype
    );
    ChildClass.prototype.test = function() {
        console.log(this[priv](key).priv_prop);
    };
    return ChildClass;
}();

var instance = new ChildClass();
instance.test(); // `200` logged, as expected
instance.parent_test(); // `100` logged, as expected

// Yet, there's no way to access the property from outside of the closure 
```

与前面的代码片段几乎唯一的不同是，我们用`this[priv]`替换了子类和父类的`this._`，其中`priv`是命名空间和随机生成的，以确保私有访问函数存储在子类和父类的不同属性名下。

我可以提出的另一个建议是，您应该通过使`this[priv]`不可配置、不可枚举和只读来保护它:

```
 Object.defineProperty(this, priv, {
        value: private(key)
    }) 
```

而不只是

```
 this[priv] = private(key) 
```

这将确保用户无法删除或修改`this[priv]`，这对正确的私有存储功能至关重要。

## 结论

继续使用**访问器模式**！它允许您创建真正封装的属性，并在原型上使用它们。让其他人知道这个方法，这样我们就不会继续误解在 JavaScript 中隐私是不可能实现的。分享这篇文章也会帮助**ðÿ˜š**