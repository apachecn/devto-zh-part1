# Kotlin @喂

> 原文：<https://dev.to/ookbee/kotlin-pronto-152>

又来了，我们去参加 kotlin workshop，来了，Ookbee 是个很大的赞助人，听说他是 gold diamon 级别的，听着像 ookbeet 少了一个心，另一个病了，我们早上有事要赶去 radah 街，转了一条线，转了一个弯，接着又一个弯路，然后 skip .. .我们开始吧。

#### 第一场:来自 Agoda 的空气

我们到了，就快结束了

> 从变量声明开始，**【var/val】t1㎡**

Java Syntax 时间，我们将创建一个变量，保留一个值，它将编写大约这个，是的，
t0㎡。

```
private String name = “Hello World”;

Love people = new Love(); 
```

从现在开始，不要再宣布它很麻烦了，否则我们会告诉类型的，它是 t0㎡t0㎡。

```
var name: String = “Hello World”

var name = “HelloWorld”

var people = Love() 
```

结束，如何声明一个简单的变量，等等。.还有，如果你希望它是 Java 的不可编辑的话，我们会用 final，是的，man 来使它成为可免疫的
t0㎡。

```
final String name = “Hello World”; 
```

我们不能以其他方式解决 name . kotlin 使用的是 t0㎡t

```
val name : String = “Hello World”; 
```

> lateinit 完全匹配后插入

TestNaja 类{

var 人员:人员

}

当然，Kotlin 的优点是，我们会用更少的 Null PointerException 来进行权衡。

用 lateinit〔t0〕求解

```
class TestNaja{

lateinit var person : Person

@SetUp fun setup(){

person = Person()

}

} 
```

在这方面，我们不知道为什么 Setup 在我们的 ide 中找不到页面。

> 懒惰不容易

还不明白多少，但多少读的意思是，听的话，如果还没有运行，就不会运行。他举了这样的例子。
t0㎡。

```
class Test{

val database by lazy { Database()

}

fun saveToDatabase(name: Person) { database.save(name)

……

}

} 
```

is database 在调用 saveToDatabase 之前不会被刷新

> 可扩展的 null 类型无法跳过

就像我告诉 Kotlin 的那样，总是提醒我们 null，但是如果我们真的想和 null 一起玩，我们可以把标记放在哪里？也称为 Elvis 标记，它可以给你这个变量，比如，

。

```
var x: String = “Hello” 
```

x = null < compiler จะไม่ยอมให้ค่านี้เป็น null เด็ดขาด เราเลยต้องแก้ใส่ ? ให้มัน

```
var x: String? = “Hello” 
```

#### 第二场:Yesmom 的露珠

把 java 的代码转换成 Kotlin 的感觉很喜欢解释 dusoff

从一开始，他就让克隆项目下来了，在这个项目中，它分为两个主要的活动，一个列表页的数据，另一个是那个数据的 Detail。

后来，它教你如何从 java 到 Kotlin 转换类。后来它把 Kotline REPL 描述为 short cut，写了一个很短的测试程序。真的很好，不用去构建。

浮点函数不需要等待任何人。Kotlin 的另一个优点是我们可以在类之外创建函数，它是一个浮点函数！我的天啊！

然后继续教 Apply 和 Lambda

Apply function 用来编辑重复的对象名，举个例子来看。

如果我们要写『T0』。

```
TextView textView = new TextView(context);

textView.setText(“Hello”);

textView.setColor(Color.RED);

textView.setTextSize(18); 
```

写什么？拉长？什么？拿着这个吧，我们来做个 Kotlin 的解决方案，最好是通过
来解决。

```
var textView = TextView(context)

textView.apply{

setText(“Hello”)

setColor(Color.RED)

setTextSize(18)

} 
```

你能告诉我这个项目吗？apply 可以在{ }博客上找到 textView .. .让我们的模型看不到它，或者也称为内块，它将到达所有的这个对象。

后来，Lambda 长得像这样->就像 Java 8 Dell 一样，它可以创建一个 anonymus 函数。

#### 第三场:POP 来自 Shopspot，Ju 来自 Agoda

他说得太快了，加上我们找不到克隆人

就博客而言，它真正指的是 apply 短变量，它还会有三种不同的类型。

也适用于

打电话的方式有点不同

Also 使用 it 来表示对象，如
等。

```
var textView : TextView(context)

textView.also{

it.setText(“Hello”)

} 
```

向它投一个投一个投一个投一个给它
。

```
with(textView){

it.setText(“Hello”)

} 
```

后来他给我们做了他创造的解决方案我们没有代码，只能一个人坐在那里看

#### 第四场:Genxas 的 Christopher Ng

说到 Kotlin，它可以转换成多种语言，然后把代码放到 IOS 上，让我们看看！我的天啊！我的天啊！

事实上，Kotlin 的 Syntax 非常接近 IOS。我们将来可能会写 Kotlin 运行它。2 平台。但从他看到的，我们可能会把 lolo

后来他在 java script 上给 Kolin 讲了一个故事

在我们个人看来，如果做了一个 vena 的岸线应该是 microsoft 的 C#那么我们就知道了，但最喜欢的是 IOS，这就是 5555。

记住这点，不过看着未来，kotlin 很可能会取代 android 的 java，做好准备