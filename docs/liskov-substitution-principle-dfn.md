# 利斯科夫替代原理

> 原文：<https://dev.to/satansdeer/liskov-substitution-principle-dfn>

*原贴于[maksimivanov.com](http://maksimivanov.com/posts/liskov-substitution-principle)T3】*

1988 年，芭芭拉·利斯科夫写了一篇文章，现在它代表了《T4》中的《T2·L·T3》和《坚实的 T5》原则。让我们深入了解一下它是什么，以及它与 **TDD** 有什么关系。

下面是最初的表述:*“如果对于每一个类型为* **的对象**O1***S****有一个类型为* **T** *的对象* **o2** *这样，对于所有程序****用***T*****P 来定义*****

 ***简单来说:*“派生类对象必须可以替换基类对象。这意味着派生类的对象的行为方式必须与基类契约中的承诺一致。*

说得更简单一点:*“派生类对象应该* **补充** *，而不是* **替代** *基类行为。”*

[![liskov example](img/b11de5f874b85fd3245544a8d5e8271e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lCrcnvnY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://d33wubrfki0l68.cloudfront.net/23d137d68c64df9909d604e3b0e78ffa708dfbec/2f091/asseimg/liskov_1.png)

LSP 也可以被描述为[鸭子测试](https://en.wikipedia.org/wiki/Duck_test) : *“如果它看起来像鸭子，叫起来像鸭子，但需要电池——你可能有错误的抽象”*

## 所以，在现实世界中

如果你有某个类 **Foo** 和一个派生类 **SubFoo** ，那么如果你把 **Foo** 类的所有概念都改成 **SubFoo** ，程序执行应该不会改变，因为 **SubFoo** 不会改变 **Foo** 类的功能，而只是扩展它。

## 我们来看例子

回到鸭子。我们来描述一个`Duck`。我们对它的期望很低。我们只期望它会嘎嘎叫，别无他求。

```
describe('Duck', function(){
  describe('#quack', function(){
    it('produces "Quack" sound', function(){
      const duck = new Duck();
      expect(duck.quack()).toEqual('Quack');
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在让我们定义基本的鸭子。

```
class Duck{
  constructor(){
    // Duck initialization process
  }

  quack(){
    return 'Quack';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们运行规范，它通过了。酷，现在让我们创建一个派生类`MechanicalDuck`。它应该还会嘎嘎叫。唯一不同的是，它需要电池来运行。

```
class MechanicalDuck extends Duck{
  constructor(battery=null){
    super();
    this._battery = battery;
  }

  quack(){
    if(!this._battery){
      throw 'Need battery to operate.';
    }
    return 'Quack';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，根据 LSP，我们应该能够安全地将基类的实例更改为派生类的实例。让我们稍微改变一下规格，试着用`MechanicalDuck`代替`Duck`。

啊哦，测试失败了。需要电池才能嘎嘎叫。所以这里的`MechanicalDuck`显然不是鸭子。尽管它的界面看起来很相似，但它的**行为**完全不同。

## 但是什么是合适的子类呢？

在我们的例子中，它可能是一个`FemaleDuck`。我们来实施吧。

```
class FemaleDuck extends Duck{
  constructor(){
    super();
    // Initialization of female stuff
    this._butt = new FemaleDuckButt();
  }

  layAnEgg(){
    const egg = this._butt.layAnEgg();
    return egg;
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

将成功通过鸭子测试，因为我们没有改变行为，而只是扩展了它。我们的鸭子会下蛋了，万岁！***