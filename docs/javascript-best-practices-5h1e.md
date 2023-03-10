# JavaScript 最佳实践

> 原文：<https://dev.to/sarfraznawaz2005/javascript-best-practices-5h1e>

JavaScript 不仅是令人惊叹的语言，也是非常复杂的语言。为了正确使用它，您需要遵循一些最佳实践来避免任何可能出现的问题。我分享了一些您在编写 JavaScript 代码时应该遵循的最佳实践。当然，这不是一个详尽的列表，但在最基本的层面上，每个程序员都应该知道并遵守它。

## 1-永远不要忘记`VAR`关键词

大多数开发人员都知道这一点，但我还是想提一下，因为对于一些使用 JavaScript 的新手或有其他编程语言背景的人来说，这可能不太清楚。

考虑下面这段代码:

```
function myFunc(){
    var firstName = 'sarfraz';
    lastName = 'ahmed';
} 
```

应该注意，在 JS 中，变量有函数级的作用域，这意味着在函数内部声明的变量不能在函数外部访问。所以让我们测试上面的两个变量:

```
myFunc();
console.log(lastName); // ahmed
console.log(firstName); // undefined 
```

您会注意到，我们仍然能够访问`lastName`变量。原因是它没有函数级的作用域，因为我们忘记了在它前面加上 var 关键字，不像`firstName`变量。因此，`lastName`变量进入了**全局**范围，成为了`window`对象的一部分(在浏览器内部)，例如`window.lastName`也将输出姓氏。

因此，总是需要将`var`关键字放在变量之前，这样它们就不会成为全局范围的一部分。它具有以下优点:

*   您节省了内存并提高了性能
*   你不会污染全球范围
*   您错误地没有覆盖可能具有相同变量名的全局变量

