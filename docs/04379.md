# Scala 中泛型类型的 F-Bound

> 原文：<https://dev.to/apium_hub/f-bound-over-generic-type-in-scala-9n4>

在这篇文章中，我将提到 Comonads。如果您知道它们是什么，那很好，如果您不知道，也不用担心，因为本文的主题不是 Comonads。它实际上是关于 Scala 泛型的，关于返回“Scala 中的当前类型”。在我理解 Comonads 的漫长旅程中，我在阅读完它们之后做的第一件事是实现一系列测试，让它们变得更加清晰，我使用了 scalaz 库的非空列表实现。但是显然测试一个特定的实现并不能让我完成它，所以我决定自己实现一个 IdentityComonad，一个没有附加功能的 Comonad。

Scala 泛型类型的 F-bound

最后得到了这样的结果:
case 类 identity commonad[A](https://dev.toa:%20A){
def map[B](https://dev.tof:%20A%20=>%20B):identity commonad[B]= identity commonad(f(A))
def counit:A = A
def duplicate:identity commonad[A]]= identity commonad(this)
def co join = duplicate
def cobind[B](https://dev.tof:%20IdentityComonad%5BA%5D%20=>%20B):identity 接口的第一个版本，设计了我已经定义的所有方法， 是这样的:
trait IComonad[A]{
def map[B](https://dev.tof:%20A%20=>%20B):IComonad[B]
def coun unit:A
def co join:IComonad[IComonad[A]]
def duplicate = co join
def co bind[B](https://dev.tof:%20IComonad%5BA%5D%20=>%20B):IComonad[B]
}
所以 IdentityComonad 演变成这样:
case 类 IdentityComonad
override def cobind[B](https://dev.tof:%20IdentityComonad%5BA%5D%20=>%20B):IdentityComonad[B]= identity comonad(f(this))
}
就在这里，我们发现了第一个问题:到目前为止，我们只在返回类型中发现了 identity comonad(特性中的 IComonad)，但是…
override def cobind[B](https://dev.tof:%20IdentityComonad%5BA%5D%20=>%20B):identity comonad[B]= identity comonad(f(this))
Oh，in 因为 identity commonad[A]不是 IComonad[A]
这是当我们告诉自己，编译或死亡，我们改变 cobind 以使用 IComonad:
case 类 identity commonad[A](https://dev.toa:%20A)扩展 IComonad[A] {
覆盖 def map[B](https://dev.tof:%20A%20=>%20B):identity commonad[B]= identity commonad(f(A))
覆盖 def coun unit:A = A
一切都可以编译，甚至可以工作…酷吧？
问题是如果我们在接口中为 cobind 留下这个方法签名:
override def cobind[B](https://dev.tof:%20IComonad%5BA%5D%20=>%20B):IComonad[B]=？？？我们可以接受从 IComonad 扩展的任何其他余项作为 f 中的参数。

我们需要更改 IComonad，以确保 f 中使用的类型是我们正在实现的子类，而不是泛型 IComonad。
但是这里并没有全部结束 map 方法的返回类型也是 IComonad…哎哟。
这意味着在 identity commonad 中的 map 实现中，我们可以返回 IComonad 的任何其他实现，而不一定是 identity commonad，这将使我们的 Comonad 失去意义。
到了这个阶段，你记得读过一些叫做 F 有界类型的东西，所以我们寻找信息并尝试使用它:
trait IComonad[A，F[A]]{
def map[B](https://dev.tof:%20A%20=>%20B):F[B]
def coun it:A
def co join:F[F[A]]
def duplicate = co join
def co bind:F[B]
}
If
这样，IComonad 使用 A，就像以前一样，但是现在它也使用 F[A]，确定实现中使用的类型。
看起来可能很混乱&凌乱，但总而言之，实现告诉它的接口它是谁，这样接口就可以强制类型。

实现可能是这样的:
case 类 identity commonad[A](https://dev.toa:%20A)扩展 IComonad[A，identity commonad]{
override def map[B](https://dev.tof:%20A%20=>%20B):identity commonad[B]= identity commonad(f(A))
override def counit:A = A
override def co join:identity commonad[A]= identity commonad(this)
override def cobind[？现在，返回类型必须完全是我们正在实现的类的类型(identity commonad)，我们不再有以前的问题(能够使用同级 identity commonad 类型)
但是…你闻到了吗？我也闻到了……我没有被迫告诉 IComonad 这是相同的命令。
我的意思是，identity commonad 可以扩展 IComonad](https://dev.tof:%20IdentityComonad%5BA%5D%20=>%20B) [A，Something](https://dev.tobeing%20Something%20another%20generic%20class%20that%20accepts%20%5BA%5D) ，一切都会解决(使用 Something 作为 F)
这有点难以解释，所以我会画一张图来说明:
Case 类 identity commonad[A](https://dev.toa:%20A)扩展 IComonad[A，Something]{
|
V V
这个类&这个类
它们可能不是看看这个例子，有一个" ListComonad(不是有效的实现):
类 ListComonad [A](https://dev.tolist:%20List%5BA%5D) 扩展 IComonad[A，List] {
覆盖 def map[B](https://dev.tof:%20A%20=>%20B):List[B]= List . map(f)
覆盖 def counit: A = list.head
覆盖 def co join:List[A]]= List(List)
覆盖 def cobind [B](https://dev.tof:%20List%5BA%5D%20=>%20B) 现在我有一张唱片了，不错吧？但是这不是一个有效的列表命令，我没有返回一个命令，所以我也不能链接它们。
此外，ListComonad 参数不是 A 类型，而是 List[A]类型，这是一个问题，因为 ListComonad 不应该依赖于 List(它是一个单子)。
不过没关系，还是保持专注吧，事实是我刚刚演示了我可以忽悠接口使用不该用的类型(或者不想用，全看你怎么看)。因此我们完成了最终的实现，这是我到目前为止最好的实现(没有使用 typeclasses)。
现在我们尝试将 IComonad 中的 F[A]强制为 IComonad 的一个子类型(F[A] < : IComonad[A，F])，这样更有意义。现在，List 将无法取代 F.
然而，它可以由 FakeComonad 或 IdentityComonad 的任何其他兄弟类型(从 IComonad 扩展而来)来完成，但至少我们限制了“搞乱它”的可能性:
object IComonad
{
trait IComonad[A，F[A]B]:F[B]
def counit:A
def co join:F[F[A]]【T48

case 类 identity commonad[A](https://dev.toa:%20A)扩展 IComonad[A，identity commonad]{
override def map[B](https://dev.tof:%20A%20=>%20B):identity commonad[B]= identity commonad(f(A))
override def counit:A = A
override def co join:identity commonad[identity commonad[A]]= identity commonad(This)
override def cobind[B](https://dev.tof:%20IdentityComonad%5BA%5D%20=>%20B):identity commonad
case 类 FakeComonad [A](https://dev.toa:A) 扩展 IComonad[A，identity comonad]{
override def map[B](https://dev.tof:%20A%20=>%20B):identity comonad[B]= identity comonad(F(A))
override def counit:A = A
override def co join:identity comonad[A]]= identity comonad(identity comonad(A))
override 你可以从一开始就做！这是真的，它是 F-bound，但是它有一个特殊的困难:它是 F-bound，有两个类型参数(A 和 F)，这意味着它是 F-Bound 在一个已经是泛型的类型上，这迫使我们使用更高级的类型(F[A])。这就是这个实验的伟大之处，达到了 F-Bound 不起作用的地步，我们必须使用更高级的类型！一旦你到了最后，如果你明白了一切，它并不比正常的复杂多少，但在开始时，它可能会很糟糕。嘿，难道一个类型类不能解决你的生活吗？当然，有了 typeclass，我们就摆脱了这些问题，因为它们有为每个 typeclass 显式定义的类型。但好的是检查我们能走多远，对吗？仅使用这套工具我们能走多远？

对于那些想知道如何用 typeclass 做这种事情的人，这里有一个很好的参考。

总之，通过我们所遵循的思路，我们可以看到对泛型的滥用会导致奇怪的漏洞。这并不意味着你应该停止使用它们！这意味着当创建非常通用的类型时，对于库和其他类型，必须特别小心，以监控可能产生的某些错误。