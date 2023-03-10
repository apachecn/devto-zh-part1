# ★ Symfony 现在有了改进的转储功能

> 原文：<https://dev.to/freekmurze/symfony-now-has-an-improved-dump-function-30ci>

先简单说一下 Symfony 的`dump`功能。这是[的 VarDumper 组件](http://symfony.com/doc/current/components/var_dumper.html)的一部分。该函数可以将一个变量以比 PHP 自带的`var_dump`更好的格式转储到屏幕或浏览器中。在最近发布的 Symfony 3.4 和 Symfony 4 中，这个功能得到了一个很好的小改进，我想在这篇文章中强调一下。

您可能已经知道可以像这样转储内容:

```
dump($object); 
```

不要太花哨。但是如果你在以前版本的框架中这样做，它会爆炸

```
// dump didn't return anything so method is getting called on null ☠️
dump($object)->method(); 
```

我对此很恼火，所以我向 Symfony 提交了一份 PR ,让`dump`归还给它的东西。所以现在你可以用任何变量包装函数，即使有方法被调用。

```
// in Symfony 3.4 and 4.x this just works
dump($object)->method(); 
```

当转储多个东西时，它们都被返回。所以你可以像这样转储所有传递给函数的参数。

```
$object->method(...dump($arg1, $arg2, $arg3)); 
```

如果你正在使用 Laravel，你会很高兴知道 Laravel 只是使用 Symfony 的`dump`功能，所以你也可以利用 Laravel 的这个小改进。

快乐饺子！