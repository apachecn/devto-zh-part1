# 鲁棒性原则

> 原文：<https://dev.to/napicella/robustness-principle-with-jackson-3h5>

> 发送的东西要保守，接受的东西要开明

这个明智的建议，也被称为健壮性原则或 Postel 定律，在应用程序之间发送消息的所有用例中，都非常有用。这些消息通常有一个通过 HTTP 发送的 Json 负载。
典型的场景包括:

1.  客户机在 Json 中序列化模型，并通过 HTTP 将其发送到服务器。
2.  另一方面，服务器获取消息，提取请求体，即我们的 Json，将其反序列化回一个模型(可以不同于客户端的模型)，然后用它执行操作。

这里没什么新东西。即使在这个简单而常见的场景中，也必须做出一个非常重要的决定:数据的格式。在这种情况下，消息的 json 结构。
让我们假设，我们正在设计一个对用户执行操作的 rest 端点。根据我们的需求，为了给我们的用户建模，我们只需要名字和姓氏。
我们用户的 json 可能是这样的:

```
{  "firstname"  :  "Diego",  "lastname"  :  "Maradona"} 
```

Enter fullscreen mode Exit fullscreen mode

我们基本上在系统的两部分(客户机和服务器)之间定义了一个隐式契约。为了让他们作为一个整体工作，他们需要在数据格式上达成一致。尽管我们的决定看起来合理，但我们违反了另一条重要原则:

> “增加未做出决策的数量”

通过定义消息的结构，我们决定由客户机发送到服务器的 json 有两个字段(“名字”和“姓氏”)，并且它只能包含这两个字段。这正是句子的第二部分，听起来像是一个不必要的决定。事实上，我们正在无缘无故地让我们的系统变得难以改变。有几个与之相关的缺点:

1.  如果将来我们必须为我们的用户添加一个新的字段，那只能通过同时更改客户端和服务器并一起部署它们来完成。
2.  客户端或服务器可能会更改自己的模型，这将导致一个只有在运行时才能检测到的错误(除非我们做额外的工作来测试我们的契约)

## 图式进化

这个问题很常见，属于模式进化的总称，定义也包括数据库模式进化之类的东西。因此，有一些库可以帮助我们做到这一点并不奇怪(例如 Protocol buffer 或 Avro)。
尽管我对 ProtoBuf 进行了一些试验，但我很少需要使用它(主要是因为它包含了我实际需要的一系列功能)。事实上，我工作过的大多数 java 应用程序都依赖 Jackson 或 Gson。事实证明，这两者都可以被调整为宽容的读者/作者，并支持模式进化。
如果你是一名 Java 程序员，你可能熟悉 Jackson 或 Gson，这是两个流行的将 Java POJO 与 Json 相互转换的库。如果你不是，你可能想看看这个[教程](http://www.baeldung.com/jackson-annotations)。无论哪种方式，遵循这些例子应该很容易。

## 举例

下面的例子展示了如何调整 Jackson 以适应模式变化。这些例子是用 Java 写的，你可以在[github.com/napicella/java-jackson-tolerant-reader](https://github.com/napicella/java-jackson-tolerant-reader)找到文章中用到的所有代码。

这是我们的 rest api 的用户模型的样子:

```
public class User {
    private String name = "";
    private String surname = "";

    public User() {
    }

    public User(String name, String surname) {
        this.name = name;
        this.surname = surname;
    }
    // setters and getters … 
} 
```

Enter fullscreen mode Exit fullscreen mode

服务器获取 json 并将其反序列化为用户类型。

```
public void handle(Request request) throws IOException {
  ObjectMapper mapper = new ObjectMapper();  
  User user = mapper.readValue(request.body(), User.class);
   // do something with User
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，Jackson 对象映射器将 request.body 中的字符串映射到 User 类。如果 json 与类不匹配，它会抛出一个异常。
例如，假设客户端发送给我们以下 json:

```
{  "firstname"  :  "Diego",  "lastname"  :  "Maradona",  "middlename":  "Armando"  } 
```

Enter fullscreen mode Exit fullscreen mode

Jackson 将抛出一个异常，因为属性“middlename”没有在 User 类中定义。

## 一个宽容的读者- 1

我们能做些什么呢？让我们通过定义 json 必须至少包含“名字”和“姓氏”来尝试增加没有做出的决定的数量。Jackson 允许用两种方式定义它:编程和用 Java 注释。不失一般性，我们将用注解来做这件事:

```
@JsonIgnoreProperties(ignoreUnknown = true)
public class User {
//… 
```

Enter fullscreen mode Exit fullscreen mode

`@JsonIgnoreProperties(ignoreUnknown = true)`告诉 Jackson 无一例外地忽略 JSON 输入中的任何未知属性。

```
 @Test
    public void test() throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        User user =
                mapper.readValue("{ \"name\" : \"Diego\", \"surname\" : \"Maradona\", \"middlename\" : \"Armando\"}",
                        User.class);

        assertThat("TOLERANT-READER. The json might contain property that are not defined in the pojo. " +
                        "Ignore them!" +
                        "How: use @JsonIgnoreProperties(ignoreUnknown = true) annotation on the POJO",
                user.getName(), is("Diego"));
    } 
```

Enter fullscreen mode Exit fullscreen mode

这样，服务器就不会再抱怨用户类中没有定义的字段了。

## 一个宽容的读者- 2

在许多情况下，服务器可以通过为属性分配默认值来处理属性缺失的情况。对于这个例子，假设服务器可以接受一个空用户，在这种情况下，它将为名字和姓氏分配默认值。
这需要设置两件事:
1 -在模型中设置名和姓的默认值

```
@JsonIgnoreProperties(ignoreUnknown = true)
public class User {
    private String name = "Dries";
    private String surname = "Mertens";
    // rest of the class as before 
```

Enter fullscreen mode Exit fullscreen mode

2 -告诉 Jackson 不要使用`@JsonInclude(JsonInclude.Include.NON_NULL)`注释来序列化空值。

```
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonIgnoreProperties(ignoreUnknown = true)
public class User {
    private String name = "Dries";
    private String surname = "Mertens";
    // rest of the class as before 
```

Enter fullscreen mode Exit fullscreen mode

`JsonInclude.Include.NON_NULL`保证 null 值不会被序列化，所以服务器收到的 json 根本不会显示这些属性。
设置默认值会导致 Jackson 在 Json 中缺少属性时使用该值。
结果，下面的测试是绿色的:

```
 @Test
    public void test2() throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        User user = new User("Michael", null);
        String json = mapper.writeValueAsString(user);

        User deserializedUser = mapper.readValue(json, User.class);

        assertThat("TOLERANT-WRITER. Don't serialize null values. A tolerant reader prefers no value at all, " +
                        "because in that case can provide a default." +
                        "If you serialize null, there is no way for the reader to understand that actually the property" +
                        "is missing." +
                        "How: use @JsonInclude(JsonInclude.Include.NON_NULL) annotation on the POJO",
                deserializedUser.getSurname(), is("Mertens"));

        assertThat(deserializedUser.getName(), is("Michael"));
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在 rest apis 的世界里，关于模式进化，肯定还有很多要说的。同样有趣的是我们可以用来测试这种契约的方法，但这是另一个时间的讨论。当然，我们非常感谢您的任何反馈！