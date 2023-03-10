# 做一个不同的汉堡

> 原文：<https://dev.to/jtlunsford/build-a-different-burger>

# 要点

*   使用默认值最小化初始参数
*   在这个过程中的任何时候都可以选择 getBurger()

我喜欢尼山的汉堡店的文章。它利用了我多年来一直使用的模式。但是我似乎只对类使用了这种链接模式，很少使用其他方式。最近，我已经不再使用 class 关键字进行更函数式的编程了。如果我必须在我目前正在开发的应用程序中创建一个汉堡生成器，下面是它的样子。

```
'use strict'

const burger = (opts = {}) => {
    let {
        size = 14,
        cheese = false,
        pepperoni = false,
        lettuce = false,
        tomato = false
    } = opts

    const getBurger = () => Object.assign({},{
        size,
        cheese,
        pepperoni,
        lettuce,
        tomato
    })

    const add = topping => ({
        'cheese': () => cheese = true,
        'pepperoni': () => pepperoni = true,
        'lettuce': () => lettuce = true,
        'tomato': () => tomato = true
    }[topping]())

    return {
        getBurger,
        add
    }
}

const myBurger = burger(14)
myBurger.add('cheese')
myBurger.add('lettuce')
myBurger.add('tomato')
myBurger.add('pepperoni')
console.log(myBurger.getBurger()) 
```

Enter fullscreen mode Exit fullscreen mode