# Clojure 中的惰性序列

> 原文：<https://dev.to/__namc/lazy-seq-inclojure-bmh>

基本思想:
**表达式的求值延迟到需要值的时候。**

有两种方法可以达到上述目的:

*   运行时使用 lambda 函数
*   编译时使用宏/特殊形式

通过惰性求值技术，可以使用 lambdas、闭包和递归来构造被求值为消耗的无限数据结构。在 clojure 中，它们是使用`lazy-seq`和`cons`表单生成的。

让我们从[clo jure docs for lazy-seq](http://clojuredocs.org/clojure.core/lazy-seq)
中取一个例子

```
user>  (def  fib-seq  (lazy-cat  [0  1]  (map  +  (rest  fib-seq)  fib-seq)))  ;; => #'user/fib-seq 
```

Enter fullscreen mode Exit fullscreen mode

`lazy-cat`是连接懒惰序列的另一个宏。我们将把上面的表达写成使用`lazy-seq`
的替代翻译

```
user>  (def  fib-seq  (concat  (lazy-seq  [0  1])  (lazy-seq  (map  +  (rest  fib-seq)  fib-seq))))  ;; => #'user/fib-seq  user>  (doall  (take  2000  fib-seq)) 
```

Enter fullscreen mode Exit fullscreen mode

此`lazy-seq`评估非常大的索引，而不给出 out of memory exception/stack overflow error。(不过，对于非常大的整数，可能会抛出算术异常)

**内部工作原理:**

*   `lazy-seq`第一次访问主体时执行一次。
*   结果将被缓存，并在将来调用该函数时使用。

**回到例子；**

`[0 1]`是问题的基本情况。在斐波那契数列中，前两个值是 0 和 1。每个值的连续计算需要对前一个值和前一个值求和。因此，我们至少需要两个值来开始这个过程。

`(map + (rest fib-seq) fib-seq) `
这里 rest 会返回`fib-seq`没有它的头的部分。`map`将使用`+`合并两个序列，并产生下一个序列。让我们来看看斐波那契数列`fib-seq-temp`

```
user>  (def  fib-seq-temp  [0  1  1  2  3])  user>  (map  +  (rest  fib-seq-temp)  fib-seq-temp)  ;; => (1 2 3 5) 
```

Enter fullscreen mode Exit fullscreen mode

提供给 map 的序列是同一基本序列的一部分。第一个序列`(rest [seq])`是没有头部的基本序列。第二个序列是基本序列，包括第一个值。

**这里的碱基序列是什么？**

根据斐波那契数的定义，`A(n) = A(n-1) + A(n-2)`。为了扩展序列，我们需要能够访问先前计算的值。因此，我们使用对`fib-seq`的递归引用来访问我们之前的结果。碱基序列通常从`[0 1]`开始。

让我们按照上面提到的步骤来完成这个过程:

取第一个元素`fib-seq`(基例[0 1] ) — 0
取第二个元素`fib-seq` — 1
取第三个元素`fib-seq` —卡住了吧？

这里，我们使用 map 生成一个序列，用作剩余值。
余数为 1 的[0 1]和为 0 的[0 1]的第一项之和为 1；也就是我们下面的结果。

```
user>  (map  +  (rest  [0  1])  [0])  ;; => (1) 
```

Enter fullscreen mode Exit fullscreen mode

同样，让我们重复生成第四个元素的过程。
我们再次使用`map`生成碱基序列。
计算`rest [0 1]`的第二项之和，如上计算其值为(1)；`[0 1]`和第二项

```
user>  (map  +  (rest  [0  1])  [1])  ;; => (2) 
```

Enter fullscreen mode Exit fullscreen mode

**到目前为止容易吗？**

现在生成第四个元素。
将`rest [0 1]`的第三个元素(我们计算为 1)和`[0 1]`产生的第四个元素(为 2)相加。我们得到 3。

这将不断积累，以符合系列的理想定义。为了更好地理解这一点，让我们在最初的定义中添加一个 print 语句。

```
user>  (def  fib-seq  (lazy-cat  [0  1]  (map  (fn  [a  b]  (println  (format  "%d + %d"  a  b)  (+  a  b))  (rest  fib-seq)  fib-seq)))  ;; => #'user/fib-seq  user>  (doall  (take  10  fib-seq))  1  +  0  1  +  1  2  +  1  3  +  2  5  +  3  8  +  5  13  +  8  21  +  13  ;; => (0 1 1 2 3 5 8 13 21 34) 
```

Enter fullscreen mode Exit fullscreen mode

—-

**使用不同的数据类型**

*   `map`、`reduce`、`take`等按`first`、`rest`、`more`工作。
*   `first`(以及其他)确保集合是否实现`ISeq`。
*   如果是，则直接实现这些功能。否则，创建集合的`seq`视图来实现`first` / `more` / `rest`。

`lazy-seq` **宏**

*   宏使用 [thunk](https://en.wikipedia.org/wiki/Thunk) 来存储序列生成计算。
*   当请求序列的元素/块时，调用 next thunk 来检索值。
*   thunk 创建下一个子例程来表示序列的尾部([失去理智](https://github.com/danielmiladinov/joy-of-clojure/blob/master/src/joy-of-clojure/chapter6/laziness.clj))。
*   这些 thunk 实现序列接口，每个 thunk 只被调用一次，然后被缓存。所以[实现的部分](https://clojuredocs.org/clojure.core/realized_q)只是一个值序列。

**抓牢头部**

采取这两种方法生成随机的无限懒惰序列。

```
user>  (defn  infinite[]  (lazy-seq  (cons  (rand)  (infinite))))  ;; => #'user/infinite  --  user>  (defn  infinite-1[]  (lazy-seq  (cons  (rand)  (infinite-1))))  ;; => #'user/infinite-1  user>  (def  zyx  (infinite-1)) 
```

Enter fullscreen mode Exit fullscreen mode

如果你试图运行`(last (infinite))`；它不会崩溃，但也不会终止。然而，`(last zyx)`会很快与`OutOfMemoryError`崩溃。

这是因为“抓头”。

在`(last (infinite))`中，Clojure 处理序列的早期成员，因为它能够确定它们没有任何用处。因此，记忆不会丢失。然而，在`(last zyx)`中，程序坚持定义，因此，它不能清除存储器。这导致它抛出 OutOfMemoryError。

**那么`lazy-seq`有什么意义呢？**

只是创建懒惰序列的许多可能方法之一。在 clojure 中还有其他几种方法来做这件事。

如果一个序列不是懒惰的，它经常保持在它的头部，这会消耗大量的堆空间。如果它是懒惰的，它被计算，然后被丢弃，因为它不再用于进一步的计算。当您处理大量序列，并且只使用其中的一小部分进行顺序计算时，这一点特别有用。

[介质上的原始文章](https://medium.com/lazy-eval/lazy-seq-in-clojure-da06f6d35971)

[更多关于懒惰评估的信息](https://medium.com/lazy-eval)

—
更多参考:
[https://purelyfunctional.tv/lesson/what-are-lazy-sequences/](https://purelyfunctional.tv/lesson/what-are-lazy-sequences/)
T5】http://theatticlight.net/posts/Lazy-Sequences-in-Clojure/
[http://www . thesoftware simpleton . com/blog/2014/09/08/lazy-seq/](http://www.thesoftwaresimpleton.com/blog/2014/09/08/lazy-seq/)