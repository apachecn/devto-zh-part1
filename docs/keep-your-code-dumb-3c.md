# 让你的代码保持沉默

> 原文：<https://dev.to/joelnet/keep-your-code-dumb-3c>

我最近偶然发现了这条推文...

> ![JavaScript Daily profile image](img/d637a6e0403821686eec414f379fb1d3.png)JavaScript Daily@ JavaScript Daily![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)用懒惰的价值观加速你的 JS Apps:[bitovi.com/blog/lazy-valu…](https://t.co/6Y1uPClHfc)23:21PM-12 2017 年 11 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=929851552172519424)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=929851552172519424)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=929851552172519424)

建议的解决方案是使用一个 getter 在需要的时候填充值，类似于下面的内容:

```
function getUniqueId() {
  // pretend this is some expensive computation
  return '--id--'
}

function MyObject () {
}

// lazy getter for 'id'
Object.defineProperty(MyObject.prototype, 'id', {
  get: function() {
    const value = getUniqueId()
    Object.defineProperty(this, 'id', { value, enumerable: true })
    return value
  }
})

const obj = new MyObject()
console.log(obj) // > {}
console.log(obj.id) // > "--id--"
console.log(obj) // > { id: "--id--" } 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，这段代码看起来*非常聪明*。它今天可能工作得很好，但是像这样的代码错误以后将很难被追踪到，甚至对代码库来说是灾难性的。

我维护着一个大型的遗留 C#项目，该项目目前有懒惰的 getters。这段代码是许多年前由过去的程序员的鬼魂写的，当时写得非常聪明。

类似这样的:

```
// BAD: clever code
public class House
{
  private _cats Cat[];

  public int Id { get; set; }

  // Lazy property that "does work". "work" should be in a function, not prop.
  public Cat[] Cats
  {
    get
    {
       if (_cats == null)
       {
         _cats = db.GetCats(Id);
       }

       return _cats;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

今天这个聪明的代码正在咬我们的屁股。

最近有一个项目使用 Redis 进行缓存。人们意识到(在投入生产后),现在每个惰性值都在缓存的序列化过程中被枚举。它最终导致生产中的 CPU 出现如此大的峰值，以至于不得不回滚部署。

因为在那个部署中有很多东西，所以团队花了一些时间来确定是新的 Redis 代码导致了 CPU 峰值，甚至花了更长的时间来确定*为什么*Redis 代码导致了 CPU 峰值。

我们是否使用了更愚蠢的代码，比如...

```
// GOOD: dumb code
public class House
{
  public Cat[] GetCats()
  {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

...我们绝不会碰到这个问题。

代码库现在超过一百万行，对一个不应该存在的问题的修复是巨大的。至今仍未修复。

这不是第一次同样聪明的代码咬我们的屁股。在代码中有些地方我会看到这样的东西:

```
// BAD: In this code, `house.Cats` has to be touched
//      in order for the property to be populated.

var house = db.GetHouse(id);

// WTF?
house.Cats;

return DoSomething(house); 
```

Enter fullscreen mode Exit fullscreen mode

这种类型的代码要求我深入研究属性`id`的实现，以理解它的用法。这种代码味道太难闻了，让我难受。

我见过太多次聪明的代码回来困扰代码库。

聪明的代码会让你去寻找和解决同样聪明的错误。让你的程序保持沉默，你的代码和你的错误会更容易被发现。

我很想在下面的评论中听到关于你是如何被聪明的代码咬到的故事！

在 Twitter [@joelnet](https://twitter.com/joelnet) 或 [LinkedIn](https://www.linkedin.com/in/joel-thoms/) 上关注我

干杯！