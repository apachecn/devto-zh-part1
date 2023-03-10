# Clojure 中的 HTML 搜索和替换

> 原文：<https://dev.to/lepovirta/html-search-and-replace-in-clojure-5g4d>

在 Clojure 中，数据结构主要是由一些核心数据结构构建的，比如列表、向量、映射和集合。这意味着大多数数据结构可以利用为核心数据结构构建的所有通用数据转换和查询功能，而不必为每个数据结构重新构建相同的功能。这个特性与 Clojure 丰富的标准库相结合，使得 Clojure 对于解决其他领域的数据咀嚼问题非常有吸引力。

在本文中，我将展示这些使用 Clojure 解决 HTML 转换的能力。首先，我将描述如何在 Clojure 中表示 HTML。记住这种表示，我将演示如何在 Clojure 中转换 HTML 文档。最后，我将把转换与 HTML 解析和格式化结合在一起，产生一个完整的解决方案

## HTML 的 Clojure 表示法

HTML 是一种相当一致的格式。HTML 文档主要由具有以下属性的元素组成:

*   他们有名字。
*   它们有零个或多个属性。
*   它们可以包含零个或多个标签或文本元素。

除了标签和文本元素，HTML 文档还可以包含 DTD(文档类型声明)和注释。为了简单起见，我们可以忽略 DTD，将注释解释为文本元素。

