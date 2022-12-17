# PHP 101:基本表单验证

> 原文：<https://dev.to/bengreenberg/php-101-basic-form-validation-8ge>

<figure>[![](img/87676f824255a640fa2dda5a3fefc0d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TqREXP_---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AL4ACgFeqaDywDSSxfpNU7g.jpeg) 

<figcaption>图片来自[公共领域图片](http://www.publicdomainpictures.net/pictures/30000/velka/baby-working-on-a-laptop.jpg)</figcaption>

</figure>

当想到当今流行的 web 技术时，你可能不会首先想到 PHP。首先想到的可能是 Javascript 或者 Python，这两种都是强大的编程语言。然而，PHP 仍然在 web 上扮演着非常重要的角色，无论是对于新的应用程序，还是作为您可能会遇到的许多现有代码库的语言。事实上，在 2017 年 Stack Overflow [开发者调查](https://insights.stackoverflow.com/survey/2017#technology)中，PHP 在受欢迎程度方面排名第六，仅落后 Python 几个百分点。

在这个新系列中，我将探索 PHP 的一些基本功能。由于 PHP 是一种用于网站和 web 应用程序的语言，所有这些博客帖子都将围绕将 PHP 整合到 web 项目中以增加功能和动态性。我们将讨论的第一个主题是使用 PHP 执行一些基本的表单验证。

表单验证是 web 开发的重要组成部分。web 开发的第一条规则是**永远不要相信用户**。这并不是说你的项目遇到的每一个用户都想要实施恶意行为。尽管如此，每个用户都可能犯错误，这是事实。每个用户都可以选择一个项目，如果他们指的是另一个项目，或者忘记在需要的地方包含信息等等。如果你不想让你的用户意外地[发出导弹来袭的警报](https://dev.to/ben/the-hawaii-missile-alert-was-the-software-developers-fault-2bid)，那么表单验证就至关重要。

让我们创建一个将要使用的表单:

```
<form action="form_submit.php" method="post">
  <p>First Name: <input type="text" name="first_name" size="20" maxlength="20">
  <p>Last Name: <input type="text" name="last_name" size="30" maxlength="40">
  <p>Email: <input type="email" name="email" size="40" maxlength="60">
  <p>Current Age: <input type="text" name="age" size="10" maxlength="10">  
  <p><input type="submit" name="submit" value="Submit">
</form> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个简单的表单，要求用户输入他们的名字、姓氏和电子邮件地址。在 PHP 中，表单操作非常重要。在这种情况下，表格数据将被发送到`form_submit.php`。

作为复习，我们可以通过*超全局*变量`$_POST`访问每个输入的值，这是一个数组(如果方法是`GET`，那么我们会使用变量`$_GET`)。可以使用输入标记中提供的名称来访问数组中每一项的索引。因此，`$_POST['first_name']`将给出他们提供的用户名字的值。

在本文中，我们不打算讨论如何处理表单数据。我们要求用户提供这些信息可能是为了让他们访问应用程序的某些受限部分，收集他们的信息以添加到数据库中，或者出于某种原因验证他们的身份。所有这些功能以及更多功能都可以用 PHP 轻松实现。在以后的主题中，我们将重点关注如何处理数据，但是现在，我们只想执行一些简单的验证。

我们可以执行的第一个验证是检查用户输入的信息:

```
if (isset($_POST['first_name'])) {
  // do something;
} else {
  echo '<p>It looks like you forgot to enter your first name.</p>';
} 
```

Enter fullscreen mode Exit fullscreen mode

[`isset`](http://php.net/manual/en/function.isset.php) 功能检查该项目是否已被*设置*。换句话说，只有当它包含数据并且不是`NULL`时，它才会返回`true`。因此，当我们在`$_POST['first_name']`上用`isset`执行条件检查时，只有当用户填写时才会是`true`。这实际上并不检查用户填写了什么，但至少检查了用户是否填写了内容。

或者，您可以使用 [`empty()`](http://php.net/manual/en/function.empty.php) 来检查表单字段是否为空。你会这样做:

```
if (empty($_POST['first_name'])) {
  echo '<p>You forgot to enter your first name.</p>';
} else {
  // do something;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了确保表单只包含数字数据而不是字母，你可以使用`is_numeric()`函数。比如:

```
if (is_numeric($_POST['age']) {
  // do something;
} else {
  echo '<p>You forgot to enter your age.</p>';
} 
```

Enter fullscreen mode Exit fullscreen mode

该功能可与`isset`或`empty`结合使用，检查表格条目是否同时填写和编号:

```
if ((isset($_POST['age'])) && (is_numeric($_POST['age']))) {
  // do something;
} else {
 echo '<p>You forgot to enter your age.</p>';
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦你验证了用户提交了数据，例如，当你希望数据是一个数字时，数据是一个数字，另一个有用的工具是 [`trim()`](http://php.net/manual/en/function.trim.php) ，它从字符串的开头和结尾删除所有多余的空格。因此，如果用户在输入他们的姓时不小心按了太多次空格键，你将会删除所有带有`trim`的用户。你可以这样简单地使用它:`trim($_POST['last_name'])`。事实上，为了方便引用，您可以将返回值赋给另一个变量，比如`$lastname`。

PHP 中还有很多表单验证，特别是当你开始与一个数据库交互时，无论是 MySQL 还是其他。一个常见的例子是删除 SQL 语句中使用的特殊字符的 [`mysqli_real_escape_string()`](http://php.net/manual/en/mysqli.real-escape-string.php) 函数。

我希望这篇文章为您开始考虑将表单验证与 PHP 结合起来提供了一个良好的起点。和往常一样，所有 PHP 相关函数的参考资料是[php.net](http://php.net/)，在那里你可以找到关于每个 PHP 函数的文档以及更多内容。编码快乐！

*(如题，这是一篇“101”级别的文章。还有很多东西需要学习，包括非常有用的 [`filter_input()`](http://php.net/manual/en/function.filter-input.php) 函数。)*