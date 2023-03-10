# 在 Javascript - Listing.js 中处理列表

> 原文：<https://dev.to/larswaechter/dealing-with-lists-in-javascript---listingjs-664>

大多数人可能知道 Java 或 C#等编程语言中的列表。在我工作的公司，我们在后端使用 Adobe 的 ColdFusion。在这里，我们实际上经常使用列表。在我看来，这是数组的一个很好的替代品。这就是为什么我开始做一个小的 JS 库来处理 Javascript 中的列表，叫做: **Listing.js** 。起初这个库只是为了私人使用，但是我认为没有理由不共享它。

**该库可在[GitHub](https://github.com/Lars124/Listing.js/tree/master)T3 获得**

这个库中的方法是来自其他编程语言的列表和数组函数的集合，所以你会知道其中的大部分。你可以在你的浏览器和 Node.js 中使用这个库，接下来，我想简单介绍一下这个库。

## 有效分隔符和列表:

Listings.js 识别三种不同类型的分隔符:

```
var list = "1,2,3,4";
var list = "1;2;3;4";
var list = "1:2:3:4"; 
```

Enter fullscreen mode Exit fullscreen mode

显然，每个数字都是一个列表元素。默认的列表分隔符是逗号(“，”)。除了数字，你也可以使用字符串作为列表元素。

```
var list = "hello,I,am,Lars";
var list = "Nice;to;see;you"; 
```

Enter fullscreen mode Exit fullscreen mode

**注意:** Listing.js 识别列表中的空格。因此，如果一个元素包含空格，并且您试图查找该元素，请确保您的搜索参数也包含空格。

```
var list = "hello my,name is,not Peter";
listing.find(list, 'name is');

// Result: 1 
```

Enter fullscreen mode Exit fullscreen mode

## 例子:

接下来，这里是一些方法的例子。你可以在 [GitHub Repo](https://github.com/Lars124/Listing.js/tree/master) 找到所有方法和测试用例的概述。

```
var list = "1,54,2,64,24,76";
listing.append(list, 89);

// Result: "1,54,2,64,24,76,89" 
```

Enter fullscreen mode Exit fullscreen mode

```
var list = "1,54,2,64,24,76";
listing.changeDelims(list, ";");

// Result: "1;54;2;64;24;76;89" 
```

Enter fullscreen mode Exit fullscreen mode

```
var list1 = "1,54,2";
var list2 = "77,31,501";;
listing.concat(list1, list2);

// Result: "1,54,2,77,31,501" 
```

Enter fullscreen mode Exit fullscreen mode

```
var list = "ThisIs,aList,withStrings";
listing.contains(list, 'List');

// Result: "1" 
```

Enter fullscreen mode Exit fullscreen mode

```
var list = "43,21,5,9,104";
listing.each(list, function(item, index) {
  // Iterartion 1 -> item = 43, index = 0
  // Iterartion 2 -> item = 21, index = 1
  // Iterartion 3 -> item = 5, index = 2
  // Iterartion 4 -> item = 9, index = 3
  // Iterartion 5 -> item = 104, index = 4
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
var list = "13,32,54,13,11,6,103,54";
listing.getDuplicates(list);

// Result: "13,54" 
```

Enter fullscreen mode Exit fullscreen mode

```
var list = "13,32,54";
listing.len(list);

// Result: "3" 
```

Enter fullscreen mode Exit fullscreen mode

```
var myString = 'This is a test';
var list1 = 'a,test';
var list2 = 'no,fun';

listing.replace(myString, list1, list2);

// Result: "This is no fun" 
```

Enter fullscreen mode Exit fullscreen mode

如果你知道更多你想在 lib 中看到的特性，请告诉我。；)