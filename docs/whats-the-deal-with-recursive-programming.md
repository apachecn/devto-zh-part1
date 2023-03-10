# 递归编程是怎么回事？

> 原文：<https://dev.to/bengreenberg/whats-the-deal-with-recursive-programming>

链接列表。二元搜索。深度优先搜索。递归。如果你像我一样，没有数学或科学背景就开始编程，这些概念一开始看起来会让人不知所措。你可能真的很擅长制作令人敬畏的 web 应用程序和解决问题，但是你已经直观地使用过的工具，当用技术名称引用时，可能会飞过你的头顶。在接下来的几周里，我会写一些简短的帖子，向像我一样可能被称为“文科”或“人文”程序员的人介绍这些概念。我们从递归编程开始。

什么是递归？

维基百科将计算机科学中的递归定义为:

> 计算机科学中的递归是一种方法，其中问题的解决方案依赖于同一问题的较小实例的解决方案(与迭代相对)。这种方法可以应用于许多类型的问题，递归是计算机科学的中心思想之一。

简单来说，这意味着什么？本质上，如果你试图解决的问题可以被分解成一个接一个的小步骤，你可以使用递归来得到解决方案。递归编程的好处是(*虽然不总是*)比迭代方法更有时间效率，并且在处理非常大的数据集时很有帮助。

让我们拿一个简单的问题，用递归的方法把它分解。我们如何构建一个程序来检查一个给定的字符串是否是回文？(复习:回文是任何向后或向前读都一样的单词。)

我们的回文函数的一个递归解如下:

```
function isPalindrome(myString) {
    if (myString.length <= 1) return true;
    if (myString.charAt(0) != myString.charAt(myString.length - 1)) return false;
    return isPalindrome(myString.substr(1, myString.length - 2)); 
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你在我们的`isPalindrome`函数的第 4 行看到的，我们从函数内部返回函数本身。简而言之，这就是递归。我们为什么要这么做？一行一行的检查会弄清楚的。

第 1 行:
`if (myString.length <= 1) return true;`
这里我们检查我们传递的字符串是否为 1 个字符(或更少)。如果是，那么很明显一个 1 字符的单词可以被同样的向前或向后读，程序返回`true`。

第 2 行:
`if (myString.charAt(0) != myString.charAt(myString.length - 1)) return false;`
在这一行我们执行下一个检查。如果字符串的第一个字母与字符串的最后一个字母不匹配，这将是一种快速确定字符串肯定不是回文的方法，然后程序返回`false`。

第 3 行:
`return isPalindrome(myString.substr(1, myString.length -2));`
这是我们递归的核心所在。我们如何实现一个回文检查？通过遍历每个字母并检查该字符串索引中相对位置的互补字母是否匹配。我们从第二行开始，检查第一个和最后一个字母。然后我们可以一行一行地检查字符串中的每个字母，但是这看起来效率很低。相反，我们调用函数本身，通过重复第 1-2 行来继续，直到到达最后一个字母。

如果在任何一点

```
myString.charAt(0) 
// 0 being the current beginning letter 
// after the previous beginning letter was removed with .substr() 
```

Enter fullscreen mode Exit fullscreen mode

不等于当前结尾字母，则程序将返回 false。但是如果它遍历字符串中的所有字母，并且每次都返回`true`,那么我们知道我们有一个回文。

简而言之，这就是递归编程。