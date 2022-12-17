# Web Maker:防止无限循环

> 原文：<https://dev.to/chinchang/web-maker-preventing-infinite-loops>

## 问题

当您在 [Web Maker](https://kushagragour.in/lab/web-maker) 中编写 JavaScript 时，它会在预览窗口中实时呈现该 JavaScript。这意味着如果你在你的代码中使用任何循环结构，你可能会在中途定义你的循环变量，条件，变量等等。此时，如果 Web Maker 呈现您的 JavaScript，将导致无限循环。让我们看一个例子。假设我要写一个 for 循环迭代 10 次，我就从
开始

```
for (var i = 0; i<10; [cursor_here]) {
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我的光标应该在显示`[cursor_here]`的地方，我仍然在定义我的循环，并在那里写`i++`。但是当我们实时渲染时，这些不完整的代码会在预览中执行，导致无限循环。

因此，我们需要一种方法来防止这种中间不完整循环无限运行并阻塞浏览器。

## 解

Web 上有很多关于在运行时检测无限循环有多困难的讨论，原因很简单，因为任何运行时实际上都无法区分正常循环和将要无限运行的循环。有一些分析器试图解决这个问题，但是静态分析只能做这么多。

Web Maker 解决这个问题的方法是检查在循环中花费的时间。具体来说，我检查循环时间是否不超过 1 秒。否则它会被标记为无限循环。为了能够跟踪这个时间，我们需要在循环内部进行时间检查，因此这需要改变实际的循环代码，也称为[代码检测](https://en.wikipedia.org/wiki/Instrumentation_(computer_programming))。

所以如果我们有一个这样的代码:

```
for ( var i = 0; i < 10;) {
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的检测之后，它应该变成:

```
var startTime = Date.now();
for ( var i = 0; i < 10;) {
    if (Date.now() - startTime > 1000) { break; }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了分析一段代码，我们需要一个 JavaScript 解析器。为此，我使用了 [Esprima](http://esprima.org/) 。让我们看看如何使用 Esprima 来检测我们的代码。

### 检测一个循环

Esprima 将一串 JavaScript 代码转换成一个[抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (AST)，这基本上是一个表示代码片段的树状结构。

```
function instrumentCode(code) {
    var ast = esprima.parse(code);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了了解 AST 是什么样子，让我们在下面的代码上运行 esprima:

```
var foo = 3;
for (var i = 0; i < 10; i++) {} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们将得到的:

```
<img src="http://kushagragour.img/ast.png" alt="Esprima Abstract Syntax Tree">
Esprima's Abstract Syntax Tree 
```

Enter fullscreen mode Exit fullscreen mode

注意，返回的 AST 是一个重复的嵌套数组结构(`body`在`body`里面)，每个可识别的单元都是某个类的一个实例。例如`VariableDeclaration`、`ForStatement`等。这是我们可以用递归轻松遍历的东西，比如:

```
function processAst(ast) {
    var currentElement;
    // If this ins't actual body, recurse with the body
    if (!Array.isArray(astBody)) {
        processAst(astBody.body);
        return;
    }
    // Traverse the body
    for (var i = ast.length; i--;) {
        var currentElement = ast[i];

        // Process `currentElement` here

        // Recurse on inner body
        if (currentElement.body) {
            processAst(currentElement.body);
        }
    }
}
function instrumentCode(code) {
    var ast = esprima.parse(code);
    processAst(ast);
} 
```

Enter fullscreen mode Exit fullscreen mode

有了上面的代码，我们将继续在`currentElement`中获得不同的语法结构。接下来，我们检查它们并注入实际的循环保护代码。

### 注入回路保护检查

我们关心什么时候`currentElement`是一个`for`、`while`或`do-while`回路。这可以通过简单的测试`currentElement.type`来检查。让我们补充一下。

```
function processAst(ast) {
    var currentElement;
    // If this ins't actual body, recurse with the body
    if (!Array.isArray(astBody)) {
        processAst(astBody.body);
        return;
    }
    // Traverse the body
    for (var i = ast.length; i--;) {
        var currentElement = ast[i];

        if (currentElement &&
            currentElement.type === 'ForStatement' ||
            currentElement.type === 'WhileStatement' ||
            currentElement.type === 'DoWhileStatement') {
            // We got a loop!
        }
        // Recurse on inner body
        if (currentElement.body) {
            processAst(currentElement.body);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要在 AST 语法中注入两个语句。为此，我们可以再次使用 esprima。一旦我们将语句转换成 AST 对象，只需将它们添加到 AST 中的右边`body`即可。

```
if (currentElement &&
    currentElement.type === 'ForStatement' ||
    currentElement.type === 'WhileStatement' ||
    currentElement.type === 'DoWhileStatement') {

    var ast1 = esprima.parse('var myvar = Date.now();');
    var ast2 = esprima.parse('while(a){if (Date.now() - myvar > 1000) { break;}}');
    var insertionBlocks = {
        before: ast1.body[0],
        inside: ast2.body[0].body.body[0]
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们在`myVar`中指定了当前时间。现在，在一个代码片段中可能有多个循环(甚至是嵌套的),它们都需要用它们唯一的变量来处理。所以我们用一个 3 位数的随机字符串来替换插入块中的变量名:

```
if (currentElement &&
    currentElement.type === 'ForStatement' ||
    currentElement.type === 'WhileStatement' ||
    currentElement.type === 'DoWhileStatement') {

    var ast1 = esprima.parse('var myvar = Date.now();');
    var ast2 = esprima.parse('while(a){if (Date.now() - myvar > 1000) { break;}}');
    var insertionBlocks = {
        before: ast1.body[0],
        inside: ast2.body[0].body.body[0]
    };
    randomVariableName = '_' + generateRandomId(3);
    insertionBLocks.before.declarations[0].id.name = insertionBLocks.inside.test.left.right.name = randomVariableName;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在剩下的就是在正确的位置插入插入块:

```
if (currentElement &&
    currentElement.type === 'ForStatement' ||
    currentElement.type === 'WhileStatement' ||
    currentElement.type === 'DoWhileStatement') {

    var ast1 = esprima.parse('var myvar = Date.now();');
    var ast2 = esprima.parse('while(a){if (Date.now() - myvar > 1000) { break;}}');
    var insertionBlocks = {
        before: ast1.body[0],
        inside: ast2.body[0].body.body[0]
    };
    randomVariableName = '_' + generateRandomId(3);
    insertionBLocks.before.declarations[0].id.name = insertionBLocks.inside.test.left.right.name = randomVariableName;

    // Insert time variable assignment as first child in the body array.
    ast.splice(i, 0, insertionBLocks.before);

    // If the loop's body is a single statement, then convert it into a block statement
    // so that we can insert our conditional break inside it.
    if (!Array.isArray(el.body)) {
        currentElement.body = {
            body: [ el.body ],
            type: 'BlockStatement'
        };
    }

    // Insert the `If` Statement check
    currentElement.body.body.unshift(insertionBLocks.inside);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们结束了。

你也可以看到这个逻辑的实际 Web Maker [源代码](https://github.com/chinchang/web-maker/blob/master/src/utils.js)或者[自己尝试](https://chrome.google.com/webstore/detail/web-maker/lkfkkhfhhdkiemehlpkgjeojomhpccnh)。