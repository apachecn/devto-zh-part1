# Clojure 上的地图查找的后退(或者宏的简单介绍)

> 原文：<https://dev.to/rodrigoflores/fallback-on-maps-lookup-on-clojure-or-a-gentle-introduction-to-macros-9d9>

所以，你必须处理一个映射，但是你不能确定你是否有一个给定键的值。

最简单的，也可能是最广为人知的是使用 get 函数(可选地接受第三个参数，即回退值)。

```
(def  a-qb  {:name  "Josh"  :surname  "McCown"  :team  "New York Jets"})  (def  another-qb  {:name  "Robert"  :surname  "Griffin III"})  (get  a-qb  :team)  ; => "New York Jets"  (get  another-qb  :team)  ; => nil  (get  another-qb  :team  "Free Agent")  ; => "Free Agent" 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是将[关键字作为函数](https://clojure.org/reference/data_structures#Keywords)使用，甚至将地图作为函数使用。

```
(:team  a-qb)  ; => "New York Jets"  (:team  another-qb)  ; => nil  (a-qb  :team)  ; => "New York Jets"  (another-qb  :team)  ; => nil 
```

Enter fullscreen mode Exit fullscreen mode

我了解到，你也可以为这些情况指定第二个参数，如果没有找到值，它将返回第二个参数。

```
(:team  a-qb  "Free Agent")  ; => "New York Jets"  (:team  another-qb  "Free Agent")  ; => "Free Agent"  (a-qb  :team  "Free Agent")  ; => "New York Jets"  (another-qb  :team  "Free Agent")  ; => "Free Agent" 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您想引发一个不存在的异常，该怎么办呢？

```
(a-qb  :team  (throw  (Exception.  )))  (a-qb  :team  (throw  (Exception.  ))) 
```

Enter fullscreen mode Exit fullscreen mode

如果以这种方式调用它，它将在函数被调用之前评估所有参数(因为它是一个函数)，不管键-值对在映射上是否存在，都会引发一个异常。解决这个问题的方法是使用一个`or`。

```
(or  (a-qb  :team)  (throw  (Exception.  )) 
```

Enter fullscreen mode Exit fullscreen mode

因为`or`是作为一个宏实现的，它将只评估第二个 sexp `(throw ...)`,除非第一个 sexp 是 falsy(零或假),因此只在这种情况下引发异常。