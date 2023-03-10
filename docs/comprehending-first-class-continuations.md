# 理解一流的延续

> 原文：<https://dev.to/buntine/comprehending-first-class-continuations>

延续作为头等价值的概念对我来说是一个棘手的问题，难以理解到一个令人舒服的确定性水平。我想对于像我这样的外行人来说，这可能是真的。这篇文章代表了我收集并以连贯的方式呈现我的想法的尝试！我很高兴收到更正和评论。

我将从定义我自己风格中的几个关键术语开始。

## 延续

我们可以观察到，无论复杂程度如何，每个表达式的最终目标都是向周围的执行上下文返回值。这个上下文被称为延续——它代表了所有需要计算的东西。因此，每个计算都有一个关联的 continuation，它指定一旦控制返回，执行应该从哪里继续。

作为一个简单的例子，考虑下面的方案形式:

```
(lambda (n) (+ n 1)) 
```

Enter fullscreen mode Exit fullscreen mode

与子表达式“1”相关联的延续是一个匿名函数，其主体由对+原语过程的调用组成，该过程的第一个参数是局部变量 n，并且正在寻找其第二个参数。有了这种理解，我们可以说表达式传递值，延续接收值。

## 当前延续

“当前”指的是从程序执行的当前点导出的延续。换句话说，当前的执行点，词法环境和调用栈的状态(你应该熟悉[栈](http://en.wikipedia.org/wiki/Stack-based_memory_allocation)和[堆](http://en.wikipedia.org/wiki/Memory_management#Dynamic_memory_allocation))。

## 一等物体

要让一个对象成为编程语言中的“一等公民”，它需要一些属性。具体来说，它必须支持:

*   作为参数传递给函数；
*   从函数返回；
*   存储在变量中或数据结构中；
*   在运行时构造。

数字和字符串之类的东西在大多数编程语言中都是头等大事。函数在所有函数式编程语言中都是一流的。在 Scheme(我为本文选择的语言)中，几乎所有东西都可以被认为是一流的——包括我们很快就会看到的延续。

## 好了，现在继续方案

为了向程序员呈现当前执行上下文(当前延续)的抽象概念，Scheme 需要某种方式来表示它。有可能在语言中实现特定的对象和语法来处理这个问题，但是简单地将当前的延续表示为一个一级函数要容易得多。这个将隐含的东西转化为可以明确表达的东西的过程被称为“[具体化](http://en.wikipedia.org/wiki/Reification_(computer_science))”。因此，我们可以说 Scheme 将当前延续具体化为一个函数对象。

Scheme 提供了函数**call-with-current-continuation**(别名为 **call/cc** )为程序员提供延续。call/cc 是一个一元函数，它接受另一个一元函数 f 作为它的参数。当被调用时，Scheme 会将当前延续 c 具体化为一个函数，并将 f 应用于 c。因此:

```
c(call/cc f) --> c(f c)     ; Not Scheme. c(...) represents the current continuation 
```

Enter fullscreen mode Exit fullscreen mode

当 c 应用于自变量 v 时，现有的延续被终止，由 c 表示的延续被恢复。因此，程序流将从捕获延续的地方继续，v 将成为 **call/cc** invokation 的总值。

迷惑？看看这个简单的例子:

```
(call/cc
  (lambda (return)
    (+ 2 (return 4) 1))) 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，你可能认为这个表达式的值是 7，但实际上，它的值是 4。这是因为我们已经在形式参数返回中捕获了当前延续，然后将它应用于数字 4。这使得程序流立即返回到继续执行的点。是的——一旦应用，c 将不再返回！我们有效地模仿了“返回”控制操作符，它在许多流行的编程语言中允许提前转义。

为了以更有效的方式演示早期转义的概念，让我向您展示一个小 Scheme 过程的两个版本——一个使用延续，另一个不使用。过程**有-sym？**接受一个 S 表达式(在本例中是符号列表和/或嵌套符号列表)和一个符号作为参数。如果给定的符号出现在 S 表达式中的某个位置，则返回 true。首先看一下非延续版:

```
(define (has-sym? lst s)
  (cond
    ((empty? lst) #f)               ; Nothing left, must not be present.
    ((list? (car lst))              ; Is a sublist, inspect it also.
      (or (has-sym? (car lst) s)
          (has-sym? (cdr lst) s)))
    ((eq? s (car lst)) #t)          ; Found a match, return true to the caller!
    (else
      (has-sym? (cdr lst) s))))     ; Nothing yet, keep looking... 
```

Enter fullscreen mode Exit fullscreen mode

虽然简明易懂，但这个实现(因为它被定义为一个递归过程)有一点麻烦，即使我们找到了一个匹配，我们也需要等到调用栈展开，然后实际的结果才能返回到 continuation，在 continuation 中**有-sym？**过程被调用。如果，也许，我们抓住了**的延续，会怎么样？**带有 **call/cc** 的过程，然后在我们的函数参数中定义一个本地助手过程(来执行搜索)，我们就能够调用 continuation 对象，并且一旦发现匹配就直接跳出这个过程。例如:

```
(define (has-sym? lst s)
  (call/cc
    (lambda (return)
      (define (find lst)            ; Helper proc, local to lambda argument of call/cc.
        (cond
          ((empty? lst) #f)
          ((list? (car lst))
            (or (find (car lst))
                (find (cdr lst))))
          ((eq? s (car lst))
            (return #t))            ; A match! Invoke 'return' for an early-escape!
          (else
            (find (cdr lst)))))
      (find lst))))                 ; We end up here when/if 'return' is invoked. 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，值得指出的是 Scheme 中的 continuations 虽然更强大，但也比其他语言的 [GOTO](http://en.wikipedia.org/wiki/Goto) 语句更保守，因为我们只能将控制返回到我们已经访问过的地方(显然，这是一件好事！).还要注意，与词法闭包不同，应用延续不会恢复引用环境！如果你改变变量的值，它们将保持不变。

## 延续为一等公民

到目前为止，我试图解释 Scheme 中一级对象和延续的概念，但是当我们将两者结合起来，并开始将延续本身视为一级状态时，我们就打开了一个酷想法的宝库。例如，考虑下面这个令人困惑的例子:

```
(define (fact n)
  (let* ((total n)
         (k (call/cc (lambda (kk) kk))))
    (set! n (- n 1))
    (set! total (* total n))
    (if (<= n 1)
      total
      (k k)))) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过捕获局部变量 k 中的 continuation(具体化为一个一级函数)来演示 Scheme 中 continuation 的不确定范围。factorial 的这种定义通过捕获 continuation 对象、就地修改参数 n 和局部变量 total，然后以 continuation 本身作为参数来调用 continuation。这导致了局部 GOTO，并将原始延续重新绑定到局部变量 k。这个过程一直持续到 n 达到 1，这时我们将总数返回给用户。 **let* **表单在这里很重要，因为它将扩展成一系列嵌套的**let** 表单。如果没有这个，我们将在 continuation 对象的每次调用中把 total 重新绑定到 n 的初始值。用* *让* **我们可以存储一个运行总数。

如您所见，我们已经使用了控制流来计算阶乘，而没有使用递归或任何内置的循环结构。当然，这种实现也有两个明显的缺点:

*   这有点深奥，很难理解
*   它改变了本地状态，这使得证明这个函数将保持引用透明性变得更加困难。

## 如此这般...

凭借一流的延续处理能力，我们有能力定义其他编程语言常见的几个控制流构造，如[回溯](http://en.wikipedia.org/wiki/Backtracking)、[尝试/捕捉异常处理](http://en.wikipedia.org/wiki/Exception_handling)和[“绿色”线程](http://en.wikipedia.org/wiki/Green_threads)。

研究延续这样的主题有助于我们巩固一些我们可能习以为常的概念。管理控制流的想法对于我们的领域来说是如此的初级，以至于我们可能永远不会停下来思考如何有效地解释这样一个隐含理解的想法。

在进一步阅读方面，我建议维基百科上的[Call-with-current-Continuation](http://en.wikipedia.org/wiki/Call-with-current-continuation)、 [Continuation](http://en.wikipedia.org/wiki/Continuation) 和 [Call stack](http://en.wikipedia.org/wiki/Call_stack) 文章。R5RS 标准文件 **call/cc** 和方案中可用的其他[控制处理程序](http://schemers.org/Documents/Standards/R5RS/HTML/r5rs-Z-H-9.html#%_sec_6.4)。Lambda Ultimate 上关于[理解延续](http://lambda-the-ultimate.org/node/86)的讨论也帮助了我写这篇文章。

最后，我认为当程序员获得如此高水平的延续控制权时，必须记住本大叔的话:

> "强大的力量也必然带来巨大的责任！"-神奇幻想# 15(1962 年 8 月)-第一个蜘蛛侠故事。