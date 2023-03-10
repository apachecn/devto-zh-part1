# 反向可能是另一个应用非单子

> 原文：<https://dev.to/cblp/reverse-maybe-as-another-applicative-not-monad>

## 适用与单子

每当有人想说明 *[适用](https://hackage.haskell.org/package/base/docs/Control-Applicative.html#t:Applicative)* 和 *[单子](https://hackage.haskell.org/package/base/docs/Control-Monad.html#t:Monad)* 的区别时，他们总是挑 *[单子](https://hackage.haskell.org/package/base/docs/Control-Applicative.html#t:ZipList)* ，这是一个*适用*而不是一个*单子*。

我最近发现了另一个不太常见的例子。

## 第一个’

考虑以下类型:

```
newtype First' a b = First' (Maybe a) 
```

Enter fullscreen mode Exit fullscreen mode

它的尾参数`b`是双变量的(既有协变的也有逆变的)。编译器清楚地看到了这一点:

```
{-# LANGUAGE DeriveFunctor #-}
newtype First' a b = First' (Maybe a)
    deriving (Functor) 
```

Enter fullscreen mode Exit fullscreen mode

我将编写非派生的*仿函数*实例留给您作为练习。

请注意，这个函子完全忽略了它的参数，不像纯*也许是*。

第一个名字*不是巧合。我们将使用*数据的语义。首先是这里的*。也就是说，*第一个*允许*映射两个*可能是*的值，而不会下降到其中:** 

```
λ> Nothing <> Just 'A'

<interactive>:
    No instance for (Monoid Char) arising from a use of ‘<>'
    ... 
```

Enter fullscreen mode Exit fullscreen mode

```
λ> First Nothing <> First (Just 'A')
First {getFirst = Just 'A'} 
```

Enter fullscreen mode Exit fullscreen mode

## 幺半群

让我们用相同的语义:
为我们的*先写*幺半群**

```
instance Monoid (First' a b) where
    mempty = First' Nothing
    x@(First' (Just _)) `mappend` _ = x
    _ `mappend` y = y 
```

Enter fullscreen mode Exit fullscreen mode

让我们试试 repl:

```
λ> First' Nothing <> First' (Just 'A')
First' (Just 'A') 
```

Enter fullscreen mode Exit fullscreen mode

有用！

## 适用

让我们进入下一关。我们将使用这种语义编写*可应用的*实例。我们也可以重用幺半群实例。

```
instance Applicative (First' a) where
    pure _ = First' Nothing
    First' x <*> First' y = First' x <> First' y 
```

Enter fullscreen mode Exit fullscreen mode

检查:

```
λ> First' Nothing <*> First' (Just 'A')
First' (Just 'A')
λ> First' Nothing *> First' (Just 'A')
First' (Just 'A') 
```

Enter fullscreen mode Exit fullscreen mode

## 反转*也许*

为什么这是一个反向的*也许是*？

*也许*语义可以表述为:

*   *无事* - >失败，停止
*   *刚 x* - >继续跟 *x*

```
λ> Nothing *> Just 1
Nothing
λ> Just 1 *> Just 2
Just 2 
```

Enter fullscreen mode Exit fullscreen mode

*第一个'*语义是:

*   *没事* - >继续
*   *刚 x* - >结果是 *x* ，停

```
λ> First' Nothing *> First' (Just 2)
First' (Just 2)
λ> First' (Just 1) *> First' (Just 2)
First' (Just 1) 
```

Enter fullscreen mode Exit fullscreen mode

## 单子？

*First'* 虽然看起来不像是*单子*。但是我懒得证明。如果你能提供这样的证明，请让我看看。

## 常数

弗拉季斯拉夫·扎维亚洛夫[注意到](https://twitter.com/int_index/status/834126005577060353)第一个*仅仅是* 

```
type First' a b = Const (First a) b 
```

Enter fullscreen mode Exit fullscreen mode

其中*第一个*来自*数据。幺半群*。

的确，

```
λ> Const (First Nothing) *> Const (First (Just 2))
Const (First {getFirst = Just 2})
λ> Const (First (Just 1)) *> Const (First (Just 2))
Const (First {getFirst = Just 1}) 
```

Enter fullscreen mode Exit fullscreen mode

并且 *Const* 是另一个有趣的应用非单子的例子。*