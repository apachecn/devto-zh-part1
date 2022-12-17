# 像老板一样打字

> 原文：<https://dev.to/luispa/typing-like-a-boss-in-typescript-71f>

嗨，同事们！我今天带来了一些关于 TypeScript 的最佳实践。

如果您是对 TypeScript 感兴趣的 Javascript 开发人员，我建议您练习下面列出的一些方法。

## 初学者资源

### [打印正式文件](https://www.typescriptlang.org/docs/home.html)

### [打字稿介绍(免费 Udemy 课程- 1 小时)](https://www.udemy.com/typescript/learn/v4/overview)

### [伟大的诅咒以打字稿开始(30 分钟)](https://tutorialzine.com/2016/07/learn-typescript-in-30-minutes)

### 你可以在这里测试所有的例子

## 一般类型

#### 数字、字符串、布尔和对象

不要使用类型`Number`、`String`、`Boolean`或`Object`。这些类型指的是非原始的装箱对象，它们在 JavaScript 代码中几乎从未被恰当地使用过。

```
/* WRONG */
function reverse(s: String): String; 
```

务必使用类型`number`、`string`和`boolean`。

```
/* OK */
function reverse(s: string): string; 
```

不要使用`Object`，而是使用非原语`object`类型(在 TypeScript 2.2 中添加的[)。](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-2.html#object-type)

#### 仿制药

不要使用不使用类型参数的泛型类型。在 [TypeScript FAQ 页面](https://github.com/Microsoft/TypeScript/wiki/FAQ#why-doesnt-type-inference-work-on-this-interface-interface-foot---)中查看更多详细信息。

## 回调类型

#### 返回回调类型

不要将返回类型`any`用于其值将被忽略的回调:

```
/* WRONG */
function fn(x: () => any) {
    x();
} 
```

对于其值将被忽略的回调，一定要使用返回类型`void`:

```
/* OK */
function fn(x: () => void) {
    x();
} 
```

为什么:使用`void`更安全，因为它可以防止你以一种未经检查的方式意外地使用`x`的返回值:

```
function fn(x: () => void) {
    var k = x(); // oops! meant to do something else
    k.doSomething(); // error, but would be OK if the return type had been 'any'
} 
```

#### 回调中的可选参数

不要在回调中使用可选参数，除非你真的这么想:

```
/* WRONG */
interface Fetcher {
    getObject(done: (data: any, elapsedTime?: number) => void): void;
} 
```

这有一个非常具体的含义:`done`回调可能用 1 个参数调用，也可能用 2 个参数调用。作者可能想说回调可能不关心`elapsedTime`参数，但是没有必要让参数可选来实现这一点——提供接受更少参数的回调总是合法的。

务必将回调参数写成非可选:

```
/* OK */
interface Fetcher {
    getObject(done: (data: any, elapsedTime: number) => void): void;
} 
```

#### 重载和回调

不要编写仅在回调 arity 上不同的单独重载:

```
/* WRONG */
declare function beforeAll(action: () => void, timeout?: number): void;
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void; 
```

使用最大 arity:
写一个单个的霸王

```
/* OK */
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void; 
```

原因:回调忽略参数总是合法的，所以不需要更短的重载。首先提供一个较短的回调允许传入类型不正确的函数，因为它们与第一个重载匹配。

## 功能过载

#### 订购

不要把更一般的重载放在更具体的重载之前:

```
/* WRONG */
declare function fn(x: any): any;
declare function fn(x: HTMLElement): number;
declare function fn(x: HTMLDivElement): string;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: any, wat? 
```

通过将更一般的签名放在更具体的签名之后来进行`sort`重载:

```
/* OK */
declare function fn(x: HTMLDivElement): string;
declare function fn(x: HTMLElement): number;
declare function fn(x: any): any;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: string, :) 
```

原因:TypeScript 在解析函数调用时选择第一个匹配的重载。当较早的重载比较晚的重载“更通用”时，较晚的重载实际上是隐藏的，不能被调用。

#### 使用可选参数

不要写几个只有尾部参数不同的重载:

```
/* WRONG */
interface Example {
    diff(one: string): number;
    diff(one: string, two: string): number;
    diff(one: string, two: string, three: boolean): number;
} 
```

尽可能使用可选参数:

```
/* OK */
interface Example {
    diff(one: string, two?: string, three?: boolean): number;
} 
```

请注意，只有当所有重载都具有相同的返回类型时，才会发生这种折叠。

原因:这很重要，有两个原因。

TypeScript 通过查看是否可以用源的参数调用目标的任何签名，以及是否允许无关的参数，来解决签名兼容性问题。例如，这段代码只有在使用可选参数
正确编写签名时才会暴露错误

```
function fn(x: (a: string, b: number, c: number) => void) { }
var x: Example;
// When written with overloads, OK -- used first overload
// When written with optionals, correctly an error
fn(x.diff); 
```

第二个原因是当消费者使用 TypeScript 的“严格的空检查”功能时。因为未指定的参数在 JavaScript 中显示为`undefined`，所以通常可以将一个显式的`undefined`传递给一个带有可选参数的函数。例如，这段代码在严格的空值下应该是可以的:

```
var x: Example;
// When written with overloads, incorrectly an error because of passing 'undefined' to 'string'
// When written with optionals, correctly OK
x.diff("something", true ? undefined : "hour"); 
```

#### 使用联合类型

不要只在一个参数位置写不同类型的重载:

```
/* WRONG */
interface Moment {
    utcOffset(): number;
    utcOffset(b: number): Moment;
    utcOffset(b: string): Moment;
} 
```

尽可能使用联合类型:

```
/* OK */
interface Moment {
    utcOffset(): number;
    utcOffset(b: number|string): Moment;
} 
```

注意，这里我们没有让 b 可选，因为签名的返回类型不同。

为什么:这对于向你的函数传递一个值的人来说很重要:

```
function fn(x: string): void;
function fn(x: number): void;
function fn(x: number|string) {
    // When written with separate overloads, incorrectly an error
    // When written with union types, correctly OK
    return moment().utcOffset(x);
} 
```

仅此而已！感谢阅读，永远不要停止学习。