# JavaScript 中的简单原型继承

> 原文：<https://dev.to/adamkdean/simple-prototype-inheritance-in-javascript-5336>

这个帖子更多的是一个片段，而不是一个深入的帖子；当我停止做 JS 并且可能忘记一切的时候，这是为了将来。我真的认为我的大脑就像荷马一样，每当我学习新的东西，旧的东西似乎就会泄露出来！

下面的代码片段是简单的 JavaScript 原型继承。作为一个. NET 人，乍一看似乎有点奇怪，但实际上它只是一个称为原型的基类链，直接链接回对象。我想了解一下普通的 ol' js 是如何工作的，而不需要使用任何所谓的优秀库，例如 [base.js](https://code.google.com/p/base2/) 和 [John Resig 的简单 JavaScript 继承](http://ejohn.org/blog/simple-javascript-inheritance/)。

```
var Base = function() {  
  this.printString = function(string) {
    console.log(string);
  }
};

var Ext = function() {
  this.printMessage = function() {
    this.printString("Hello, testing!");
  }
};

Ext.prototype = new Base();

var ext = new Ext();
ext.printString("test!"); // calls prototype method from Base
ext.printMessage();       // calls method from Ext 
```

Enter fullscreen mode Exit fullscreen mode

这是不言自明的，希望我能有时间深入研究这个问题。如果我这样做，期待职位！