# 用 Rust 实现 REST 客户端:traits 和泛型介绍

> 原文：<https://dev.to/samipietikainen/implementing-rest-client-with-rust-an-introduction-to-traits-and-generics-4pk9>

在读了 Rust 的书和[写了我最初的经历](https://pagefault.blog/2017/09/14/my-experiences-learning-rust/)之后，我想要一个小的实践项目来让自己熟悉这门语言。所以，我决定写一个 REST 客户端库。这篇文章一步一步地展示了我最初的设计是如何被重构得更加健壮和易用的。特征和泛型是这些重构中的关键概念。目的不是详细介绍库的内部，这里也不介绍特征和泛型的基本语法。锈书[第十章](https://doc.rust-lang.org/book/second-edition/ch10-00-generics.html)是后者的绝佳资源。

## Restson

我实现的库叫做 Restson，它是 JSON 编码的 REST APIs 的客户端库。主要思想是为 API 请求提供易于使用的接口，并自动处理 URL 解析以及 Rust 结构的序列化和反序列化。这个库是使用 Hyper 实现的，Hyper 是 Rust 的去因子 HTTP 库。

Restson 库可以在 GitHub 和 T2 的 crates.io 中找到。

## 设计迭代

当我开始写这个库的时候，我是 Rust 的新手(我仍然是),我以前也从来没有用过 Hyper。所以我没有马上做出优雅的设计。虽然，通常你不会马上得到最终的设计，即使你很熟悉这门语言。至少对我来说，通常需要一些迭代和重构来改进和提炼最初的想法和结构。

让我们看看 GET 接口签名最初是什么样子的:

```
pub fn get(&mut self, url: String) -> Result<String, Error> 
```

Enter fullscreen mode Exit fullscreen mode

因此，该函数将 REST 端点 URL 作为参数，使用 Hyper library 执行 GET 请求，然后以字符串形式返回请求正文。从功能的角度来看，上面的功能没有错，因为它确实完成了工作。然而，这对于图书馆用户来说并不理想，而且这种设计也有许多缺点。

*   每次使用接口时都需要提供完整的 URL:
    *   分散在代码中的 URL 文字
    *   更改基本 URL 需要修改所有 API 调用
*   客户端调用者需要处理 URL 解析(这增加了样板文件并且容易出错)
*   客户端调用者需要在 GET 中手动反序列化返回的 JSON，在 POST 中将数据序列化到 JSON。

让我们看看如何通过改进库接口来解决这些缺点。

### 自动(反)序列化

让我们暂时把 URL 解析放在一边，先看看函数的返回值。对于简单的用例，直接操作 JSON 可能是可以的，但是将它反序列化为 struct 通常更好。这允许编译器进行正确的类型检查，并且减少了对 JSON 格式的依赖。例如，如果一些字段名发生变化，只需要更新(反)序列化代码，而不是所有使用数据的地方。

为了能够返回反序列化的数据，`get`-函数需要知道 JSON 被反序列化到的类型。然而，这些类型是由库用户定义的，不能在库代码中固定。这可以通过将`get`变成一个通用函数来解决:

```
pub fn get<T>(&mut self, url: String) -> Result<T, Error> where
    T: serde::de::DeserializeOwned 
```

Enter fullscreen mode Exit fullscreen mode

引入了泛型类型`T`，函数现在返回`Result<T, Error>`，而不是返回`Result<String, Error>`。现在，该函数可以返回不同的用户定义类型，这些类型是从服务器返回的 JSON 反序列化而来的。在 Rust 中，类型推断也使得泛型使用起来很方便:

```
let data: MyType = client.get("url").unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，编译器能够判断出`get`中的泛型类型`T`必须是`MyType`，因为返回值被赋给了该类型的变量(确切地说是`unwrap`的返回值)。泛型类型也可以被显式注释，但在这里没有必要。上面的调用与下面的示例是等效的。

```
let data = client.get::<MyType>("url").unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

你可能想知道`where T: serde::de::DeserializeOwned`部分是做什么的。通用类型`T`不能是任何类型。`get`-函数只能接受实现反序列化接口的类型。在 Restson 库中，反序列化是使用 Serde 库实现的，所以 type `T`需要实现`DeserializeOwned` trait。这样`get`的实现可以调用该类型的`serde_json::from_str`函数。

`where`子句设置了一个特征界限，确保如果`T`没有实现`DeserializeOwned`，因此没有可用的`serde_json::from_str`函数，构建就不会通过。对于库用户来说，由于 Serde 提供的宏，实现这个特性很容易。

```
#[derive(Deserialize)]
struct MyType {
    field1: String,
    field2: String,
} 
```

Enter fullscreen mode Exit fullscreen mode

通过对返回值使用泛型类型，库现在能够自动将数据反序列化为用户定义的类型。对库做了相对较小的改动，使得界面使用起来更加方便。

### URL 处理

URL 处理的第一个明显改进是在创建客户端实例时给出基本 URL。那么像`get`这样的 API 函数将只把 REST 路径作为一个参数。

这已经很好了，但是仍然容易出错。对于 API 路径，仍然很容易使用错误类型的返回值。这意味着反序列化总是会失败，并且在编译期间不会捕捉到错误。此外，代码中仍然会有分散的路径字符串。

解决这个问题的一种方法是将 API 路径与库使用的类型相关联。也就是说，REST 请求中使用的每种类型都能够返回相应的 API 路径。这是通过为 REST 路径定义特征来实现的。

```
pub trait RestPath {
    fn get_path() -> String;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
pub fn get<T>(&mut self) -> Result<T, Error> where
    T: serde::de::DeserializeOwned + RestPath 
```

Enter fullscreen mode Exit fullscreen mode

现在，用作返回类型`T`的每个类型都必须实现`DeserializedOwned`和`RestPath`特征。这也是由编译器强制执行的，如果特征没有被实现，构建将会失败。URL 参数可以从`get`中完全删除，因为实现可以调用`T::get_path()`来获得正确的路径，并且在客户端被实例化时提供了基本 URL。trait 实现只需要返回正确的路径字符串:

```
impl RestPath for MyType {
    fn get_path() -> String { String::from("apipath") }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在 API 路径只需定义一次，接口甚至可以在不知道实际路径字符串是什么的情况下使用:

```
let data: MyType = client.get().unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

这种设计仍有一个主要弱点。例如，像`api/devices/<ID>/status`这样的路径很难实现，因为它包含一个应该很容易改变的参数。但是，当前的`get_path`不带任何参数。

### 参数化路径

为了能够返回参数化的 API 路径，`get_path`函数应该采用一个用于格式化路径字符串的参数。比如:

```
impl RestPath for MyType {
    fn get_path(id: u32) -> String { format!("api/devices/{}/status", id) }
} 
```

Enter fullscreen mode Exit fullscreen mode

该参数也需要从接口函数如`get`转发。然而，问题是，这是库代码，不知道库用户希望使用什么类型作为路径参数。避免这个问题的一个方法是传递一个字符串向量。虽然这可行，但不是最佳选择。首先，对于库用户来说，这将需要不必要的字符串转换代码，并且使 trait 实现中的错误检查更加困难。更好的选择是让`RestPath`特性通用化。

```
pub trait RestPath<U> {
    fn get_path(par: U) -> Result<String, Error>;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，库用户可以使用任何参数类型来实现特征。返回值也被更改为`Result<String, Error>`，这样 trait 实现可以用提供的参数指示错误。这些改变也需要改变`get`接口来传递参数。

```
pub fn get<U, T>(&mut self, param: U) -> Result<T, Error> where
    T: serde::de::DeserializeOwned + RestPath<U> 
```

Enter fullscreen mode Exit fullscreen mode

另一个通用参数`U`被引入到在特征界限`RestPath<U>`中使用的接口中，也作为`param`被接受。当库实现调用`get_path`函数时，参数被传递给它`T::get_path(params)`。如果没有找到具有正确参数类型的类型`T`的`RestPath`实现，编译器将产生一个错误。界面现在看起来非常复杂，但实际上使用起来非常简单。

首先为用户类型
实现`RestPath`

```
impl RestPath<u32> for MyType {
    fn get_path(id: u32) -> Result<String,Error> { 
        Ok(format!("api/devices/{}/status", id)) 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，简单地将路径参数给接口函数，接口函数将它传递给`get_path`。

```
// calls api/devices/1234/status and deserializes
// returned JSON to data variable
let data: MyType = client.get(1234).unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

对于具有不同泛型参数的同一类型,`RestPath`特征也可以实现多次。还可以通过使用例如元组来提供多个路径参数。

## 结论

这些重构改进了最初的设计，虽然可以工作，但对库用户来说不是很方便。它还容易出现编译时不容易发现的错误。通过使用泛型和 traits 的组合，该接口保持了易用性，但仍有许多通用功能(如序列化)封装在库中。库还允许用户类型，这使得编译器能够进行更多的错误检查(与传递字符串等东西相比)。

总而言之，特征和泛型是强大的工具，尤其是在编写库、实用程序或其他可重用代码时。