# 数据导向编程

> 原文：<https://dev.to/alancampora/data-directed-programming>

# 介绍

几天前，我发现了一门关于计算机科学的课程，叫做[计算机程序的结构和解释](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-001-structure-and-interpretation-of-computer-programs-spring-2005/video-lectures/)，通过这门课，我学到了很多有趣的东西。**数据导向编程**是我今天想和大家分享的话题之一。

在接下来的文章中，我们将使用复数创建一个算术运算解释器。所以如果你喜欢数学和 **Lisp** 继续读这篇文章，如果不是，你会看到一些很酷的概念！

## 复数

首先，我们应该知道如何表示复数。TL；DR 任何复数都可以用两种形式表示:

矩形: **z = x + y * i** *使得* **x** *为实部* **y*i** : *为虚部。*

极坐标: **x = R*cos(a)** *和* **y = R*sen(a)** *这样* **R** *:代表星等* **a** *角度*

等价: **a = arct(y，x)** *和* **R = sqrt( x <sup>2</sup> ，y <sup>2</sup> )**

因此，我们有四个重要的常数，x，y，星等(R)和角度(a)。

让我们开始定义操作。在这个例子中，我们将使用加法和乘法。

> *z1+z2
> 实部(z1+z2) =(实部 z1) +(实部 z2)
> 虚部(z1+z2) =(虚部 z1) +(虚部 z2)*
> 
> *z1*z2
> 震级(z1*z2)=(震级 z1) *(震级 z2)
> 角度(z1*z2) =(角度 z1) +(角度 z2)*

数学够了！既然我们知道了复数是如何表示的，以及运算是如何完成的，我们就可以开始开发解释器了！

## 操作

前面的操作可以用下面的方式在 Lisp 中定义

```
 (define (+c z1 z2) 
    (create-from-real-imaginary
        (+(real-part z1) (real-part z2)) 
        (+(imaginary-part z1) (imaginary-part z2))))

(define (*c z1 z2) 
    (create-from-magnitude-angle
        (*(magnitude z1) (magnitude z2)) 
        (+(angle z1) (angle z2)))) 
```

# 我们来编码吧！

## 表示抽象数据

首先，我们需要做出一个重要的设计决策，我们如何表示这些数字？矩形形式似乎是一个非常简单的实现。因此，复数将使用具有实部和虚部(x，y)的有序对来表示。为了找到角度和大小，我们应该使用三角关系。

```
 (define (create-from-real-imaginary x y) 
    (cons x y )) ;;;cons returns a pair

(define (real-part z) 
    (car z )) ;;; car returns the first element

(define (imaginary-part z ) 
    (cdr z)) ;;; cdr returns the first element

(define (create-from-magnitude-angle r a ) 
    (cons (* r (cos a)) (* r (sin a))))

(define (angle z)
    (atan (imaginary-part z) (real-part z))

(define (magnitude z) 
    (sqrt (+ (square (real-part z)) (square (imaginary-part z)))) 
```

## 问题是

该解决方案运行良好，但几个月后，我们的合作伙伴 Hal 实施了其他策略。他选择了极坐标形式(r，a)来表示复数。

```
(define (create-from-magnitude-angle r a) 
    (cons (r a)) 

(define (angle z) 
    (car z))

(define (magnitude z)
    (cdr z))

(define (create-from-real-imaginary x y) 
    (cons (sqrt (+ (square x) (square Y)))
          (atan y x)))

(define (real-part z) 
    (* (magnitude z) (cos(angle z)))) 

(define (imaginary-part z) 
    (* (magnitude z) (sin(angle z)))) 
```

