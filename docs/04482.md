# ES6 初学者用示例

> 原文：<https://dev.to/srebalaji/es6-for-beginners-with-example-c7>

在这篇文章中，我将介绍 ES6 的一些新特性。如果你是 ES6 新手或者正在学习前端框架，这将会很有帮助。

我将在这篇文章中讨论的话题

1.  让和 Const
2.  箭头功能
3.  默认参数
4.  for of 循环
5.  传播属性
6.  地图
7.  设置
8.  静态方法
9.  Getters 和 Setters

**让**

let 类似于 var，但是 let 有作用域。let 只能在它所定义的块级中访问。

```
if (true) {
 let a = 40;
 console.log(a); //40
}
console.log(a); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，变量“a”是在 If 语句中定义的，所以在函数外部是不可访问的。

再比如:

```
let a = 50;
let b = 100;
if (true) {
 let a = 60;
 var c = 10;
 console.log(a/c); // 6
 console.log(b/c); // 10
}
console.log(c); // 10
console.log(a); // 50 
```

Enter fullscreen mode Exit fullscreen mode

**常数**

Const 用于给变量赋一个常数值。并且该值不可更改。已经修好了。

```
const a = 50;
a = 60; // shows error. You cannot change the value of const.
const b = "Constant variable";
b = "Assigning new value"; // shows error.
Consider another example.
const LANGUAGES = ['Js', 'Ruby', 'Python', 'Go'];
LANGUAGES = "Javascript"; // shows error. 
LANGUAGES.push('Java'); // Works fine.
console.log(LANGUAGES); // ['Js', 'Ruby', 'Python', 'Go', 'Java'] 
```

Enter fullscreen mode Exit fullscreen mode

这可能有点令人困惑。

这样考虑。每当定义一个常量变量时，Javascript 都会引用该变量的值的地址。在我们的例子中，变量“语言”实际上是指分配给数组的内存。所以你不能改变这个变量来引用其他的内存位置。在整个程序中，它只引用数组。

**箭头功能**

ES6 中的功能有一点改变。我指的是语法。

```
// Old Syntax
function oldOne() {
 console.log("Hello World..!");
}
// New Syntax
var newOne = () => {
 console.log("Hello World..!");
} 
```

Enter fullscreen mode Exit fullscreen mode

新的语法可能会有点混乱。但我会试着解释语法。语法有两部分。

1.  无功一失=-
2.  => {}

第一部分只是声明一个变量，并为它分配函数(即())。它只是说变量实际上是一个函数。

然后第二部分是声明函数的主体部分。带花括号的箭头部分定义了正文部分。

另一个带参数的例子。

```
let NewOneWithParameters = (a, b) => {
 console.log(a+b); // 30
}
NewOneWithParameters(10, 20); 
```

Enter fullscreen mode Exit fullscreen mode

我想我不需要对此做出解释。这很简单。

**默认参数:**

如果您熟悉 Ruby、Python 等其他编程语言，那么默认参数对您来说并不陌生。

默认参数是在声明函数时默认给出的参数。但是它的值可以在调用函数时改变。

示例

```
let Func = (a, b = 10) => {
 return a + b; 
}
Func(20); // 20 + 10 = 30 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们只传递一个参数。该函数使用默认参数并执行该函数。

考虑另一个例子:

```
Func(20, 50); // 20 + 50 = 70 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，该函数采用两个参数，第二个参数替换默认参数。

考虑另一个例子:

```
let NotWorkingFunction = (a = 10, b) => {
 return a + b;
}
NotWorkingFunction(20); // NAN. Not gonna work. 
```

Enter fullscreen mode Exit fullscreen mode

当你调用带有参数的函数时，它们会按顺序被赋值。(即，第一个值赋给第一个参数，第二个值赋给第二个参数，依此类推)..

在上面的例子中，值 20 被赋给参数‘a ’,而‘b’没有任何值。所以我们得不到任何输出。

但是，

```
NotWorkingFunction(20, 30); // 50; 
```

Enter fullscreen mode Exit fullscreen mode

工作正常。

**For of 循环**

为..的与的非常相似..稍加修改。

为..of 遍历元素列表(例如 Array)并逐个返回元素(不是它们的索引)。

```
let arr = [2,3,4,1];
for (let value of arr) {
 console.log(value);
}
Output:
2
3
4
1 
```

Enter fullscreen mode Exit fullscreen mode

请注意，变量“value”输出数组中的每个元素，而不是索引。

另一个例子

```
let string = "Javascript";
for (let char of string) {
 console.log(char);
}
Output:
J
a
v
a
s
c
r
i
p
t 
```

Enter fullscreen mode Exit fullscreen mode

是的。它也适用于字符串。

**传播属性**

顾名思义，传播属性有助于传播表达式。简单地说，它将元素列表转换为数组，反之亦然。

无跨页属性的示例:

```
let SumElements = (arr) => {
 console.log(arr); // [10, 20, 40, 60, 90]
 let sum = 0;
 for (let element of arr) {
  sum += element;
 }
 console.log(sum); // 220\. 
}
SumElements([10, 20, 40, 60, 90]); 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子很简单。我们声明一个函数来接受数组作为参数并返回它的和。很简单。

