# C#编译器中的“降低”(以及误用它会发生什么)

> 原文：<https://dev.to/mattwarren/lowering-in-the-c-compiler-and-what-happens-when-you-misuse-it>

原来，我一直认为的“*编译器魔术*或“*语法糖*”实际上被称为技术术语“*降低*”，C#编译器(又名[罗斯林](https://github.com/dotnet/roslyn))广泛使用它。

但这是什么呢？这句话来自[所以你想写你自己的语言？](http://www.drdobbs.com/architecture-and-design/so-you-want-to-write-your-own-language/240165488?pgno=2)给了我们一些想法:

> 降低
> 一种语义技巧被称为“降低”，这在事后看来是显而易见的(但安德烈·亚历山德雷斯库向我指出了这一点)从内部来说，它包括用简单的语义结构重写更复杂的语义结构。例如，while 循环和 Foreach 循环可以根据 for 循环重写。然后，剩下的代码只需要处理 for 循环。这揭示了在 D 中如何实现 while 循环的几个潜在错误，这是一个很好的胜利。它还用于重写 try-finally 语句等方面的范围保护语句。在语义处理中可以找到这一点的每一种情况都将有利于实现。
> 
> ——作者:沃尔特·布莱特(D 编程语言的作者)

但是如果你仍然不确定这是什么意思，读一读 Eric Lippert 关于这个主题的文章,[语言设计中的降低](https://ericlippert.com/2014/04/28/lowering-in-language-design-part-one/),其中有这样一段话:

> 一种常见的技术是让编译器“在同一种语言中从高级语言特性降低到低级语言特性”。

* * *

顺便说一句，如果你喜欢阅读关于罗斯林编译器源代码的文章，你可能会喜欢我写的其他文章:

*   [如何使用“固定”关键字？](%7B%7B%20base%20%7D%7D/2016/10/26/How-does-the-fixed-keyword-work/?recommended=1)
*   [罗斯林代码库-性能课程(第一部分)](%7B%7B%20base%20%7D%7D/2014/06/05/roslyn-code-base-performance-lessons-part-1/?recommended=1)
*   [罗斯林代码库-性能课程(第二部分)](%7B%7B%20base%20%7D%7D/2014/06/10/roslyn-code-base-performance-lessons-part-2/?recommended=1)

* * *

## ‘降低’是什么样子的？

C#编译器已经使用了一段时间，其中一个最古老或最公认的例子是当这段代码:

```
using System.Collections.Generic;
public class C {
    public IEnumerable<int> M() 
    {
        foreach (var value in new [] { 1, 2, 3, 4, 5 })
        {
            yield return value;
        }
    }
} 
```

变成了这样

```
public class C
{
    [CompilerGenerated]
    private sealed class <M>d__0 : IEnumerable<int>, IEnumerable, IEnumerator<int>, IDisposable, IEnumerator
    {
        private int <>1__state;
        private int <>2__current;
        private int <>l__initialThreadId;
        public C <>4__this;
        private int[] <>s__1;
        private int <>s__2;
        private int <value>5__3;
        int IEnumerator<int>.Current
        {
            [DebuggerHidden]
            get
            {
                return this.<>2__current;
            }
        }
        object IEnumerator.Current
        {
            [DebuggerHidden]
            get
            {
                return this.<>2__current;
            }
        }
        [DebuggerHidden]
        public <M>d__0(int <>1__state)
        {
            this.<>1__state = <>1__state;
            this.<>l__initialThreadId = Environment.CurrentManagedThreadId;
        }
        [DebuggerHidden]
        void IDisposable.Dispose()
        {
        }
        bool IEnumerator.MoveNext()
        {
            int num = this.<>1__state;
            if (num != 0)
            {
                if (num != 1)
                {
                    return false;
                }
                this.<>1__state = -1;
                this.<>s__2++;
            }
            else
            {
                this.<>1__state = -1;
                this.<>s__1 = new int[] { 1, 2, 3, 4, 5 };
                this.<>s__2 = 0;
            }
            if (this.<>s__2 >= this.<>s__1.Length)
            {
                this.<>s__1 = null;
                return false;
            }
            this.<value>5__3 = this.<>s__1[this.<>s__2];
            this.<>2__current = this.<value>5__3;
            this.<>1__state = 1;
            return true;
        }
        [DebuggerHidden]
        void IEnumerator.Reset()
        {
            throw new NotSupportedException();
        }
        [DebuggerHidden]
        IEnumerator<int> IEnumerable<int>.GetEnumerator()
        {
            C.<M>d__0 <M>d__;
            if (this.<>1__state == -2 && this.<>l__initialThreadId == Environment.CurrentManagedThreadId)
            {
                this.<>1__state = 0;
                <M>d__ = this;
            }
            else
            {
                <M>d__ = new C.<M>d__0(0);
                <M>d__.<>4__this = this.<>4__this;
            }
            return <M>d__;
        }
        [DebuggerHidden]
        IEnumerator IEnumerable.GetEnumerator()
        {
            return this.System.Collections.Generic.IEnumerable<System.Int32>.GetEnumerator();
        }
    }
    [IteratorStateMachine(typeof(C.<M>d__0))]
    public IEnumerable<int> M()
    {
        C.<M>d__0 expr_07 = new C.<M>d__0(-2);
        expr_07.<>4__this = this;
        return expr_07;
    }
} 
```

呀，我很高兴我们不用自己写代码了！！这里有一个完整的状态机，它允许我们的原始代码在每次循环时暂停/恢复(在‘yield’语句处)。

* * *

## C #编译器和‘降级’

但事实证明，罗斯林编译器比你想象的要“降低”得多。如果你看一下['/src/Compilers/cs harp/Portable/Lowering '](https://github.com/dotnet/roslyn/tree/master/src/Compilers/CSharp/Portable/Lowering)(VB.NET[等价于这里的](https://github.com/dotnet/roslyn/tree/master/src/Compilers/VisualBasic/Portable/Lowering))下的代码，你会看到下面的文件夹:

*   [异步重写器](https://github.com/dotnet/roslyn/tree/master/src/Compilers/CSharp/Portable/Lowering/AsyncRewriter)
*   [迭代器编辑器](https://github.com/dotnet/roslyn/tree/master/src/Compilers/CSharp/Portable/Lowering/IteratorRewriter)
*   [羔羊作者](https://github.com/dotnet/roslyn/tree/master/src/Compilers/CSharp/Portable/Lowering/LambdaRewriter)
*   [StateMachineRewriter](https://github.com/dotnet/roslyn/tree/master/src/Compilers/CSharp/Portable/Lowering/StateMachineRewriter)

这对应于一些你可能熟悉的 C#语言特性，比如“lambdas”，即`x => x.Name > 5`、`yield`(如上)使用的“迭代器”和`async`关键字。

但是，如果我们更深入地观察一下，在[“local rewriter”文件夹](https://github.com/dotnet/roslyn/tree/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter)下，我们可以看到更多我们可能从未考虑过的“降低”场景，例如:

*   [代表创建](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_DelegateCreationExpression.cs)
*   [事件](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_Event.cs)
*   [‘固定’关键字](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_FixedStatement.cs)
*   [ForEach 循环](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_ForEachStatement.cs)
*   [‘是’运算符](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_IsOperator.cs)
*   [‘锁定’语句](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_LockStatement.cs)
*   [’？.'又名零合并](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_NullCoalescingOperator.cs)
*   [‘stack alloc’关键字](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_StackAlloc.cs)
*   ['String.Concat()'](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_StringConcat.cs)
*   [‘切换’语句](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_SwitchStatement.cs)
*   [‘扔’的表情](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_ThrowStatement.cs)
*   [‘使用’语句](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_UsingStatement.cs)
*   甚至还有一个[‘while’循环](https://github.com/dotnet/roslyn/blob/master/src/Compilers/CSharp/Portable/Lowering/LocalRewriter/LocalRewriter_WhileStatement.cs)

因此，非常感谢所有过去和现在的 C#语言开发人员和设计人员，他们为我们做了所有这些工作。想象一下，C#没有所有这些高级特性，我们就只能手工编写了。

这就像编写 **Java** :-)

* * *

## 误用会怎样

但是，当然真正有趣的部分是“误用”或公然“滥用”编译器。所以我设立了一个小小的 [twitter 竞赛](https://twitter.com/matthewwarren/status/867753577346985984),看看我们能让编译器为我们做多少“降低”(即代码的“输入”行与“输出”行的最高比率)。

它有以下规则(参见[此要点](https://gist.github.com/mattwarren/3c7cfaa245effc0a318b87f1ee5dc153)了解更多信息):

1.  在方法`M()`中可以有任意多的行
2.  任何单行都不能超过 100 个字符
3.  要获得您的分数，请用“扩展行数”除以“原始行数”
    1.  基于 [https://sharplab.io](https://sharplab.io/#b:master/f:r/) 的默认**输出**格式，不允许重新格式化！！
    2.  但是你可以随意格式化**输入**,也就是说，利用全部 100 个字符
4.  必须在没有警告的情况下编译 [https://sharplab.io](https://sharplab.io) (允许 C# 7 特性)
    1.  但是在运行时不需要做任何明智事情
5.  您不能修改已经存在的代码，即`public class C {}`和`public void M()`
    1.  不能只把`async`加到`public void M()`上，那太简单了！！
6.  您可以添加新的`using ...`声明，这些声明不计入行数

例如，使用以下代码(交互式版本可在 [sharplab.io](https://sharplab.io/#b:master/f:r/K4Zwlgdg5gBAygTxAFwKYFsDcBYAUAB2ACMAbMAYxnJIEMQQYBhGAbzxg5kNIpgDcA9mAAmMALIAKAJSt2neQDFgEcgB4UAJ0hQAfDDQoYAXhjTjegESkaACws5c8gL54nQA) 上获得):

```
using System;
public class C {
    public void M() {
        Func<string> test = () => "blah"?.ToString();
    }
} 
```

这算作原始代码的 **1** 行(只计算方法`M()`内的代码)

这扩展到 **23** 行(同样只计算`class C`的括号(`{`，`}`)内的代码行)。

给出 **23** (23 / 1)的**总分**

```
....
public class C
{
    [CompilerGenerated]
    [Serializable]
    private sealed class <>c
    {
        public static readonly C.<>c <>9;
        public static Func<string> <>9__0_0;
        static <>c()
        {
            // Note: this type is marked as 'beforefieldinit'.
            C.<>c.<>9 = new C.<>c();
        }
        internal string <M>b__0_0()
        {
            return "blah";
        }
    }
    public void M()
    {
        if (C.<>c.<>9__0_0 == null)
        {
            C.<>c.<>9__0_0 = new Func<string>(C.<>c.<>9.<M>b__0_0);
        }
    }
} 
```

### 结果

第一个条目是来自 [Schabse Laks](https://gist.github.com/mattwarren/3c7cfaa245effc0a318b87f1ee5dc153#gistcomment-2106237) 的以下条目，它在`M()`方法中包含 9 行代码:

```
using System.Linq;
using Y = System.Collections.Generic.IEnumerable<dynamic>;

public class C {
    public void M() {
((Y)null).Select(async x => await await await await await await await await await await await
await await await await await await await await await await await await await await await await
await await await await await await await await await await await await await await await await
await await await await await await await await await await await await await await await await
await await await await await await await await await await await await await await await await
await await await await await await await await await await await await await await await await
await await await await await await await await await await await await await await await await
await await await await await await await await await await await await await await await await
await await await await await await await await await await await await await await await x.x()());
    }
} 
```

这扩展到令人印象深刻的 7964 行代码(是的，你没看错！！)获得 **885** (7964 / 9)的分数。他发现的主要技巧是在输入中增加更多的线可以增加分数，也就是说，是超线性的。尽管你[做得太过分了](https://twitter.com/Schabse/status/867809080714313729)，编译器还是抛出了一条令人印象深刻的错误消息:

> 错误 CS8078:表达式太长或太复杂，无法编译

以下是 6 大最佳结果:

| 屈服 | 进入 | 得分 |
| --- | --- | --- |
| [沙布斯湖](https://twitter.com/Schabse) | [链接](https://twitter.com/Schabse/status/867808817655840768) | 885 (7964 / 9) |
| [安德烈·迪亚特洛夫](https://twitter.com/a_tessenr) | [链接](https://twitter.com/a_tessenr/status/867776073735454721) | 778 (778 / 1) |
| [阿尔兹](https://twitter.com/alrz_h) | [链接](https://twitter.com/alrz_h/status/867780509627273216) | 755 (755 / 1) |
| 安迪·戈克 * | [链接](https://twitter.com/andygocke/status/867773813907312640) | 633 (633 / 1) |
| 贾里德·帕森斯 * | [链接](https://twitter.com/jaredpar/status/867772979698049024) | 461 (461 / 1) |
| 乔纳森·钱伯斯 | [链接](https://twitter.com/jon_cham/status/867759359803228162) | **384** (384 / 1) |

罗斯林编译器团队的成员(他们没有被取消资格，但也许他们应该有某种障碍来“平衡”比赛场地？)

### 光荣提及

然而，还有一些其他参赛作品虽然没有进入前 6 名，但由于其独创性，仍然值得一提:

*   揭露一个[编译器错误](https://twitter.com/a_tessenr/status/867765123745710080)，向 [@a_tessenr](https://twitter.com/a_tessenr) 致敬
    *   几个小时内完成的 GitHub 错误报告和编译器中的[修复！！](https://github.com/dotnet/roslyn/pull/19784/files)
*   触及了内部编译器限制，T2 @ Schabse 做得很好
*   最[优雅的尝试](https://twitter.com/NickPalladinos/status/867764488958857216)以 [@NickPalladinos](https://twitter.com/NickPalladinos) 的`Y combinator`为特色
*   [利用 VB.NET](https://twitter.com/AdamSpeight2008/status/867800480478515200)(提示:没好下场！！)，但仍然是 [@AdamSpeight2008](https://twitter.com/AdamSpeight2008) 的一次勇敢尝试
*   最赏心悦目的参赛作品 [@leppie](https://twitter.com/leppie)

* * *

C#编译器中的[‘降低’(以及误用它会发生什么)](http://mattwarren.org/2017/01/25/How-do-.NET-delegates-work/)这个帖子最先出现在我的博客【mattwarren.org T2】上