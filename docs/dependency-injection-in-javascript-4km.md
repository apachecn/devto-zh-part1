# JavaScript 中的依赖注入

> 原文：<https://dev.to/kayis/dependency-injection-in-javascript-4km>

*Flickr 上[paper tmmer](https://www.flickr.com/photos/beffboffmann/)的封面图片*

## 为什么？

我们都编写依赖于其他代码的代码，这是完全正常的。即使我们不使用任何库，我们也会开始以某种方式构建我们的代码库。也许我们把一切都模块化了，现在一个模块依赖于另一个模块，等等。

你可能听说过我们应该*编写松散耦合的代码*,这样我们可以在以后替换我们软件的一部分，但是这实际上意味着什么，我们如何实现这一点呢？

一种方法叫做**依赖注入**或简称 **DI** 。

## 如何？

**DI** 归结为一个想法:去掉代码中的显式依赖，代之以间接依赖，但编码时这意味着什么？

### 什么是显式依赖？

您在代码库中定义了一些或多或少的静态实体。例如*类*或者*功能*。

```
class A {}

function f(x) { return x * x; } 
```

Enter fullscreen mode Exit fullscreen mode

但是，定义一个类或函数并不能使它们显式。它们的使用方式是这里的重要因素。

它们的名称可用于在代码的其他地方引用它们。例如，您可以有一个使用`class A`的`class B`。

```
class B {
  constructor() {
    this.a = new A();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以在一个`function g`中调用`function f`来增加结果。

```
function g() {
  return f() + 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在`function f`和`class A`的用法变得清晰了。现在`class B`只有在定义了`class A`的情况下才起作用，而`function g`只有在定义了`function f`的情况下才起作用。

对于许多开发人员来说，这似乎是一个微不足道的事实，大多数时候它并没有更大的含义，因为类或函数从来不会改变。

但是通常情况下，代码会发生变化，现在必须重写相关的代码。

### 如何摆脱显性依赖？

基本思想是，你不再用显式的名字调用函数或类。在静态类型语言中，这也意味着去掉类型注释，但是因为 JavaScript 是动态类型的，所以我们只需要去掉类名和函数名。

而不是写

```
const a = new A(); 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const result = A.someStaticMethod(); 
```

Enter fullscreen mode Exit fullscreen mode

您保存了对`A`的引用，并将其传递给需要调用它的代码。这允许您在需要时更改对另一个类的引用。

```
 class C {
      constructor(helperClass) {
        this.helper = new helperClass();
      }
    }
    ...
    let someHelperClass = A;
    ...
    if (someCondition) someHelperClass = B;
    ...
    const c = new C(someHelperClass); 
```

Enter fullscreen mode Exit fullscreen mode

函数也是如此。

```
 function h(doSomething) {
      return doSomething() + 10;
    }
    ...
    let doSomething = f;
    ...
    if (someCondition) doSomething = g;
    ...
    const result = h(doSomething); 
```

Enter fullscreen mode Exit fullscreen mode

这种情况可能发生在任何地方。一些 **DI** 框架甚至通过配置文件来配置它们。

你也可以创建你的对象并注入它们而不是对类的引用。

```
 class C {
      constructor(helper) {
        this.helper = helper;
      }
    }
    ...
    let someHelperClass = A;
    ...
    if (someCondition) someHelperClass = B;
    ...
    const c = new C(new someHelperClass()); 
```

Enter fullscreen mode Exit fullscreen mode

### 实际例子

你有一个从服务中获取数据的软件。您有多个类，每个类用于一个服务，但是它们都共享同一个接口。现在，您可以通过命令行参数、配置文件或环境变量创建一个条件来决定使用哪个类。

```
 class ServiceA { getData() {} }
    class ServiceB { getData() {} }
    class ServiceC { getData() {} }

    let Service;
    switch(process.env.APP_SERVICE) {
      case 'serviceB':
        Service = ServiceB;
      break;
      case 'serviceC':
        Service = ServiceC;
      break;
      default:
        Service = ServiceA;
    }
    ...
    class Application {
      constructor(Service) {
        this.service = new Service();
        this.run = this.run.bind(this);
      }
      run() {
        this.service.getData();
      }
    }
    ...
    const myApplication = new Application(Service);
    myApplication.run(); 
```

Enter fullscreen mode Exit fullscreen mode

您有一些 UI 组件，它们呈现嵌套在其中的其他 UI 组件。您可以让他们决定父组件使用什么子组件

```
 const planets = ["mercury", "venus", "earth", "mars"];

    function List(planets) {
      return "<someMarkup>" + planets.map(planet => Item(planet)) + "</someMarkup>";
    }
    ...
    const markup = List(planets); 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以简单地将完成的子进程传递给父进程

```
 function List(children) {
      return "<someMarkup>" + children + "</someMarkup>";
    }
    ...
    const markup(data.map(item => Item(item))) 
```

Enter fullscreen mode Exit fullscreen mode

现在，家长可以使用您给它的任何孩子。

```
 const children = [FirstItem("Planets")]
    data.forEach(planet => children.push(Item(planet)));
    List(children); 
```

Enter fullscreen mode Exit fullscreen mode

正如您在这里看到的，您不必将对类或函数的引用作为引用来获得 **DI** 的好处。你也可以在*将*注入依赖它的目标代码之前创建你的结果或实例。

### 问题

有时候这有点失控。通常 **DI** 用得相当简单。就像我说的，当你在代码中传递对一个类或函数的引用时，它已经是 **DI** 了，但是你可以通过使用外部配置文件来决定使用什么代码，这样用户就有办法修改软件而不用重新编写任何代码。

如果你做得太多，你最终会得到一个大的配置，而且没有人真正知道到底是什么代码在运行。

## 结论

依赖注入是一种结构化代码的方式，因此它变得更加松散耦合。它可以用于应用程序的小部分，也可以管理整个应用程序。

但是和所有东西一样，要适度使用。代码越清晰，就越容易推理。