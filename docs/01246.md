# 掌握一些高级设计模式:资源库

> 原文：<https://dev.to/theodesp/mastering-some-advanced-design-patterns-resource-pool-1l64>

[![img](img/d8e30015b8358685ed9a0ccf8c06b2e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zT-zc2ZY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/76f0fueyu447wkccfx9d.jpg)

[**阅读原文**](https://codeburst.io/mastering-some-advanced-design-patterns-resource-pool-7197edf1d1cc)

晚上好，新年快乐。我相信你假期过得很愉快。

我希望，我没有耽误你太长时间，因为上一个系列是关于**坚实的原则**。它非常成功，以至于它们被 Codeburst 列为 2017 年 Codeburst 上最受欢迎的[软件开发文章/教程之一](https://codeburst.io/software-development-best-of-2017-d2b7825e9ac0)。为此感谢大家。

所以我决定保持这种势头，并与您分享一些其他的软件工程模式和食谱，我相信这些模式和食谱非常棒，您应该知道它们。

这个系列是关于探索一些[设计模式](https://en.wikipedia.org/wiki/Software_design_pattern)，但不是你从最初的[4 人组的书](https://en.wikipedia.org/wiki/Design_Patterns)中知道的那些。我不想无礼，但这些年来，原始文章已经过彻底的剖析和分析，并就此发表了大量的文章和意见。我不是简单地想一遍又一遍地重复整个事情。

正如我所说的，我想把重点放在其他一些不太受欢迎的模式上，这些模式在许多软件解决方案中最常见，是针对特定问题的最合适的设计决策。这些也是我最有经验的模式。当然还有其他我不知道的有用模式，但是除了它们的描述链接之外，我无法给你任何帮助。

以下是所选设计模式的列表:

*   **延迟加载模式**
*   **模型-视图-控制器模式**
*   **拦截器模式**
*   **发布-订阅模式**
*   **活动记录模式**
*   **资源池模式**

它们中的每一个都有自己的特点，所以我将尝试用简单的步骤和简单的代码示例来描述它们。

然后我们开始使用**资源池模式**。

## 资源池模式

> 如果初始化对象实例的成本很高，那么最好不要构造新对象，而是根据需要从池中检索可重用对象，并将其释放到池中。

这种模式背后的主要思想是，如果您必须使用一个创建成本太高的对象，例如数据库连接，为什么不让**管理器**为您处理所有的脏细节。

大多数情况下，您不希望过于明确地管理数据库连接的生存期，因为很多事情可能会出错。理想情况下，你会喜欢随时可以使用的东西。

资源池将处理为客户端预先保留这些对象的整个过程，当客户端完成对这些对象的处理后，会将它们释放回来，为下一个请求相同对象的客户端做好准备。资源池可能会进行额外的工作，处理对象的重新连接或紧急处理，以便保持较低的内存占用。

您所需要做的就是提供创建和处置资源池在其接口定义中需要的对象的具体方法。

“**昂贵的**物品的一些例子有:

*   例如，来自数据库的 TCP 连接
*   在使用前需要初始化一些数据的对象。
*   引用可能随时关闭的远程连接的对象。
*   一块记忆。

让我们看看如何用代码来实现。

## 实现

在其基本形式中，资源池只是一个包含 **2 列表**的对象。第一个列表包括可以立即使用的对象。第二个列表包含客户端已经在使用的对象。我们需要这个列表，以便跟踪池的使用情况，更重要的是防止内存泄漏，以防客户端忘记归还内存。

先说一些定义。

```
type PooledObject interface {
   Reset()
}

// All pooled object factories must satisfy this interface
type PooledObjectFactory interface {
   Create() (PooledObject, error)
}

// All pool implementations must satisfy this interface
type Pool interface {
   Get() (PooledObject, error)
   Return(obj PooledObject) error
} 
```

`PooledObject`是实现`Reset`方法的任何对象。这是为了提供一种将资源重置为初始状态的方法。

`PooledObjectFactory`只是一个**工厂**对象，它将被池用来创建新的池对象。

该池必须只实现两个方法`Get`和`Return`。

因此，让我们首先用池实现来满足这些接口定义。

对于本例，我们希望创建一个固定大小的池，它允许由变量`capacity`指定的一定数量的对象

```
/**
 * Fixed size Object pool
 */
type FixedPool struct {
   // List of available objects to share
   available []PooledObject
   // List of in-use objects that are currently reserved
   inUse []PooledObject
   // Maximum size permitted
   capacity int
   // For protecting updates
   mu *sync.Mutex
   // For creating the Objects
   factory PooledObjectFactory
} 
```

这里我们定义了两个 PooledObject 列表、 **capacity** 和 PooledObjectFactory。

现在，当我们从池中请求一个对象时，需要做的是检查可用的列表是否为空。如果它是空的，就使用工厂创建一个新的对象，并把它推到**使用列表中，确保我们没有超出容量。如果它不为空，我们就返回列表中第一个可用的。** 

```
func (p *FixedPool) Get() (PooledObject, error) {
   p.mu.Lock()

   var obj PooledObject
   var err error

   if len(p.available) == 0 {
      // Make sure we don't exceed capacity
      if len(p.inUse) == p.capacity {
         err = errors.New("fixed Pool reached maximum capacity")
      } else {
         obj, _ = p.factory.Create()
         p.inUse = append(p.inUse, obj)
      }
   } else {
      // pop
      obj, p.available = p.available[0], p.available[1:]
      err = nil
      p.inUse = append(p.inUse, obj)
   }

   p.mu.Unlock()

   return obj, err
} 
```

对于`Return`方法，我们做相反的事情。我们调用`Reset`方法，使资源正确地返回到初始状态，然后我们试图在 inUse 列表中找到对象引用。如果找到了，我们只需将其从那里移除，并将其推送到可用列表中，为下一个客户端做好准备。如果我们没有找到对象，我们会返回一个错误，因为我们不能确定这个对象是从哪里产生的。

```
func (p *FixedPool) Return(obj PooledObject) error {
   obj.Reset()

   p.mu.Lock()
   if idx := findIndex(obj, p.inUse); idx != -1 {
      // Delete at index
      p.inUse = append(p.inUse[:idx], p.inUse[idx+1:]...)
      p.available = append(p.available, obj)
      err = nil
   } else {
      err = errors.New("unrecognized pooled object returned")
   }
   p.mu.Unlock()

   return err
}

func findIndex(target PooledObject, slice []PooledObject) int {
   for idx, obj := range slice {
      if target == obj {
         return idx
      }
   }

   return -1
} 
```

从这个简单的示例中可以看出，扩展这个池并添加额外的功能并不困难，例如池统计、超时或取消容量限制。由你决定是否延长。

您可以在我的报告中找到该模式的示例用法:

[**theodesp/go 对象池**](https://github.com/theodesp/go-object-pool)

## 告诫

资源池并非没有问题。非常重要的是，当客户端不再使用这些对象时，应该释放它们。有很多这样的例子，当客户端“**忘记了**释放所有的资源，这将导致内存泄漏或性能下降的问题。对于寿命很短的对象，您可能会发现提前创建许多对象的开销太大，因此需要将资源池配置为具有正确的大小或创建策略。

## 结论

拥有资源池是一件好事，因为它们为可重用对象提供了一个很好的平台，这些对象可以被预先保留，因此当正确使用时，它可以帮助提高应用程序的性能。我希望你明白它们的用处和长期利益。

## 习题

*   提供一种可以正确关闭资源库的方法。因此，我们需要向名为`Close`的 PooledObject 添加一个额外的接口方法，以便关闭资源。例如，您可以使用它来关闭单个数据库连接。

*   提供一个名为`TimedGet(d time.Duration)`的新方法，该方法将尝试在提供的持续时间内获取一个对象。如果超过这个时间限制，则返回一个错误。这个方法应该在超时期间阻止客户端调用它。

## 参考文献

您可以在以下网站上找到有关资源库的更多信息:

*   [**Wiki 文章**](https://en.wikipedia.org/wiki/Object_pool_pattern) :对象池模式的一个很好的概述
*   [**源制作篇**](https://sourcemaking.com/design_patterns/object_pool) :资源库部分
*   [**OODesign 文章**](http://www.oodesign.com/object-pool-pattern.html) :传统网站关于 OO 的设计原则

**接下来是懒加载模式。**