这是一个非常重要的概念，JS 开发人员一直在寻找解决方案来避免这个问题。其中最流行的解决方案是 [Singleton](http://chrisiona.com/learning-modern-javascript/the-singleton-pattern/) 或者[模块模式](http://www.adequatelygood.com/2010/3/JavaScript-Module-Pattern-In-Depth)你应该检查一下。顺便说一句，如果你还想看其他图案，看看:

[初学者必备的 JavaScript 设计模式](http://addyosmani.com/resources/essentialjsdesignpatterns/book/)

## 2β在顶层声明变量

另一件应该遵循的事情是，变量应该在每个函数的顶部声明，因为所谓的 **JavaScript 提升**。这里有一个例子:

```
var name = 'sarfraz';

(function(){
console.log(name); // undefined
     var name = 'nawaz';
     console.log(name); // nawaz
})(); 
```

请注意，即使`name`变量不在函数范围内，但在第一行，它返回的是`undefined`而不是实际名称。这样做的原因是解释器在函数的顶部提升或移动变量，下面是解释器如何看到或重新排列它:

```
var name = 'sarfraz';

(function(){
     var name;
     console.log(name); // undefined
     name = 'nawaz';
     console.log(name); // nawaz
})(); 
```

可以看到，`name`变量已经被提升到顶部并在那里声明，并且`var`关键字也已经被去掉，在那里我们分配了`nawaz`的值。

同样的问题不仅是变量，还有**函数声明**，而不是**函数表达式**。您可以在此了解更多关于函数声明和函数表达式之间的区别:

[去神秘化的命名函数](http://kangax.github.com/nfe/)

这个问题的解决方案是总是在容器函数
的顶部声明**变量和函数声明**

```
function myFunc(){
     var foo;
     var bar;
     var baz;

     // do something foo, bar, baz
} 
```

但是，您必须遵循的首选和推荐的语法是，通过用逗号分隔来一次性声明所有变量:

```
function myFunc(){
     var foo, bar, baz;

     // do something foo, bar, baz
} 
```

## 3β初始化多个变量

在顶层声明变量是很好的做法，但是多次初始化就不行了。考虑:

```
function myFunc(){
    var lang = encoding = 'en';
} 
```

这是一个非常常见的错误，甚至在有经验的 JS 开发人员中也是如此，他们认为他们很快就给两个变量分配了相同的范围和相同的值。虽然`lang`和`encoding`变量的值是一些但不是范围。试试看:

```
myFunc();
console.log(encoding); // en
console.log(lang); // undefined 
```

这里，变量`encoding`又进入了全局范围。由于`var`关键字只出现在`lang`变量之前，所以它是得到正确函数范围的变量。简而言之，不幸的是，您应该避免这种快速初始化。

## 同一行上 4 个起始花括号

考虑下面的代码块，其中开始的花括号`{`在新的一行上，这在大多数情况下都很好:

```
function myFunc()
{
    // some code
} 
```

然而，同样的约定不会产生预期的结果，如果你碰巧写:

```
function myFunc()
{
     return
     {
         name: 'sarfraz'
     };
}

var f = myFunc();
console.log(f); 
```

结果将是`undefined`,因为在幕后，解释器在`return`关键字后面加了一个分号`;`,使其成为

```
function myFunc()
{
     return; // <----------------
     {
         name: 'sarfraz'
     };
} 
```

为了补救这种难以调试的问题，最好总是把开始花括号放在同一行的**上，这也很好:** 

```
function myFunc() {
     return {
         name: 'sarfraz'
     };
}

var f = myFunc();
console.log(f.name); // sarfraz 
```

这也是为什么[道格拉斯·克洛克福特](http://www.crockford.com/)在他的书 [JavaScript:好的部分](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742)中提倡 JS 的这种语法:

```
function () {
     // some code
}

if (expression) {
     // do something
} 
```

继续前进，查看 [JavaScript 编码风格](http://javascript.crockford.com/code.html)以了解更多信息以及命名约定。

请注意，受自动分号插入影响的不是`return`关键字，而是所有这些关键字:

*   **var** 声明
*   **清空**语句
*   **表达式**语句
*   **do-while** 语句
*   **继续**语句
*   **中断**语句
*   **抛出**声明

有经验的 JavaScript 开发人员非常了解 JavaScript 的*自动分号插入问题*并避免它。然而，上述编码风格的好处是，你可以避免这个问题，而不知道这个问题的存在，只要遵循这种编码风格。

## 5 .使用数组文字而不是新数组()

有两种方法可以在 JS 中创建数组:

```
var arr1 = new Array(); // array constructor
var arr2 = []; // array literal 
```

虽然两者都服务于创建数组的目的，但是两者之间有重要的区别。

在 JS 中，甚至数组也是一个对象。使用上面的第一个构造函数方法，您是在告诉解释器调用`Array`的构造函数并生成一个对象。解释器在执行上下文中查找构造函数，一旦找到，就调用它并创建`Array`对象。与后一种数组文字方法相比，它的性能似乎也受到了影响。使用数组字面量方法，解释器只在运行时创建数组，不做额外的处理。

除此之外，数组构造函数错误地引导了它处理参数的方式。考虑:

```
console.log(new Array(5)); // [,,,,]
console.log(new Array('5')); // ["5"] 
```

当一个参数被传递给 Array 并且恰好是一个数字时，一个新的数组被返回，它的 length 属性等于被传递的数字。这里需要注意的重要一点是，数组将从你指定给它的数字开始初始化，例如:

```
// Array constructor
var arr = new Array(2);
console.log(arr.length); // 2
console.log(arr[0]); // undefined

// Array literal
var arr = [2];
console.log(arr.length); // 1
console.log(arr[0]); // 2 
```

所以结论是总是使用数组文字符号而不是数组构造函数。

## 6÷使用原型跨共享

原型或原型继承的概念相当混乱。我见过有人，尤其是没有经验的 JS 开发人员向父函数中添加类成员，这些成员需要在子类间共享。考虑下面的代码:

```
function Person(name){
    this.name = name;
} 
```

现在让我们假设我们想让子类能够以某种方式显示名字，其中一个方法是将方法直接放入`Person`类:

```
function Person(name){
     this.name = name;

     this.display = function(){
         alert(this.name);
     }
} 
```

另一种方法是使用`prototype` :

```
function Person(name){
     this.name = name;
}

Person.prototype.display = function(){
     alert(this.name);
} 
```

使用这两种方法，所有的子类都可以使用 display 方法，但是两者之间有很大的区别。当你通过`this`(上面的第一种方式)将任何方法或属性附加到一个类时，那么继承子类的所有实例也将在它们或它们的签名中有这些属性或方法。另一方面，当您使用`prototype`向父类添加成员(属性和方法)时，子类仍将继承所有成员，但它不会出现在自己的功能或签名中，而是从父类借用功能，从而节省内存。因此，在大多数情况下，后一种方法似乎很好。

## 7 在属性前加逗号

当处理对象或数组时，最好在变量或对象属性前加一个逗号，例如:

```
// jQuery - create a new div with some css
$('<div>').attr({
   "id" : "myId"
 , "class" : "myClass"
 , "class" : "myClass"
 , "color" : "green"
 , "fontWeight" : "bold"
}); 
```

这样，我们就不会添加额外的逗号，也不会忘记最后一个属性中的逗号。这是一个好的实践的原因是，在 IE 中，如果在最后一个属性有额外的逗号，我们有时会得不到预期的结果( **ExtJS** 开发人员一定已经了解了这一点)。我对函数的多个变量声明或参数做了同样的处理。在我看来，这也让代码看起来很漂亮。

## 8 不要混合 JS 和 HTML

最重要的最佳实践之一是始终将 JS 代码从 HTML 中分离出来，并且不引人注目地进行 T2。人们经常会看到这样的代码:

```
<a href="#" onclick="doSomething()">Some Action</a>
<input type="button" onclick="doSomething()" value="Do Something" />
<form onsubmit="doSomething();">... 
```

这是一个非常糟糕的实践，因为它很难管理和维护。HTML 和 JS 不应该混合使用。你可以像这样做同样的事情

```
<a href="#" id="link">Some Action</a>
<input type="button" id="button" value="Do Something" />
<form id="frm">...

<script type="text/javascript">
var link = document.getElementById('link'),
 btn = document.getElementById('button'),
 frm = document.getElementById('link');

link.onclick = function(){
 // do something
};

btn.onclick = function(){
 // do something
};

frm.onsubmit = function(){
 // validate form
};     
</script> 
```

这样，管理、维护或增强 HTML 和 JavaScript 就变得容易了。

## 9 .将脚本放在底部

通常脚本被放在`<head></head>`标签中，但这应该避免。这是因为浏览器会按顺序加载你的脚本，当它们被加载的时候，其他的事情都没有做，网站的加载速度变慢了(或者至少访问者是这样认为的)，你只有在浏览器加载了这些脚本之后才能看到实际的输出。

最佳实践是，脚本应该放在页面底部，就在关闭 body 标签之前，例如`</body>`。这样，浏览器将立即显示页面，对于查看该页面的用户来说，页面加载时间会更短。

顺便说一下，总是将 CSS 放在`<head></head>`标签的顶部，因为这是浏览器首先读取的内容，并相应地呈现页面的布局。

在著名的[Yahoo’s performance 文章](http://developer.yahoo.com/performance/rules.html)中了解更多信息。

我还建议你使用 Yahoo 的 YSlow 或者 Google 的 page speed add-on([Firebug 的 add-ons),它们会给你很多关于如何提高页面性能的建议。

## 10 .永远不要忘记分号

始终用分号:
结束语句和函数表达式

```
var name = 'some name'; // <-------------

var myFunc = function(){
// some doe

}; // <------------ 
```

当您想要压缩代码(为了更快的加载时间)时，这很有用。如果在任何地方没有分号，您将无法压缩代码，或者无法获得预期的结果(很可能是代码方面的)。你应该总是，总是使用分号。

## 奖金

好消息是你可以通过使用 [JSHint](http://www.jshint.com/) 或 [JSLint](http://jslint.com/) 代码质量工具来解决上述大部分问题。它将告诉您最佳实践和代码中可能存在的任何错误。说了这么多，提高自己的 JS 技能，避免去找这类工具是有好处的。

[![](img/d86b22e666be9f760148d701d6317dfe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z3yKeAFY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/Ys_foOiEpMU)