考虑到这些属性，我们可以使用 Clojure 的核心数据结构以多种风格表示 HTML 标签。 [Hiccup](https://github.com/weavejester/hiccup) ，一个用于将 Clojure 数据结构转换为 HTML 的 Clojure 库，将 HTML 标签表示为 Clojure 向量，具有以下属性:

1.  标签名被表示为向量头部的一个关键字。
2.  标签属性在向量的第二个值中被表示为一个映射。
3.  其余的 vector 元素表示 HTML 标记的主体。

例如，HTML 中的列表...

```
<ul class="navbar">
  <li>Hello world!</li>
  <li><a href="about.html">About</a></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

...可以用 Hiccup 格式(省略空白)表示如下:

```
[:ul  {:class  "navbar"}  [:li  {}  "Hello world!"]  [:li  {}  [:a  {:href  "about.html"}  "About"]]] 
```

Enter fullscreen mode Exit fullscreen mode

## 任务:给绝对路径添加根路径

现在我们有了一种用 Clojure 数据结构描述 HTML 的语言，我们可以在 Clojure 中构建 HTML 转换。

这种转换的一个例子是向出现在 URI 属性中的每个绝对路径添加一个根路径。例如，像`<a href="/index.html">...</a>`这样的链接将被转换成`<a href="/root/index.html">...</a>`。这种转换常见于在新的目录结构下移动完整的网站。

首先，我们需要指定什么是绝对路径。所有绝对路径都以单个`/`字符开始。注意，带有两个`/`字符的路径指的是[协议相关的 URL](https://www.paulirish.com/2010/the-protocol-relative-url/) 。

```
(defn  absolute-path?  [^String  uri]  (and  (.startsWith  uri  "/")  (not  (.startsWith  uri  "//")))) 
```

Enter fullscreen mode Exit fullscreen mode

考虑到这一点，我们可以编写一个函数，跳过所有不是绝对路径的 URIs，并转换那些绝对路径。添加根路径本身就像字符串串联一样简单:

```
(defn  add-root-to-uri  [root-path  uri]  (if  (and  (not  (empty?  root-path))  (absolute-path?  uri))  (str  (string/replace  root-path  #"^/*"  "/")  uri)  uri)) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要指定可以包含绝对路径的属性。有许多接受 URI 的[属性，但是为了这个练习的简单，我们只关注两个明显的属性:`a`标签中的`href`和`img`标签中的`src`。让我们为属性键定义一个集合。](https://www.w3.org/TR/REC-html40/index/attributes.html) 

```
(def  attr-names-with-uris  #{:src  :href}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以将转换应用于所有 HTML 属性(键-值对)。如果属性键是在上面的集合中定义的，我们将对属性值应用 URI 变换。否则，我们将保持属性值不变。

```
(defn  add-root-to-uri-attr  [root-path  [key  value]]  [key  (if  (attr-names-with-uris  key)  (add-root-to-uri  root-path  value)  value)]) 
```

Enter fullscreen mode Exit fullscreen mode

## 用递归转换 HTML

HTML 文档可以被认为是树，其中 HTML 元素是树中的节点或树叶。因此，为了应用转换，我们可以使用通用的树遍历策略来修改整个树。穿越一棵树的简单方法是什么？当然是递归！

```
(defn  add-root-to-html-recur  [root-path  html]  (if  (vector?  html)  ; Is it an HTML tag?  (let  [[tag  attrs  &  body]  html  ; Unpack the tag  attrs  (->>  attrs  ; Update the attributes  (map  (partial  add-root-to-uri-attr  root-path))  (into  {}))  body  (->>  body  ; Update the body (recursion)  (map  (partial  add-root-to-html-recur  root-path)))]  (vec  (concat  [tag  attrs]  body)))  ; Rebuild the tag with the updates intact  html)) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码清单中，我们定义了一个递归更新所有 URI 属性的根路径的函数。该函数解包给定的 HTML 标记(一个向量)，用先前定义的属性转换器更新其属性，对子节点应用相同的函数，最后用前两个步骤中的更新重新构建节点。如果给定的 HTML 节点不是标签(即它是文本)，则不需要转换。这与空节点体一起充当递归的终止机制。

## 摆脱递归

递归遍历相当容易理解，但是它有许多递归函数的常见问题:

1.  必须显式调用该函数，以确保处理整个树。
2.  函数必须有适当的递归终止条件，以防止无限递归。
3.  虽然 HTML 文档通常很浅，因此使用调用栈来跟踪遍历进度很少会有问题，但是如果是很深的文档，这是需要考虑的。

既然我们遍历的只是一个 Clojure 核心数据结构，那么肯定有一种通用的方法来遍历这个树吧？事实上，在 Clojure 标准库中有一个这样的模块: [clojure.walk](https://clojuredocs.org/clojure.walk) 。walk 模块提供了遍历任意 Clojure 数据结构的函数。模块中的函数采用一个数据结构，它们给每个子结构调用一个给定的函数，并用给定函数的结果替换先前的子结构来构建一个新的数据结构。

模块中的一个遍历函数是 [postwalk](https://clojuredocs.org/clojure.walk/postwalk) ，它对给定的数据结构执行深度优先、后序遍历。我们可以用`println`来告诉我们它是如何工作的。

```
(require  'clojure.walk)  (clojure.walk/postwalk  (fn  [x]  (println  x)  x)  [:p  {}  [:a  {:href  "about.html"  :title  "About"}  "See about"]])  ;; Output  :p  {}  :a  :href  about.html  [:href  about.html]  :title  About  [:title  About]  {:href  about.html,  :title  About}  See  about  [:a  {:href  about.html,  :title  About}  See  about]  [:p  {}  [:a  {:href  about.html,  :title  About}  See  about]] 
```

Enter fullscreen mode Exit fullscreen mode

正如我们从输出中看到的，在树中的其他项目中，`postwalk`点击了`href`键值。因为我们现在使用了更通用的遍历方法，传递给`postwalk`的转换被应用到所有子结构，而不仅仅是属性映射。因此，我们需要使用一个过滤器来选择对哪些结构应用我们的转换。我们可以使用一个谓词轻松实现这一点，该谓词检查该结构是否是一个属性，即一个键-值对。每个键-值对是两个元素的向量，在这种情况下，第一个元素(键)总是一个关键字。

```
(defn  key-value?  [x]  (and  (vector?  x)  (=  (count  x)  2)  (keyword?  (first  x)))) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以重用之前实现的 URI 属性转换器，并将其与上面的`postwalk`和谓词结合起来。和以前一样，我们对所有的键-值对应用转换，并简单地保持其他结构不变。

```
(defn  add-root-to-html  [root-path  html]  (let  [edit  (fn  [x]  (if  (key-value?  x)  ; only transform attributes  (add-root-to-uri-attr  root-path  x)  x))]  (clojure.walk/postwalk  edit  html))) 
```

Enter fullscreen mode Exit fullscreen mode

## 把碎片绑在一起

现在我们已经有了一种为 HTML 文档执行 URI 属性转换的方法，让我们通过处理 HTML 文本和 Hiccup 格式之间的转换来结束这项工作。

我们可以使用 [Hickory](https://github.com/davidsantiago/hickory) 库将 HTML 文本解析成 Hiccup。解析器将产生它从给定文本中找到的一系列 Hiccup 片段。

```
(require  'hickory.core)  (defn  text->hiccup  [text]  (->  text  hickory.core/parse  ; parse HTML text  hickory.core/as-hiccup))  ; convert it to Hiccup 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们完成了转换，我们就可以使用 [Hiccup](https://github.com/weavejester/hiccup) 库将数据结构转换回文本。注意，Hiccup 的`html`是一个宏，而不是一个方法，所以我们需要用一个函数包装它，以便在函数组合中使用它。

```
(require  'hiccup.core)  (defn  hiccup->text  [html]  (hiccup.core/html  html)) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将这些函数与前面创建的函数结合起来:

```
(defn  add-root-to-html-text  [root-path  html-str]  (->>  html-str  ; start with HTML string  text->hiccup  ; convert it to Hiccup  (map  (comp  hiccup->text  ; process each fragment  (partial  add-root-to-html  root-path)))  clojure.string/join))  ; join the fragments to a single string 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了它，一个在 HTML 文本中添加 URIs 的根路径的函数:

1.  给定的 HTML 文本被解析成一系列 Hiccup 片段。
2.  根路径被添加到每个 Hiccup 片段的所有 URI 属性中。
3.  每个处理过的 Hiccup 片段都被转换回 HTML 文本。
4.  所有的 HTML 文本片段被连接成一个单独的字符串。

当然，我们可以进一步将这与[文件读取](https://clojuredocs.org/clojure.core/slurp)和[写入](https://clojuredocs.org/clojure.core/spit)或者像 [Ring](https://github.com/ring-clojure/ring) 或 [HTTP Kit](http://http-kit.github.io/) 这样的 HTTP 服务器库结合起来。

## 结论

在本文中，我展示了如何在 Clojure 数据结构中表示 HTML 等语言，以及如何使用 Clojure 作为工具解决 HTML 转换问题。我们还看到了一些 Clojure 如何为其核心数据结构提供良好的开箱即用工具(如遍历)的例子。

和往常一样，我在 GitHub Gist 中加入了代码示例以供参考。感谢阅读！