# 在你毁掉你的工会之前，给你的工会贴上标签

> 原文：<https://dev.to/kayis/tag-your-unions-before-you-wreck-your-unions-oep>

Paul Gorbould 在 Flickr 上的封面图片。T3】

标记并集、区分并集、分离并集、变量、变量记录或总和类型。不同的名字，相似的概念，但是它到底是什么，标记的联合和普通的有什么不同？

## 未标记的工会

如果您来自像 C 这样的静态类型语言，您可能已经知道了联合。将不同类型的数据保存到同一个内存空间的基本方法。他们有时也被称为无标签工会。

C 语言中的一个例子可能是这样的

```
union MyUnion {
   int number;
   char text[20];
};

int main() {
   union MyUnion x;        

   x.number = 2;
   printf( "x.number: %d\n", x.number);

   strcpy( x.text, "Hello, world!");
   printf( "x.text: %s\n", x.text);

   return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

内存中`x`的大小将是`MyUnion`所能存储的最大值。它看起来有点像`struct`，但是如果你在一个*字段*中写一个值，它会覆盖其他字段的内存。这背后的基本思想是节省空间，也使 C 语言更有活力，因为一个变量现在可以存储不同的类型。

正如您可能想象的那样，这也可以用来将不同类型的结构保存到一个内存空间中。

联合的问题是，类型检查器不关心你在做什么。

如果你声明了一个`int x`，如果你试图在里面放一个字符串，类型检查器将抛出一个错误。

如果你声明了一个`union MyUnion x`，类型检查器不会跟踪你存储的内容，因为它是依赖于运行时的，所以*你必须检查你的程序逻辑*内部是否可以访问`x.number`或`x.text`。

这与 JavaScript 有什么关系？

在 JavaScript 中，你不能输入你的变量，这允许你在其中存储任何东西。

```
let x = 2;
console.log("Number:", x);

x = "Hello, world!";
console.log("Text", x); 
```

Enter fullscreen mode Exit fullscreen mode

这可能相当方便，因为如果数据结构发生变化，您仍然可以将它放在相同的变量中，而不用关心类型。

当你得到稍微复杂一点的数据结构时，问题就出现了。

```
let x = {
  httpMethod: "GET",
  path: "/users",
  queryParams: { id: 10 }
};
console.log("ID:", x.queryParams.id);

x = {
  httpMethod: "POST",
  path: "/users",
  body: { name: "Jane" }
};
console.log("ID:", x.body.name); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`GET`请求带有一个`queryParams`字段，而`POST`请求带有一个`body`字段。`path`是一样的，但是有些部分不同。

可以用`httpMethod`字段查一下是什么，但是要自己动手。如果你弄错了，你可能会在一个`GET`请求中访问`x.body.id`，然后一切都完了，因为 *x.body 是未定义的*。

如果你使用 JavaScript 有一段时间了，你可能会注意到基本上所有的数据都是一个*无标签的联合*。大多数情况下，你只是将一种类型的数据存储到一个变量中，但更多的情况是，你最终会推动那些*有点*相同，但在某些领域有所不同的对象，就像上面的请求示例。

## 标记工会

那么关于*标记的联合*的想法是什么？

它们允许您在静态类型系统的帮助下定义联合的差异。

这是什么意思？

正如我在 request 示例中解释的那样，你经常会有一堆不同的数据类型，它们出现在一个变量中，比如一个函数的参数之类的。它们基本上是相同的，但在几个领域有所不同，或者完全不同。如果你想确保不访问不存在的数据，并防止臭名昭著的*未定义*错误，你必须在运行时检查程序代码内部。

这样的检查可能是这样的:

```
function handle(request) {
  if (request.method == "GET") console.log(request.queryParams.id);
} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以直接检查`queryParams`对象，但是没有人强迫你这样做，这完全在你的掌握之中，有一天可能会在生产中失败。

在类型系统中带有标签 union 的语言允许你在编译时进行这种检查。[原因](https://reasonml.github.io/)就是这样一种语言。

请求类型的示例如下:

```
type body = {name: string};
type queryParams = {id: string};
type httpMethod = GET(queryParams) | POST(body);

type request = {
  path: string,
  httpMethod: httpMethod
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在数据被封装在一个*标记的联合*(合理地称为变体)中，这是顶部的`httpMethod`类型。

如果`httpMethod`的内容是`GET`，你甚至不能访问一个`body`，它可能有(并且经常有)与`queryParams`完全不同的结构。

用法示例如下:

```
let handleRequest = (req: request) => 
  switch (req.httpMethod) {
  | GET(query) => Js.log("GET " ++ req.path ++ " ID:" ++ query.id)
  | POST(body) => Js.log("GET " ++ req.path ++ " ID:" ++ body.name)
  }; 
```

Enter fullscreen mode Exit fullscreen mode

这是做什么的？它将参数`req`输入为`request`。由于`req.httpMethod`是一个变体(=带标签的联合)，我们可以使用 switch 为该变体中的不同类型做事情。

许多标记了联合的语言甚至强迫你为每一种可能性做事情。这一开始看起来很奇怪，但后来会有帮助。如果有人更改了标记的联合(可以在代码中的其他地方定义),类型检查器会告诉您需要为该联合中的新类型做些什么。如果手动完成，这可能会被忘记。

## 结论

标记联合是在一个变量中存储不同数据类型而不丢失其结构的好方法。这使得代码更像是用动态类型语言编写的，同时从长远来看也更加安全。

Reason 就是这样一种语言，它试图让 JavaScript 开发人员在使用熟悉的语法进行交付的同时，能够访问标记联合等概念，即 Reason 中的变体。

如果你对 FP 不感兴趣的话，TypeScript 也标记了 unions。