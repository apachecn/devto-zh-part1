# Kotlin 中的数据类

> 原文：<https://dev.to/grahamcox82/data-classes-in-kotlin>

(今天忙疯了一天，所以简单的文章来满足我的配额。抱歉)

我之前提到过，与 Java 相比，Kotlin 得到了显著的简化。它实现这一点的最明显的地方之一，你可能很早就看到，是在数据类中。

数据类只是类。从这一点来看，他们没有什么特别的。实际的“data”关键字最初只不过是编译器看到并做出反应的注释。(老实说，我不确定我担心的情况是否仍然如此)。因此，他们可以做(几乎)任何正常班级可以做的事情。

数据类的意义在于——信不信由你——它们代表数据，而不是功能。这是来自 Java-land 的 Java Bean 的定义。这就是他们的真实身份。只有明显更多的流线。当您在类定义前面添加“data”关键字(或注释，或其他任何东西)时，您告诉编译器要做的是:

*   生成一个类
*   有了这组字段
*   每个字段都有 getters(和可选的 setters)
*   用正确的`equals`和`hashCode`方法
*   用正确的`toString`方法
*   用一种`copy`方法
*   用一套`componentN`方法

前三个只是 Kotlin 定义类的一部分。那没什么特别的。另外 4 个是数据类真正发挥作用的地方。

编写`equals`、`hashCode`和`toString`方法并不难。写对了*就可以了。但即使这样，大多数时候人们要么让他们的 IDE 自动生成它们，要么使用类似 Commons-Lang Equals/HashCode/tostring builder 类的东西。这很好，但不是没有代价的。一旦类发生变化，从 IDE 中生成的文件就会过时，除非您记得更新它们。生成器类要么会遇到同样的问题，要么会使用反射，从而导致性能损失。*

 *最后一组方法只是一些辅助方法。`copy`方法允许您获取一个对象，并用相同的值创建一个新的对象。或者与*有些*相同的价值观。您可以有选择地挑选想要用新值替换的值。例如:

```
val user = User(username = "graham", email = "my.email@example.com", banned = false)
val copiedUser = user.copy(email = "my.real.email@example.com) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，copiedUser 具有:

*   用户名= "格雷厄姆"
*   email = "[my.real.email@example.com](mailto:my.real.email@example.com)
*   禁止=假

您只需指定其中一个值。

`componentN`方法由析构使用，这本身就是一个主题。简而言之，它允许您获取一个类，并将其分解为单独的字段。这在一些地方非常有用——比如模式匹配——当你不关心类本身而只关心单个字段时。

简而言之，数据类没什么特别的。他们实际上不做任何事情。它们只是让你不必为本质上是生成的方法写很多样板文件。您可以编写一个包含许多字段的 Java Bean，并在一行代码中获得您需要的所有正确的 JavaBean 方法，以及更多的方法。借用 Reddit 的一个例子，它是这样的:

```
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public String component1() {
        return name;
    }

    public int component2() {
        return age;
    }

    public User copy() {
        return new User(name, age);
    }

    public User copy(String newName) {
        return new User(newName, age);
    }

    public User copy(String newName, int newAge) {
        return new User(newName, newAge);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        User user = (User) o;

        if (age != user.age) return false;
        return name != null ? name.equals(user.name) : user.name == null;
    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        return result;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

成这样:

```
data class User(val name: String, val age: Int) 
```

Enter fullscreen mode Exit fullscreen mode*