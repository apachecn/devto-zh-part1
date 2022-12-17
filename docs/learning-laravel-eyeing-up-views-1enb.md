# 学习拉韦勒:盯着看

> 原文：<https://dev.to/nathanheffley/learning-laravel-eyeing-up-views-1enb>

*原贴于[nathanheffley.com](https://www.nathanheffley.com/blog/learning-laravel-eyeing-up-views)T3】*

既然我们已经开始学习如何设置一些基本的路线(如果你还没有创建任何路线的话，去看看[真正激进的路线](https://dev.to/blog/learning-laravel-really-radical-routing))，让我们给我们的站点访问者一些比一些纯文本更好的东西

视图将允许您使用名为 Blade 的模板引擎轻松创建 HTML 页面。您可以查看第一次在`resources/views/welcome.blade.php`中创建 Laravel 项目时创建的默认页面。

在该文件中，您将看到一个基本的 HTML 页面，其中包含一些特定于刀片的内容。下面是去掉了一些无关内容的文件:

```
<!doctype html>
<html lang="{{ app()->getLocale() }}">
  <head>
    <!-- HEAD CONTENT -->
  </head>
  <body>
    <div class="flex-center position-ref full-height">
      @if (Route::has('login'))
        <div class="top-right links">
          @auth
            <a href="{{ url('/home') }}">Home</a>
          @else
            <a href="{{ route('login') }}">Login</a>
            <a href="{{ route('register') }}">Register</a>
          @endauth
        </div>
      @endif
      <div class="content">
        <!-- PAGE CONTENT -->
      </div>
    </div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

一些刀片模板的第一个例子出现在前两行中。使用 Blade 最基本的方法是简单地将它作为一种将一些 PHP 代码的值输出到页面的方法。让 Blade 知道您想要评估一段 PHP 代码并将结果直接放入 HTML 代码的方法是使用双花括号。

```
<html lang="{{ app()->getLocale() }}"> 
```

Enter fullscreen mode Exit fullscreen mode

Blade 将遇到这一行，评估花括号中的代码(在本例中是对一个函数的调用)，然后将该函数的结果输出到 HTML 标记的`lang`属性中。在这个例子中，`app()->getLocale()`是一个特殊的 Laravel 函数，用来获取当前的语言环境。

在文件的更深处，我们遇到了一些更有趣的东西。

```
@if (Route::has('login'))
  <!-- inner stuff -->
@endif 
```

Enter fullscreen mode Exit fullscreen mode

如果你是一个敏锐的读者，你可能已经猜到这是一个`if`语句，就像你在普通 PHP 代码中使用的一样。这只是检查是否存在一条`login`路由。只有当条件为真时，这些`@if/@endif`部分中的任何内容才会被输出/评估。如果你想得到技术上的东西，用刀片模板的话说，这叫做“If 指令”。每当你在一个刀片文件中看到一个带有关键字的`@`符号时，你看到的是一个指令。

这些指令让 Blade 变得非常强大。在以后的文章中，我们可能会考虑创建我们自己的指令，但是现在我们将关注 Laravel 提供的一个特别的指令:

```
@auth
  <a href="{{ url('/home') }}">Home</a>
@else
  <a href="{{ route('login') }}">Login</a>
  <a href="{{ route('register') }}">Register</a>
@endauth 
```

Enter fullscreen mode Exit fullscreen mode

该指令检查当前用户是否已经过身份验证(登录)到站点。通常情况下，您必须使用 if 语句来检查用户是否通过了身份验证，但是这个方便的指令隐藏了所有混乱的代码和简单的关键字`@auth`。因为这基本上是一个伪装的`if`语句，所以您可以像平常一样使用`else`。这些指令赋予了 Blade 一些魔力，使它比直接的 PHP 代码更适合做模板。

既然您已经理解了大括号和指示符在模板中的含义，那么让我们创建自己的视图来代替那个无聊的“Hello，World！”来自上一篇文章。

在`resources/views`目录中创建一个名为`layout.blade.php`的新文件，我们将在其中设置任何 HTML 文档的轮廓。

```
<!DOCTYPE html>
<html>
  <head>
    Learning Laravel
  </head>
  <body>
    @yield('content')
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这个`@yield`指令是您在创建刀片模板时将要使用的最重要的指令之一。它允许你*扩展*这个模板文件，并在`@yield`所在的地方放置更多的代码。`yield`采用的参数允许你创建多个部分并把它们放在不同的地方。您可以拥有任意多的`yield`指令，只要它们有不同的名称。相当漂亮！现在让我们为我们的路线创建实际的视图。在`resources/views`中再做一个文件，命名为`hello.blade.php`。

```
@extends('layout')

@section('content')
  <h1>Hello, World!</h1>
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用`@extends`指令来告诉 Blade 我们想要*扩展哪个文件*，因为 Blade 非常聪明，它知道所有模板文件都将以`.blade.php`结尾，所以我们可以去掉那个位，只给它文件名的第一部分。然后我们使用`@section`指令，并传递给它与布局文件中的`@yield`指令相同的值。这就是 Blade 如何知道这个部分在布局模板中的位置。

现在我们已经创建了自己的视图，让我们回到`routes/web.php`文件并再次打开它。转到我们在上一课中定义的`/hello`路线，并对其进行更改，以便我们返回一个视图。

```
<?php

Route::get('/hello', function () {
    return view('hello');
}); 
```

Enter fullscreen mode Exit fullscreen mode

就像 Blade 一样，Laravel 知道我们的文件将在`.blade.php`结束，所以我们可以不写它。现在如果你保存并检查你站点的路径(很可能是在 [localhost:8000/hello](http://localhost:8000/hello) )，你应该会看到一个真正的带有`h1`的 HTML 页面，而不仅仅是纯文本。

你现在知道如何用 Laravel 创建一个网站所需的一切！您可以创建路线，显示实际的 HTML 页面，如果您愿意，甚至可以做一点编程逻辑。但是将代码放在 route 文件中并不是组织事物的好方法，仅仅返回基本页面也不是很有趣。在下一课中，我们将查看我们将一起构建的项目的简要概述，以了解如何使用 Laravel 来构建现实世界的项目。一定要回来看看，这样你就不会错过了！