# 如何创建 JS 函数

> 原文：<https://dev.to/poran/how-to-create-js-functions-2b6>

javascript、reactjs、nodejs、vuejs

如何创建 JS 函数

*   关于函数的一些东西

功能意味着程序的“构建模块”。它可以允许重复使用代码。例如，我们需要创建购物车。获取 AddItemCart，total-amount。它用于程序。

先说功能。如何创建函数？

# 功能声明:

函数 create function(){
alert(" Hello world！");
}
create function()；

我们来解释一下代码。“函数”是关键字，必须先写，然后我创建函数名，可以写任何字母，参数将是括号(上面的例子是空的)。最后，函数体代码，你必须在这里写你所有的代码。

为函数输出调用 Createfunction()。如果写两次，输出会显示两次。它执行函数的代码。

# 局部变量:

在函数体内声明的局部变量仅对该函数可见。确保当你写函数名的时候，你可以把单词的第一个字母写成大写字母，因为这有助于你看起来更漂亮..示例:CreateMyName。另外，我会在后面写如何写好函数名。

示例:1

函数 create my name(){
var name = " poran "；
警报(名称)；
}

create myname()；//输出:poran
alert(名称)；//错误输出，因为变量被作为局部变量调用。

# 外层变量:

在函数外部声明的外部变量。
例:2

var name = " poran
函数 ShowName(){
var nickName = " Shaha "+name；
机警(昵称)；
}

ShowName(); // output : Shahaporan

外部变量作为全局变量工作。它在代码中有完全的访问和修改权限。

示例:3

var name3 = " poran// outerVariable
函数 ShowName(){
name 3 = " Sujon "；//外部变量被更改。
var nickName = "我的名字是"+Name 3；
警戒(昵称)；
}
警戒(name 3)；//显示外部值
ShowName()；// output:我的名字是 Shahaporan
alert(name3) //显示更改的值

这里缺少一些关于外部变量的东西，我将在下一篇文章中介绍。

“快乐编码”