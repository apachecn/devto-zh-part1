# 搬起石头砸自己的脚

> 原文：<https://dev.to/adamfriedl/hoist-with-his-own-js-petard-bmh>

在 JavaScript 中，*提升*指的是声明——变量和函数的声明——自动移动到它们作用域的顶部。请注意，这并不意味着它们在您的文件中被*物理移动。相反，它是解释[执行上下文](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)的一个关键方面的简写——具体来说，就是 JavaScript 解释器在任何给定范围内解释信息的顺序。我们马上会用一些例子来说明，但是，首先，一个重要的问题是:“提升”到底是什么意思？*

## 文科插曲

*提升机*是一个 15 世纪中期的英语单词[源自](http://www.etymonline.com/index.php?term=hoist)其他日耳曼语言中的类似单词(如荷兰语和下德语)，意思是“举起”或“提高”它在英语中最著名的用法无疑是我们的朋友莎士比亚在*哈姆雷特*中的用法。哈姆雷特意识到他所谓的朋友实际上带着一封信，信中写着哈姆雷特的死刑执行令，他秘密地修改了这封信，以便他们反而会被杀死，并说“*这是“搬起石头砸自己的脚”。petard 是一种小型炸弹，由军事工程师使用，在突破防御工事时被举到大门或障碍物上，用以炸毁它们，因此，以其人之道还治其人之身*成了英语习语语，意思是“用自己的炸弹炸自己”或“落入自己的陷阱”有点像软件工程师在编写 JS 代码时不考虑*提升*会发生什么。

## 代码示例

所以，言归正传。浏览器中的 JS 解释器作为单线程工作，这意味着一次只能发生一件事。每当它考虑一个新的作用域时，它都以同样的顺序做这几件事。其中有*查看函数和变量声明的整个范围*和*执行函数并给变量赋值*——前者总是在后者之前。实际效果是解释代码，好像所有的声明都被*提升到各自作用域的顶部，而赋值停留在原来的位置。例如:*

```
badDude = 'Kylo Ren';
var badDude;
console.log(badDude) // prints 'Kylo Ren' 
```

即使我们在将`badDude`声明为变量后从未给它赋值，它仍然记录“凯洛·伦”,因为在代码执行前声明被提升到了作用域的顶部，就好像我们写了:

```
var badDude;
badDude = 'Kylo Ren';
console.log(badDude) // prints 'Kylo Ren' 
```

注意，这是变量*和*函数声明的情况。事实上，函数声明首先被挂起。

```
goat(); // prints "Messi is the greatest of all time!"

function goat() {
  console.log("Messi is the greatest of all time!");
} 
```

这里我们在全局作用域中声明函数，这个作用域就是调用`goat()`的作用域，因此函数声明被提升到全局作用域的顶部，*在函数调用*之上。

注意，当一个函数是一个变量的赋值时，情况就不一样了。如果我们修改我们的示例，使其读作:

```
goat(); // undefined

var goat = () => {
  console.log("Messi is the greatest of all time!");
} 
```

`var goat`被提升到顶部，但是分配给它的表达式留在原处——在函数调用的下面——所以`goat()`打印出`undefined`。

## 记住范围！

声明被提升到它们的*范围*的顶部，不一定是文件的顶部。例如:

```
var leadSinger = 'Diamond Dave';

(function vanHalen() {

  console.log('First lead singer was ' + leadSinger);  // prints undefined

  var leadSinger = 'Sammy Hagar';
  console.log('Second lead singer was ' + leadSinger); // prints "Sammy Hagar"
})() 
```

怎么会有人不知道李尚义·罗斯是范·海伦的第一位(也是唯一一位真正的)主唱，这让我难以理解，但那是给你的 JavaScript。在这里，leadSinger 变量的*re*-声明跳转到其作用域的顶部(在本例中是局部或函数作用域)，而其赋值保持不变。它覆盖了全局范围内的赋值，因此当我们到达第一个`console.log()`时，它是未定义的。如果我们想给 Diamond Dave 他应得的，我们可以简单地不重新声明`leadSinger`，而只是重新分配它。

```
var leadSinger = 'Diamond Dave';

(function vanHalen() {

  console.log('First lead singer was ' + leadSinger);  // prints "Diamond Dave"

  leadSinger = 'Sammy Hagar';
  console.log('Second lead singer was ' + leadSinger); // prints "Sammy Hagar"
})() 
```