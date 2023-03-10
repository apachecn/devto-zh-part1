# 邻近规则

> 原文：<https://dev.to/gonedark/the-proximity-rule-ip>

我注意到最近我的代码中有一种重复出现的风格。其实我的那对注意到了，问我这件事。我称之为*邻近规则*。我想过把它叫做*邻近原则*，但是这个缩写让我内心的孩子咯咯笑了。

我感觉用代码示例解释更容易。考虑下面的函数，它使用回调来过滤数组中的项。

```
function array_filter(array, callback) {
    var i;
    var length = array.length;
    var filtered = [];

    for (i = 0; i < length; ++i) {
        if (callback(array[i])) {
            filtered.push(array[i]);
        }
    }

    return filtered;
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码具有简单、传统的风格。它将相似的语句组合成由空格分隔的代码块。每个小组讲述一个*故事*——初始化、执行、响应。

然而，这个故事有点机器人化。对电脑来说没问题，但是人类也需要阅读这个故事。让我们看看应用了*邻近规则*后的相同代码。

```
function array_filter(array, callback) {
    var i;
    var filtered = [];

    var length = array.length;
    for (i = 0; i < length; ++i) {
        if (callback(array[i])) {
            filtered.push(array[i]);
        }
    }

    return filtered;
} 
```

Enter fullscreen mode Exit fullscreen mode

通过将`length`赋值移近`for`循环，我强调了它们之间的关系。所以*邻近规则*不仅仅是对相似的代码语句进行分组。也是关于分组*相关代码*。

让我们看另一个例子。考虑我们的`array_filter`函数的以下测试。

```
describe("array_filter", function() {
    var actual;
    var odd_callback;
    var even_callback;

    beforeEach(function() {
        odd_callback = jasmine.createSpy('odd');
        odd_callback.and.returnValues(true, false, true, false, true);

        even_callback = jasmine.createSpy('even');
        even_callback.and.returnValues(false, true, false, true, false);
    });

    describe("when filtering odd numbers", function() {
        beforeEach(function() {
            actual = array_filter([1, 2, 3, 4, 5], odd_callback);
        });

        it("should return only odd numbers", function() {
            expect(actual).toEqual([1, 3, 5]);
        });
    });

    describe("when filtering even numbers", function() {
        beforeEach(function() {
            actual = array_filter([1, 2, 3, 4, 5], even_callback);
        });

        it("should return only even numbers", function() {
            expect(actual).toEqual([2, 4]);
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们再次看到按语句分组的代码。如果我们在过滤偶数时关注上下文*，我们可能会问自己，“什么是`even_callback`？”*

如果我们应用*邻近规则*，我们可以提高可读性并消除这个问题。

```
describe("array_filter", function() {
    var actual;

    describe("when filtering odd numbers", function() {
        beforeEach(function() {
            var callback = jasmine.createSpy('odd');
            callback.and.returnValues(true, false, true, false, true);
            actual = array_filter([1, 2, 3, 4, 5], callback);
        });

        it("should return only odd numbers", function() {
            expect(actual).toEqual([1, 3, 5]);
        });
    });

    describe("when filtering even numbers", function() {
        beforeEach(function() {
            var callback = jasmine.createSpy('even');
            callback.and.returnValues(false, true, false, true, false);
            actual = array_filter([1, 2, 3, 4, 5], callback);
        });

        it("should return only even numbers", function() {
            expect(actual).toEqual([2, 4]);
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个例子还演示了*邻近规则*如何帮助压缩代码。尤其是当[码闻](https://blog.codinghorror.com/code-smells/)，比如*懒班*，都在空气中的时候。

对我来说，*邻近规则*只是一种编码风格。我绝不认为这是新的。我敢肯定 Knuth 或 Beck 或其他编程教父之一已经写了这一点。如果有，请告诉我。我最近的一个目标是正确地称呼事物。公平地说，我确实尝试过让[在 Twitter](https://twitter.com/gonedark/status/753278729556664320) 上提问。