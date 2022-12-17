# 为什么那些 web 服务在 JSON 中发送带双引号的“Number”？可铸造库可以处理它们。

> 原文：<https://dev.to/bitrinjani/why-are-those-web-services-sending-number-with-double-quotes-in-json--castable-library-can-handle-them-24f8>

# 可转换类型脚本库

[Castable](https://github.com/bitrinjani/castable) 通过将运行时的所有属性转换为编译时指定的类型来清理外部脏数据。

# 为什么需要这个库？

在 JSON 中，很多 web 服务返回带双引号的`number`类型字段**。如果通过 JSON.stringify 转换，双引号数字就会变成`string`类型！！** 

```
const serverResponse = `{
　　"name": "Milk", 
　　"price": "200", 
　　"tax": "10", 
}`;
const product = JSON.parse(serverResponse);
const sum = product.price + product.tax;
console.log(`sum: ${sum}`); // "200" + "10" = "20010"⛔️ 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 类型注释能帮上忙吗？不，TypeScript 无法检查这种运行时类型不匹配。即使类型注释是完美的，也会得到相同的结果。

这就是我建造这个图书馆的原因。Castable 可以在运行时转换这些类型。所有字段都将被转换为带注释的类型。

```
import { cast, Castable } from 'castable';

class Product extends Castable { 
  @cast name: string;
  @cast price: number;
  @cast tax: number;
}

const serverResponse = `{"name": "Milk", "price": "200", "tax": "10"}`;
const product = new Product(JSON.parse(serverResponse));
const sum = product.price + product.tax;
console.log(`sum: ${sum}`); // 200 + 10 = 210👍 
```

Enter fullscreen mode Exit fullscreen mode

Castable 在内部将数字(“200”)应用于价格字段，将数字(“10”)应用于税收字段，以便将它们转换为正确的类型，识别出那些实际上是`number`类型，而不是`string`。

支持的类型:

*   数字:“100”将转换为实数`100`。
*   boolean:JSON 中的字符串“true”、“false”将是真正的 boolean `true`、`false`。
*   日期:日期构造函数支持的任何字符串表示形式，如“周四 2017 年 1 2 月 21 日 18:38:58 GMT+0900(东京标准时间)”
*   嵌套类型
*   排列
*   多维阵列

# 安装

```
npm install @bitr/castable 
```

Enter fullscreen mode Exit fullscreen mode

# 用法

1.  延展浇注料
2.  将`@cast`装饰器添加到原始类型字段(字符串、数字、布尔值)
3.  向日期类型字段添加`@cast(Date)`装饰器
4.  将`@cast @element(T)`添加到数组类型字段
5.  向嵌套类型添加`@cast`装饰器
6.  对所有嵌套类型进行同样的操作

# 举例

这个库在 [R2](https://github.com/bitrinjani/r2) 被广泛使用，例如 [type.ts](https://github.com/bitrinjani/r2/blob/master/src/Quoine/types.ts) 。