现在考虑带有扩展属性
的相同示例

```
let SumElements = (...arr) => {
 console.log(arr); // [10, 20, 40, 60, 90]
let sum = 0;
 for (let element of arr) {
  sum += element;
 }
 console.log(sum); // 220\. 
}
SumElements(10, 20, 40, 60, 90); // Note we are not passing array here. Instead we are passing the elements as arguments. 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，spread 属性将元素列表(即参数)转换为一个数组。

再比如:

```
Math.max(10, 20, 60, 100, 50, 200); // returns 200. 
```

Enter fullscreen mode Exit fullscreen mode

Math.max 是一个简单的方法，从给定的列表中返回最大的元素。它不接受数组。

```
let arr = [10, 20, 60];
Math.max(arr); // Shows error. Doesn't accept an array. 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们用我们的救世主。

```
let arr = [10, 20, 60];
Math.max(...arr); // 60 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，spread 属性将数组转换为元素列表。

**地图**

Map 保存键值对。它类似于一个数组，但是我们可以定义自己的索引。而索引在地图中是唯一的。

示例:

```
var NewMap = new Map();
NewMap.set('name', 'John'); 
NewMap.set('id', 2345796);
NewMap.set('interest', ['js', 'ruby', 'python']);
NewMap.get('name'); // John
NewMap.get('id'); // 2345796
NewMap.get('interest'); // ['js', 'ruby', 'python'] 
```

Enter fullscreen mode Exit fullscreen mode

我认为上面的例子是不言自明的。

地图的其他有趣特性是所有索引都是唯一的。我们可以用任何值作为键或值。

示例:

```
var map = new Map();
map.set('name', 'John');
map.set('name', 'Andy');
map.set(1, 'number one');
map.set(NaN, 'No value');
map.get('name'); // Andy. Note John is replaced by Andy.
map.get(1); // number one
map.get(NaN); // No value
Other useful methods used in Map:
var map = new Map();
map.set('name', 'John');
map.set('id', 10);
map.size; // 2\. Returns the size of the map.
map.keys(); // outputs only the keys. 
map.values(); // outputs only the values.
for (let key of map.keys()) {
 console.log(key);
}
Output:
name
id 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，map.keys()返回映射的键，但是它是在迭代器对象中返回的。意思是不能照原样展示。它应该只通过迭代来显示。

再比如:

```
var map = new Map();
for (let element of map) {
 console.log(element);
}
Output:
['name', 'John']
['id', 10] 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子不言自明。森林..of 循环输出数组中的键值对。

我们可以稍微优化一下。

```
var map = new Map();
for (let [key, value] of map) {
 console.log(key+" - "+value);
}
Output:
name - John
id - 10 
```

Enter fullscreen mode Exit fullscreen mode

**设置**

集合用于存储任何类型的唯一值。简单的..！

示例

```
var sets = new Set();
sets.add('a');
sets.add('b');
sets.add('a'); // We are adding duplicate value.
for (let element of sets) {
 console.log(element);
}
Output:
a
b 
```

Enter fullscreen mode Exit fullscreen mode

请注意，不会显示重复的值。将显示唯一的值。还要注意集合是可迭代的对象。我们必须遍历元素来显示它。

其他有用的方法:

```
var sets = New Set([1,5,6,8,9]);
sets.size; // returns 5\. Size of the size.
sets.has(1); // returns true. 
sets.has(10); // returns false. 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，大小是不言自明的。还有另一个方法“has ”,它根据给定元素是否出现在集合中返回一个布尔值。

**静态方法**

你们大多数人已经听说过静态方法。ES6 中引入了静态方法。定义和使用它非常容易。

示例:

```
class Example {
 static Callme() {
 console.log("Static method");
 }
}
Example.Callme();
Output:
Static method 
```

Enter fullscreen mode Exit fullscreen mode

注意，我没有在类中使用关键字“function”。

我可以调用这个函数，而不用为这个类创建任何实例。

**getter 和 setter**

Getters 和 setters 以及 ES6 中引入的有用特性之一。如果你在 JS 中使用类，它会派上用场。

没有 getters 和 setters 的例子:

```
class People {
constructor(name) {
      this.name = name;
    }
    getName() {
      return this.name;
    }
    setName(name) {
      this.name = name;
    }
}
let person = new People("Jon Snow");
console.log(person.getName());
person.setName("Dany");
console.log(person.getName());
Output:
Jon Snow
Dany 
```

Enter fullscreen mode Exit fullscreen mode

我认为上面的例子不言自明。我们在 People 类中有两个函数，帮助设置和获取人名。

getters 和 setters 的例子

```
class People {
constructor(name) {
      this.name = name;
    }
    get Name() {
      return this.name;
    }
    set Name(name) {
      this.name = name;
    }
}
let person = new People("Jon Snow");
console.log(person.Name);
person.Name = "Dany";
console.log(person.Name); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，你可以看到在类 People 中有两个函数，分别具有“get”和“set”属性。“get”属性用于获取变量的值,“set”属性用于设置变量的值。

你可以看到调用 getName 函数时没有括号。调用 setName 函数时不带括号，就像给变量赋值一样。

谢谢你的时间。希望你喜欢这篇文章。:) :)