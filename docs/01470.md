# 不再有美食、酒吧和巴兹

> 原文：<https://dev.to/warrend/no-more-foo-bar-and-baz-56j4>

当我还是个孩子的时候，我最好的朋友的爸爸拥有一家电脑公司，并告诉我们他知道 Fubar 的秘密。他说他发誓保守秘密，至今从未泄露过秘密。如果你不熟悉 Fubar，他是一个 20 世纪 80 年代的机器人，其名字代表未来的铀生物原子机器人。他看起来如此栩栩如生，我小时候就梦想拥有一辆。当然，现在我回头看老 Fubar [视频](https://www.youtube.com/watch?v=0b5YWvyLT5o)，很明显 Fubar 只不过是一个头上绑着对讲机的大型遥控车，可能是由附近房间里的一个家伙操作的。

这就引出了我的下一点。也许它们没有关联，但我想谈谈编码教程中“foo，bar，baz”的用法。我不太确定 foo/bar/baz 是否来自 Fubar，但它们听起来很相似，足以说明我的观点。

对于那些写教程的人，你们做了如此伟大的工作，我们很感激。没有教程，程序员的世界将会迷失。但是，看在上帝的份上，请停止在你的教程中使用 foo、bar 和 baz。

不幸的是，这种疾病已经达到了流行病的程度。我最近碰到一个教程，作者用一个类似下面的例子解释了 ActiveRecord 中关联的概念(我在这里是代码解释):

```
class Foo < ActiveRecord::Base
  has_many :bazzes
  has_many :bars, through: :bazzes
end 

class Bar < ActiveRecord::Base
  has_many :bazzes
  has_many :foos, through: buzzes
end 

class Baz < ActiveRecord::Base
  belongs_to :foo
  belongs_to :bar
end 
```

Enter fullscreen mode Exit fullscreen mode

首先，foo 和 baz 到底是什么？我们知道酒吧是什么，但我们谈论的是酒吧/酒馆还是平行酒吧？它和 foo 有什么关系？一个 foo 有多少个 bazzes？对我来说，这些概念中有很多需要花时间去理解，但是当这些例子没有基于现实的具体意义时，这几乎是不可能的。教程作者最好使用真实世界的例子，比如一条推文如何属于一个用户，或者一只狗如何有许多骨头。

所以请继续写教程，但是不要用 foo，bar，或者 baz！