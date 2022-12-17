# 雷诺

> 原文：<https://dev.to/horia141/raynor-5500>

不久前，我编写并开源了一个名为 [Raynor](https://github.com/horia141/raynor) 的小型 TypeScript/JavaScript 数据验证和编组库。由于它被证明在家里使用非常有用，我想我可以用它做更多的事情，而不仅仅是把它放在 GitHub 上。这篇文章是朝着这个方向迈出的第一步。这是一个概述和教程，雷诺是什么，它如何运作。如果你觉得这类东西有趣，我希望这将为你提供足够的信息，以便你能够在我们自己的项目中尝试它。

一个代码示例值`1000`个字节，所以在我说更多之前，这里有一个:

```
class User {
    @MarshalWith(StringMarshaller)
    name: string;
    @MarshalWith(ArrayOf(NumberMarshaller))
    scoresByDay: number[];

    totalScore(): number {
        return this.scoresByDay.reduce((a,b) => a + b, 0);
    }
}

const um = new (MarshalFrom(User))();
const u = um.extract(JSON.parse('{"name": "Raynor", "scoresByDay": [10, 20, 30]}'));
console.log(u.totalScore()); // Prints 60 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，核心思想是将一个基本的 JS 对象转换成一个更复杂的对象，并检查某些约束是否适用。实际的输入通常是某种反序列化过程的结果。在这种情况和许多其他情况下，这将是 JSON。但是表单编码、URL 编码、一些自定义二进制格式等。都可以用。因为在格式和实现上有如此多的变化，所以 Raynor 目前没有处理这方面的编组的目标。

本文的其余部分分为两部分。首先是实际教程。第二个作为建立它的基本原理和与其他类似系统的比较。

## 教程

雷诺在 NPM 的名字是[雷诺](https://www.npmjs.com/package/raynor)。通过`npm install --save raynor`安装就够了。因为它是用 TypeScript 编写的，所以它已经包含了它的`.d.ts`文件，所以不需要您做额外的事情。

雷诺自己处理一种类型的对象——编组器*。它是通过`extract`方法进行引言中提到的转换和检查的对象。然而，它可以双向作用。因此，在我们的例子中，`um`也可以通过`pack`方法将`u`转换回一个常规的 JS 对象。所有的编组器都是从`Marshaller<T>`接口派生出来的，这个接口挺小的，看起来是这样的:* 

```
interface Marshaller<T> {
    extract(raw: any): T;
    pack(cooked: T): any;
} 
```

Enter fullscreen mode Exit fullscreen mode

模板类型`T`表示编组器产生的类。`extract`方法通常做繁重的工作。输入是`any`，通常是`null`，一个布尔值，一个数字，一个字符串，一个数组或者一个对象。然而，必须检查这一事实，因为如果一个方法需要一个数组，那么当遇到一个数字时，它应该会优雅地失败。当遇到阻止`raw`转换的情况时，应该抛出`ExtractError`。最后，`pack`在功能上与`extract`相反。就代码而言，它更像是一个比`extract`简单得多的方法，因为许多验证步骤是不需要的。

作为一个例子，假设我们在应用程序中已经将布尔值编码为`'TRUE'`和`'FALSE'`，我们希望有一个编组器可以将它们转换成适当的布尔值。这是一个相当做作的例子，但是它将有助于突出从头开始编写封送拆收器的大多数问题。它可能看起来像这样:

```
class CapitalBooleanMarshaller implements Marshaller<boolean> {
    extract(raw: any): boolean {
        if (typeof raw !== 'string') {
            throw new ExtractError('Expected a string');
        }

        const rawRaw = raw.toUpperCase();

        if (rawRaw == 'TRUE') {
            return true;
        } else if (rawRaw == 'FALSE') {
            return false;
        } else {
            throw new ExtractError('Expected either TRUE or FALSE');
        }
    }

    pack(cooked: boolean): any {
        return cooked ? 'TRUE' : 'FALSE';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`extract`的输入是`any`，所以首先要做的是检查它实际上是一个字符串。因此，如果有人要求`CapitalBooleanMarshaller`提取一个字符串数组，它会失败。第二步，基本上是把`"TRUE"`变成`true`，把`"FALSE"`变成`false`。如果输入不是这些，就会引发另一个异常。如上所述，`pack`比`extract`简单多了。这通常是因为，从直觉上讲，应用程序中的数据已经是格式良好的了。只有应用程序之外的数据会带来问题。

# 内置编组器

很自然，Raynor 为简单类型的 JavaScript 提供了一堆编组器。像数字、布尔值、字符串、空值等。从一开始就被处理，所以您可以开始构建它们。还有基于这些类型的非常常见的对象的封送拆收器。例如，`IntegerMarshaller`扩展了`NumberMarshaller`，并检查输入是否为整数。`PositiveIntegerMarshaller`更进一步，检查整数是否为正数。`IdMarshaller`是这个的别名。`WebUriMarshaller`检查字符串输入是否为 URI 格式，协议是否为`http`或`https`。

例如:

```
const uriMarshaller = new WebUriMarshaller();
console.log(uriMarshaller.extract('http://example.com')); // Prints http://example.com
try {
    uriMarshaller.extract('ftp://example.com');
} catch (e) {
    console.log(e.name); // Prints 'ExtractError'
} 
```

Enter fullscreen mode Exit fullscreen mode

顺便提一下，我的计划是将这些封送拆收器从 Raynor proper 转移到第二个库中，该库中将包含常见的业务类型对象的类型定义和封送拆收器:IBANs、货币、地理位置、URIs、电子邮件等。

我们将在本文后面更详细地讨论这些是如何实现的。但是构建封送拆收器的一个非常常见的方法是从一个已经存在的封送拆收器开始，它表达了对输入的某种约束，然后向它添加另一个约束。例如，如果您的应用程序的域是`my-app.com`，并且您想要检查您使用的特定资源是否有来自`my-app.com`的 URI，您可以从`SecureWebUriMarshaller`开始，并像这样扩展它:

```
class MyAppUriMarshaller extends SecureWebUriMarshaller {
    filter(uri: string): string {
        if (!uri.startsWith('https://my-app.com/')) {
            throw new ExtractError('Expected my-app URI');
        }

        return uri;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以再一次扩展`MyAppUriMarshaller`，也许只允许图片，就像这样:

```
class MyAppImageUriMarshaller extends MyAppUriMarshaller {
    filter(uri: string): string {
        if (!uri.endsWith('.jpg')) {
            throw new ExtractError('Expected an image URI');
        }

        return uri;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

应用`filter`方法的顺序是从基类到派生类，或者从更一般的到更具体的。因此，例如，您可以安全地假设到达`MyAppImageUriMarshaller`的`uri`从`https://my-app.com/`开始。

# 为类构建编组器

这些封送拆收器及其扩展涵盖了大量用例。然而，当输入从简单类型变成对象、数组或它们的复杂集合时，为它们编写封送拆收器就变得单调乏味，甚至完全困难了。幸运的是，Raynor 提供了一组丰富的工具来处理这些情况，包括一组用于类定义的注释，以及将它们自动转换为编组器所需的支持基础设施。这样构建的封送拆收器实现了非常常见的模式，即将复杂的对象递归地分解成更小更简单的对象，由其他类型的封送拆收器处理。

本文中的第一个例子使用了这些注释。我们现在将关注一个不同的、更大的例子，以便更深入地了解 Raynor 提出的功能。

要建模的一个经典的东西是数学点。首先，它可能看起来像这样:

```
class Point {
    @MarshalWith(NumberMarshaller)
    x: number;
    @MarhsalWith(NumberMarshaller)
    y: number;

    constructor(x: number, y: number) {
        this.x = x;
    this.y = y;
    }

    getNorm(): number {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该类有两个公共属性`x`和`y`，一个两个参数的构造函数，以及一个实例函数`getNorm`，它返回数学范数\sqrt{x^2 + y^2}.引人注目的地方是应用于公共属性的`MarshalWith`注释。你可能已经猜到了，这通知雷诺在试图构造一个`Point`时使用哪个编组器。可以指定任何类型的封送拆收器，但实际参数必须是构造函数/类。

为了创建封送拆收器，使用了`MarshalFrom`函数，它将一个带注释的类转换成一个封送拆收器类。比如:

```
const pm = new (MarshalFrom(Point))();
const p = pm.extract(JSON.parse('{"x": 10, y: "20"}')
console.log(pm);
// Prints Point { x: 10, y: 20}
console.log(pm.getNorm());
// Prints 22.36 
```

Enter fullscreen mode Exit fullscreen mode

注意该功能在`new`中的使用方式。它返回的实际上是一个构造函数/类，所以在完成任何工作之前，您需要获得一个实例。我们这样做而不是直接返回实例的原因是为了与`MarshalWith`注释兼容。用我们编写类型的方式编写封送拆收器是非常容易的。例如，一个假想的矩形看起来像这样:

```
class Rectangle {
    @MarshalWith(MarshalFrom(Point))
    topLeft: Point;
    @MarshalWith(NumberMarshaller)
    width: number;
    @MarhhalWith(NumberMarshaller)
    height: number;
} 
```

Enter fullscreen mode Exit fullscreen mode

您大概可以凭直觉知道生成的封送拆收器是做什么的。但是在广义上，它首先检查它的参数是否是一个对象。然后，对于每个附加了编组器`Mf`的带注释的字段`f`，它尝试在输入对象中查找属性`f`的值，一旦找到，就使用`Mf`提取该值。然后，这被写入输出对象的属性`f`。如果遇到任何错误，比如输入对象没有必填字段，或者子编组器失败，整个过程将停止，并引发一个`ExtractError`。

从`extract`获得的实例属于正确的类。在前面的例子中，你可以在它上面调用`getNorm()`,它就会工作。对于带注释的类应该是什么样子，没有太多的限制。你可以有一个常规的构造函数，方法和未注释的属性。唯一的硬性约束是，不带参数调用构造函数不会使对象处于非常糟糕的状态，或者以某种方式失败。所以像上面的`Point`这样的构造函数是可以的，但是如果`x`未定义就会通过异常来抱怨的构造函数是不可以的。最好还是把这样的对象看作 dto，但是现在你可以给它们添加一点逻辑了。

# 更多来自注解工具包

`MarshalWith`还接受第二个参数，即原始对象中的字段名称。当处理可能不符合项目风格的外部来源的对象时，这很有帮助。我们的点类可能是这样的:

```
class Point {
    @MarhsalWith(NumberMarshaller, '__x') //__x gets mapped to x
    x: number;
    @MarshalWith(NumberMarshaller, '__y') //__y gets mapped to y
    y: number;
    // All the other stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

一个非常常见的模式，特别是对于已经使用了很长一段时间的 API，可以看到添加和删除的特性，就是对象在对象中有可选的字段。在注释系统的上下文中，Raynor 提供了`OptionalOf`函数来将常规编组器转换为可选的编组器。将点扩展到三维空间为此提供了一个极好的例子:

```
class Point {
    @MarshalWith(NumberMarshaller)
    x: number;
    @MarhsalWith(NumberMarshaller)
    y: number;
    @MarshalWith(OptionalOf(NumberMarshaller))
    z: number|null;
}

const pm = new (MarshalFrom(Point))();
const p1 = pm.extract({x: 10, y: 20});
console.log(p1);
// Prints Point {x: 10, y: 20, z: null}
const p2 = pm.extract({x: 10, y: 20, z: 30};
console.log(p2);
// Prints Point {x: 10, y: 20, z: 30} 
```

Enter fullscreen mode Exit fullscreen mode

雷诺附带了一堆其他的实用编组器，比如`ArrayOf`、`MapOf`、`OneOf2`、`OneOf3`、`MarshalEnum`等。它们一起提供了一种丰富的语言来描述对象的结构和约束。作为一个更复杂的例子，考虑:

```
class Mesh {
    @MarshalWith(ArrayOf(MarshalFrom(Point)))
    points: Point[];
    // A map of string->string
    @MarshalWith(MapOf(StringMarshaller))
    metadata: MarshalMap<string>;
    @MarshalWith(OneOf2(StringMarshaller, NumberMarshaller))
    id: string|number;
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在不久的将来，`MapOf`将升级为与 ES6 [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 协同工作。将有一个类似的`SetOf`将与 [`Set`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 一起工作。当发射到 ES5 时，对这些的类型脚本支持有一些问题，我需要先解决一些问题，但希望不会太久。

作为行为的最后一点，由`MarshalFrom`产生的编组器忽略了额外的字段。它不会将它们包含在输出对象中，尽管根据 TypeScript，它们仍然会匹配所需的类型，但是当遇到它们时，它也不会引发错误。这通常有助于您自己的应用程序中的数据迁移和服务发展。它在编组来自外部 API 的响应时也很有帮助，因为只有您感兴趣的响应部分需要建模，而其余部分可以忽略。

# 扩展注释编组器

到目前为止，所提供的工具已经很好地对验证设置进行了建模，如果一个对象的所有字段都有效，那么这个对象就是有效的。同样，与简单的编组器一样，这捕获了大量的用例。幸运的是，为了捕捉更复杂的情况，没有必要向 Raynor 引入第三个组件，而是在我们已经拥有的组件的基础上构建。我们只需要扩展一个通过注释机制获得的编组器，并提供一个描述我们想要做的额外验证的`filter`函数。

回到我们的二维点的例子，假设我们想要约束我们的点在单位圆上。我们可以通过
实现这一点

```
class UnitCirclePointMarshaller extends MarshalFrom(Point) {
    filter(p: Point): Point {
        if (p.getNorm() != 1) {
           throw new ExtractError('Expected a point on the unit circle');
        }

        return p;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当生成一个`.d.ts`文件供其他包使用时，TypeScript 不能很好地处理扩展类表达式的情况。所以，如果你想从一个包中暴露出`UnitCirclePointMarshaller`，你必须妥协，像这样编码:

```
export class UnitCirclePointMarshaller extends Marshaller<Point> {
    private static readonly _basicMarshaller = new (MarshalFrom(Point))();

    extract(raw: any): Point {
        return this.filter(UnitCirclePointMarshaller._basicMarshaller.extract(raw));
    }

    pack(p: Point): any {
        return UnitCirclePointMarshaller._basicMarshaller.pack(p);
    }

    filter(p: Point): Point {
        if (p.getNorm() != 1) {
           throw new ExtractError('Expected a point on the unit circle');
        }

        return p;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 高级:RaiseBuildFilter 编组器

你在这里看到的几乎所有元帅都是`RaiseBuildFilterMarshaller<A, B>`的后代。事实上，只有一些不同的基本封送拆收器，比如用于布尔值和`null`的封送拆收器，而用于注释的封送拆收器则不是。除此之外，这个封送拆收器包含通过具有`filter`功能的封送拆收器的层次结构进行过滤的逻辑。

简称为`RaiseBuildFilterMarshaller<A, B>`或`RBFM<A, B>`，在处理反序列化时捕获了一个非常常见的模式。它将`extract`分解为三个阶段:筹集，建设和一个可选的过滤器。相反，它将`pack`分解为两个阶段:未构建和降低。Lower 是 raise 的反义词，unbuild 是 build 的反义词。Extract 接受它的输入，并通过 raise 操作传递它。这个结果然后通过 build 发送。最后，按照从最一般到最具体的顺序应用所有已定义的过滤器。Pack 自然会做相反的事情。它不做任何过滤，而是直接将输入传递给 unbuild，并将结果传递给 lower。

第一阶段 raise 对输入进行非常基本的检查。只要知道`any`输入实际上是一个`number`，而不是一个数组或空值。它的结果通常是一个 JavaScript 原语或对象。类型参数`A`是`raise`方法的输出。第二个阶段，构建，将这个结果转化为更结构化的东西。在这里，`number`可能会变成`Date`或者`string`变成`EmailAddress`。类型参数`B`是`build`方法的输出。`build`的结果是某种结构合理的东西。最后一个阶段——过滤器——由一系列方法组成，通常检查不同的业务类型逻辑约束。例如，`Date`是今年的或者`EmailAddress`有特定的格式。

它本身是一个抽象类，它被用作编组器层次结构的基础，其中各种编组器实现不同的阶段。例如，`RBFM`的直接后代可能通过实现`raise`和`lower`方法来实现 raise 和 lower 阶段。它的派生将通过实现`build`和`unbuild`方法来实现构建和非构建阶段。此时，后一个封送拆收器可以按原样使用，因为没有抽象方法需要实现。但是一个完整的编组器层次可以建立在上面，每个编组器只指定一个`filter`函数，并逐渐定义输入的不同约束。

作为一个更具体的例子，考虑内置的`PositiveIntegerMarshaller`。它被定义为:

```
export class PositiveIntegerMarshaller extends IntegerMarshaller {
    filter(b: number): number {
    if (b <= 0) {
        throw new ExtractError('Expected a positive integer');
    }

        return b;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它只定义了一个`filter`函数，用于检查输入是否为正。`IntegerMarshaller`反过来定义为:

```
export class IntegerMarshaller extends NumberMarshaller {
    filter(b: number): number {
        if (Number.isInteger(b)) {
            throw new ExtractError('Expected an integer');
        }

        return b;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它再次定义了一个`filter`函数，通过`Number.isInteger`检查输入是否为正。`NumberMarshaller`是事情变得更有趣的地方。看起来是:

```
export class NumberMarshaller extends BaseNumberMarshaller<number> {
    build(a: number): number {
    return a;
    }

    unbuild(b: number): number {
    return b;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以它用`B = number`实现了`RBFM<A,B>`的构建阶段。相位本身非常简单，是两个方向上的恒等函数。最后，`BaseNumberMarshaller<number>`长得像:

```
export abstract class BaseNumberMarshaller<T> extends RaiseBuildFilterMarshaller<number, T> {
    raise(raw: any): number {
    if (typeof raw !== 'number') {
        throw new ExtractError('Expected a number');
    }

        // isNaN exists in modern browsers.
    if ((Number as any).isNaN(raw) || raw == Number.POSITIVE_INFINITY || raw == Number.NEGATIVE_INFINITY) {
        throw new ExtractError('Expected a number');
    }

    return raw;
    }

    lower(a: number): any {
    return a;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

用`A = number`实现`RBFM<A,B>`的上升阶段。阶段更加复杂，检查输入是不是一个数字，以及它是不是一个奇怪的值。

在 Raynor 的内置中有许多这样的层次结构。`DateMarshaller`是一个类似的例子，它也使用了`BaseNumberMarshaller`,但是这次使用了`B = Date`,因为它想要将一个数字时间戳转换成一个`Date`对象。

## 背景、灵感等。

雷诺绝不是原创作品。它应该感谢[协议缓冲区](https://developers.google.com/protocol-buffers/)、[节俭](http://thrift.apache.org/)、 [Avro](https://avro.apache.org/) 、 [Cap'n Proto](https://capnproto.org/) 、 [JSON 模式](http://json-schema.org/)、[Json.NET](http://www.newtonsoft.com/json)以及许多其他类似的技术，因为人们处理这些东西已经有一段时间了。

它确实改进了我个人的一个痛点，即更多地涉及对实际输入的检查。我认为，在设计互联网应用程序时，我们应该真正彻底地了解如何对数据建模，以及如何检查系统的输入和输出。仅仅从结构上来看很多时候是不够的。不幸的是，上面的大多数选项都是这样做的。JSON Schema 走得更远，因为它对特定字段有一组内置约束，但是它走得还不够远。所以我想要一些能够表达关于实体的更复杂断言的东西。这自然涉及到用编程语言来表达它们，而不仅仅是声明性的。它还将最初的解决方案限制为绑定到一种编程语言，而不是它自己的独立 IDL。选择 JavaScript/TypeScript 是很自然的，因为它在客户端和服务器上都可以找到，并且非常受欢迎。

第二个改进是 Raynor 生成的实体是常规 JavaScript 类的实例。它们不是由某个工具自动生成的，也不会受到太多的限制。你可以在它们上面有额外的方法，额外的字段，一个有趣的构造函数，你甚至可以把它们构建到类的层次结构中，或者用它们做更复杂的事情。

雷诺的一个非目标是提供一种“有线格式”。像协议缓冲区、节俭等工具。也定义了它们自己的，它们规定了如何将一个对象转换成一个适合于在生成过程的内存之外传输的表示。雷诺没有这样做，而是依靠外部机制。大多数时候这意味着依赖 JSON 和`JSON.parse` / `JSON.stringify`进行序列化和反序列化。然而，这为使用不同的机制打开了可能性，扩展了它的使用范围。URL 编码或表单编码是两个例子，但也有其他解析 JSON 的方法，如`fetch`[`response.json`](https://developer.mozilla.org/en-US/docs/Web/API/Body/json)或 Express’[`body-parser`](https://www.npmjs.com/package/body-parser)。只要它能将*字节*转换成 JavaScript 对象，基本上就可以使用。

将来，为了方便起见，可能会添加一些对有线格式的支持，但也是为了支持更高级的用法，比如对单个实体应用多级序列化。

Raynor 的另一个非目标是提供 RPC“服务定义”。这主要是因为试图遵循古老的格言[“做一件事并把它做好”](https://en.wikipedia.org/wiki/Unix_philosophy#Do_One_Thing_and_Do_It_Well)。此外，RPC vs REST vs graph QL vs what-have-you 还没有结束，谨慎的做法是不知道这些事情。无论如何，额外的位可以建立在其上，而不是紧密集成。

不管是谁，我现在要结束了，因为这篇文章已经很大了。希望这也是一个有趣的阅读。