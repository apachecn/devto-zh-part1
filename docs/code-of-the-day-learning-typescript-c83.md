# 今日代码(学习打字稿)

> 原文：<https://dev.to/cizu64/code-of-the-day-learning-typescript-c83>

Typescript 真的很酷，感谢微软。我刚刚开始学习混合应用程序开发，我正在考虑是使用 ReactNative 还是 NativeScript。我想我会更快地学习 NativeScript，因为它直接支持 TypeScript，而且我在。NET 框架。

# 当天的代码

### 打字稿

函数 identity(arg: T): T {
返回 arg；
}

let gen = identity>(["欢迎来到 genrics in TypeScript "，1，(2+2)，" Hi "])；
console . log(gen)；

### 转换后的 Javascript

函数 identity(arg) {
返回 arg；
}
var gen = identity(["欢迎来到 genric "，1，(2 + 2)，" Hi "])；
console . log(gen)；

欢迎你和我一起学习。