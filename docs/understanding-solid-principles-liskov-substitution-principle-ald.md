# 理解坚实的原则:利斯科夫替代原则

> 原文：<https://dev.to/theodesp/understanding-solid-principles-liskov-substitution-principle-ald>

[![image](img/6e01bb469a424305f73a9db2d7f3933b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tpYIUJWy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8c53iit64r5b9qro3blk.jpg)

[**此处阅读原文**](https://codeburst.io/understanding-solid-principles-liskov-substitution-principle-e7f35277d8d5)

*这是理解* *坚实* *原则系列的第 5 部分，也是最后一部分，我们将探讨什么是**利斯科夫替代原则**，以及为什么它有助于编码到抽象，而不是总是编码到具体实现，从而使代码可维护和可重用。*

作为一个小提醒，在 **SOLID** 中有五个基本原则可以帮助创建好的(或坚实的)软件架构。SOLID 是一个缩写词，其中:-

**S** 代表 SRP(单一责任原则)
**O** 代表 OCP(开闭原则)
**L** 代表 LSP(利斯科夫替代原则)
**I** 代表 ISP(接口分离原则)
**D** 代表 DIP(依赖倒置原则)

我们之前讨论过[依赖倒置](https://codeburst.io/understanding-solid-principles-dependency-injection-d570c15560ab)、[单责任](https://codeburst.io/understanding-solid-principles-single-responsibility-b7c7ec0bf80)、[开闭原则](https://medium.com/r/?url=https%3A%2F%2Fcodeburst.io%2Funderstanding-solid-principles-open-closed-principle-e2b588b6491f)和[接口分离原则](https://codeburst.io/understanding-solid-principles-interface-segregation-principle-b2d57026cf6c)。

现在我们要讨论利斯科夫替代原理。

## 利斯科夫替代原理

> 对象应该可以用其子类型的实例替换，而不改变程序的正确性。

它真正的意思是，如果你传递一个抽象的子类，你需要确保你没有改变父抽象的任何行为或状态语义。

如果你选择不这样做，你将遭受:

*   阶级等级会很混乱。奇怪的行为会发生。
*   超类的单元测试永远不会对子类成功。这将使你的代码难以测试和验证。

通常，如果您修改了在其他方法中使用的父对象的不相关内部或私有变量，就会发生这种情况。这是对对象本身的一种偷袭，如果你不小心的话，它随时都可能发生，不仅仅是通过子类。

让我们来看一个例子，我们所说的改变行为是什么意思，以及它是如何影响结果的。假设我们有一个基本的`Store`抽象，将消息存储在内存数据结构中，直到达到最大存储限制。现在使用这个商店的客户希望当他们调用`retrieveMessages`时得到所有的消息。

```
interface Store {
    store(message: string);
    retrieveMessages(): string[];
}
const STORE_LIMIT = 5;
class BasicStore implements Store {
   protected stash: string[] = [];
   protected storeLimit: number = STORE_LIMIT;

   store(message: string) {
     if (this.storeLimit === this.stash.length) {
         this.makeMoreRoomForStore();
      }
      this.stash.push(message);
    }

    retrieveMessages(): string[] {
      return this.stash;
    }
    makeMoreRoomForStore(): void {
       this.storeLimit += 5;
    }
}
class RotatingStore extends BasicStore {
    makeMoreRoomForStore() {
        this.stash = this.stash.slice(1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`RotatingStore`是如何做一些偷偷摸摸和奇怪的事情，以及它如何微妙地修改`BasicStore`的语义。它修改存储并销毁进程中的一条消息，以便为更多的消息腾出空间。如果我们试图利用一个 Storer 抽象，但是我们以破坏性的`RotatingStore`告终，我们的消息就会有问题。

```
const st: Store = new RotatingStore()// or from a factory
st.store("hello")
st.store("world")
st.store("how")
st.store("are")
st.store("you")
st.store("today")
st.store("sir?")
st.retrieveMessages() // Ooops some messages are gone 
```

Enter fullscreen mode Exit fullscreen mode

一些消息将会消失，这违反了消息在被检索时应该可用的基本要求。

为了避免这些奇怪的情况，建议调用公共父方法来获取子类中的结果，而不要直接使用内部变量。这样你就可以确保父抽象达到所需的状态，而没有副作用或无效的状态转换。

**还建议尽可能保持你的基本抽象简单明了，**使其易于被子类扩展。创建一个让子类容易覆盖的胖基类是没有意义的；从而引起行为改变。

总是这样做可能可行，也可能不可行，但从内部系统健康检查的角度来看，做**后条件检查**是个好主意，这样可以验证子类没有扰乱一些关键的代码路径。我还没有看到这个想法在实践中，所以我不能给你一个例子。

另一方面，代码审查帮助很大。在开发过程中，你可能会意外地造成比你意识到的更多的损害，所以对你的代码变更多加关注是很重要的。重要的是保持代码设计的一致性，并且尽早地、经常地查明改变对象层次结构的潜在修改。

最后，我之前提到过考虑使用复合而不是继承吗？如果不是你就应该 [***读那个***](https://www.thoughtworks.com/insights/blog/composition-vs-inheritance-how-choose) ***。*T11】**

### 结论

正如你所看到的，当你开发软件时，只需要很小的努力就可以让事情变得复杂和错误。牢记这些原则，理解它们的来源以及它们试图解决的问题，会让你的工作变得更容易、更合理。这不是一件小事，相反，这应该是你在执行任务时思考过程的一部分。试着让你的设计对不利的修改和偷偷摸摸的抛出有弹性

### 参考文献

*   [ObjectMentor 文章](http://www.objectmentor.com/resources/articles/lsp.pdf)
*   [维基页面](https://en.wikipedia.org/wiki/Liskov_substitution_principle)

很遗憾，这个系列已经结束了。我希望你学到了很多，我帮助你在实践中更好地理解这些原则。请继续关注更多的系列文章，因为我有相当多的草稿。

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。

**如果您想安排一次指导会议，请访问我的[共同导师简介](https://www.codementor.io/theofanisdespoudis)** 。