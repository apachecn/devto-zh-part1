# 在领域中存储枚举

> 原文：<https://dev.to/jonstodle/storing-enums-in-realm-49jc>

自从我遇到了[领域](https://realm.io)，我就爱上了它。它速度快，易于使用，设置方便，易于迁移。自从我开始使用它，他们已经添加了奇妙的，与后端自动同步。下一个即将出现的东西是后端云服务，所以你不必自己运行它(耶！).

虽然有很多关于境界的爱，当然也有一些限制。其中一个限制是你只能存储基本类型(比如`int`、`string`、`DateTimeOffset`等等)。这与 Realm 存储和读取数据库数据的方式有关。

你不能储存的类型之一是`enum` s。但是正如我提到的，你可以在领域中储存`int` s，`enum`就像一个巧妙伪装的`int`。

从`0`开始，`enum`的值被自动分配一个`int`值。第一个声明的值被赋值为`0`，第二个被赋值为`1`，第三个被赋值为`2`，依此类推。就像数组中的索引一样。

让我们定义一个`enum` :

```
public enum Color
{
  Blue, Green, Red, Yellow
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你就可以明确地在`enum`和`int`之间进行转换，就像这样:

```
Color color = Color.Blue;
int colorInt = (int)color; // == 0
colorInt = (int)Color.Green; // == 1
color = (Color)1; // == Color.Green 
```

Enter fullscreen mode Exit fullscreen mode

正如我们可以很容易地在`Color`和`int`之间转换一样，我们也可以将`Color`作为一个`int`存储在 Realm 中。

假设我们有一个`Paint`类，其中一个属性是颜料的颜色:

```
public class Paint : RealmObject
{
  // Other code
  public int Color { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是当我们想要使用它的时候，总是要强制转换`int`就有点笨拙了。不过不用担心。我们将把`Color`重命名为`ColorRaw`,以表明它存储的是原始值，而不是想要的值。我们还将添加一个名为`Color`的新属性，它将为我们转换`int`:

```
public class Map : RealmObject
{
  // Other code
  public int ColorRaw { get; set; }

  public Color Color
  {
    get => (Color)ColorRaw;
    set => ColorRaw = (int)value;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

由于我们已经在`Color`上指定了一个定制的 getter 和 setter，Realm 将忽略该属性，并且不将其存储在数据库中。

现在，我们将能够像平常一样使用它了。例如，您将无法将数据绑定到`Color`。您可以让它自己发送`PropertyChanged`通知，或者使用绑定上的转换器在`int`和`Color`之间进行转换。

* * *

希望这个小技巧能帮到你。

编码快乐！