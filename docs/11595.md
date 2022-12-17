# Javaで短いFizzBuzz

> 原文：<https://dev.to/shiena/java-fizzbuzz-im9>

这是几年前在[Anarchy Golf](http://golf.shinh.org/p.rb?FizzBuzz#Java) 上投稿的 java 的 FizzBuzz。
很怀念，公开了。

```
class f{public static void main(String[]a){for(int i=0;++i<101;)System.out.println((i%3<1?"Fizz":"")+(i%5<1?"Buzz":i%3<1?"":i));}} 
```

更短的代码似乎使用 enum，但由于 Anarchy Golf 会检查 stderr，所以没有通过测试。

```
enum f{a;{for(int i=0;++i<101;)System.out.println((i%3<1?"Fizz":"")+(i%5<1?"Buzz":i%3<1?"":i));}} 
```

因为 Java 的语法限制很严格，所以我认为很难像其他语言那样缩短。