两种解决方案都可以。没有一个更好，但是现在我们有了两个不相容的表示，因为我们复制了定义。现在，我们希望两种表现形式共存，互不干扰。一张图胜过千言万语，所以让我们来看看我们想要创建的系统结构。
[![Interpreter structure](img/98dc1204e31569d96ae25017e570292d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jgkr3V_U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/bLzCQv/interpreter.png) 
解释器分为 2 层，一层为运算符，另一层为复数的表示。

## 第一种方法

### 标记数据

中间抽象层中的运算(实部、幅度、角度、虚部)应该不受复数的不同定义的影响。因此，这一层应该作为一个接口。但是我们如何实现这种行为呢？如果我们以下面的方式用类型标记数据会怎么样？

[![tagged data](img/254fba2c51ada30f6dc0514431521f9a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IsK8pOC3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/mdfJ0v/Screen_Shot_2017_05_30_at_22_38_52.png)

为了操作这些标记数据，我们应该实现一个函数，将类型附加到任何内容上。

```
(define (attach-type type-tag content)
    (cons type-tag content))

(define (content data)
    (cdr data))

(define (type data)
    (car data)) 
```

我们可以在哪里添加这种类型？构造函数听起来像是做这件事的好地方！让我们重构这两种表示

```
 ;;;our package 

(define (create-from-real-imaginary-rectangular x y) 
    (attach-type 'rectangular (cons x y )))

(define (real-part-rectangular z) 
    (car z )) 

(define (imaginary-part-rectangular z ) 
    (cdr z)) 

(define (create-from-magnitude-angle-rectangular r a ) 
    (attach-type 'rectangular 
        (cons (* r (cos a)) (* r (sin a)))))

(define (angle-rectangular z)
    (atan (imaginary-part z) (real-part x))

(define (magnitude-rectangular z) 
    (sqrt (+ (square (real-part z)) (square (imaginary-part z))))

;;;Hal's package

(define (create-from-magnitude-angle-polar r a) 
    (attach-type 'polar (cons (r a)))

(define (angle-polar z) 
    (car z))

(define (magnitude-polar z)
    (cdr z))

(define (create-from-real-imaginary-polar x y) 
    (attach-type 'polar 
        (cons (sqrt (+ (square x) (square Y))) 
              (atan y x))))

(define (real-part-polar z) 
    (* (magnitude z) (cos(angle z)))) 

(define (imaginary-part-polar z) 
    (* (magnitude z) (sin(angle z)))) 
```

在这个重构中实现了两个主要变化。

*   每个构造函数都在每个复数的开头附加类型，`'rectangular`或`'polar`。
*   更改函数名以避免命名冲突

关于操作+c *c，我们还是可以用的。因为每种表示法在不同的场景下都能很好地工作，所以让我们同时使用它们。

```
(define (create-from-real-imaginary x y)
    (create-from-real-imaginary-rectangular xy))

(define (create-from-magnitude-angle r a)
    (create-from-magnitude-angle-polar r a)) 
```

### 按类型调度

一旦数据被标记为每种类型，一旦我们对两种表示有了不同的函数，就有可能实现一个* *管理器*，它将负责调用相应的函数。

```
;;;Manager
(define (rectangular? z)
    (eq? 'rectangular (type z)))

(define (polar? z)
    (eq? 'polar (type z)))

(define (real-part z)
    (cond (rectangular? (type z))
          (real-part-rectangular (content z))
          (real-part-polar (content z))))  

(define (imaginary-part z)
    (cond (rectangular? (type z))
          (imaginary-part-rectangular (content z))
          (imaginary-part-polar (content z))))

(define (angle z)
    (cond (rectangular? (type z))
          (angle-rectangular (content z))
          (angle-polar (content z))))

(define (magnitude z)
    (cond (rectangular? (type z))
          (magnitude-rectangular (content z))
          (magnitude-polar (content z)))) 
```

每个函数将检查接收到的数据的类型，之后，管理器知道应该调用哪个表示。在每种情况下，被调用函数的参数将是数据的内容。这种技术在 型调度上被命名为 ***，我们用它来保持我们系统的模块化。
无论如何，这种解决方案有两个主要缺点***

*   有必要为每个表示添加许多不同的条件。
*   开发人员必须保证没有两个函数同名。

## 解决方案

### 数据导向编程

经理有必要吗？它负责调用不同的函数，但它不做太多。事实上，它的行为如下表所示

[![Manager](img/a030d33fdbfe26b66e46015c04a87a53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ax4vkdm9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/m1GKqv/Screen_Shot_2017_05_30_at_19_00_59.png)

当它匹配类型和操作符时，它知道要应用的正确函数。

但是让我们假设我们的系统有两个函数在内部表示这个表

*   `put operator-key type-key item`
*   `get operator-key type-key`

使用`put`，每个开发人员负责以如下方式将他们的函数安装到系统表中

```
 ;;;mount functions in system

    (put 'rectangular 'real-part 
        (lambda(z) (car z) )
    (put 'rectangular 'imaginary-part 
        (lambda(z)(cdr z)))
    (put 'rectangular 'angle 
        (lambda(z)(atan(imaginary-part z) (real-part z)
    (put 'rectangular 'magnitude 
        (lambda(z)(sqrt (+ (square (real-part z)) (square (imaginary-part z)))))

    (put 'rectangular 'create-from-real-imaginary
        (lambda( x y ) 
            (attach-type 'rectangular (cons x y ))

    (put 'rectangular 'create-from-magnitude-angle
        (lambda( r a ) 
            (attach-type 'rectangular 
                (cons (* r (cos a)) (* r (sin a)))))) 
```

为了完全摆脱经理，我们需要创建一个与操作员的接口。

```
(define (apply operator arg)
    (let (fn (get (type arg) operator )))
    (if (not (null? fn ))
        (fn (content arg))
        ("error: no operator")))

(define (real-part z) 
    (apply 'real-part z))
(define (imaginary-part z) 
    (apply 'imaginary-par z))
(define (angle z) 
    (apply 'angle z))
(define (magnitude z) 
    (apply 'magnitude z)) 
```

与之前的解决方案相比，它的优势在于我们没有一个**中央控制点**，事实上责任被转移到了每个代表。他们使用函数`put`将自己安装到系统上。此外，如果有 100 个新的复数表示，上面的代码不会有任何变化！
为了解决命名冲突，我们现在使用 lambda 表达式。

# 结论

总而言之，对我来说，这是我第一次听说这些概念。在它们背后，我们真正想做的是为表示创建一个多态。

我希望这个例子对你们所有人来说够清楚了！通常，我们在工作时远离数学，但从我的角度来看，混合编程和数学也很有趣。

此外，一切都是使用 lisp (scheme)实现的，这是一种美丽的语言=D(我知道你在想什么，你不喜欢看到许多`()()()`但你会习惯它们的，我保证)。

感谢阅读！