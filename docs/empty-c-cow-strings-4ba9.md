# 空 C++ COW 字符串

> 原文：<https://dev.to/aivarsk/empty-c-cow-strings-4ba9>

标准 C++库的 GCC 实现曾经有写时复制 std::string 实现。它的好处是可以快速复制字符串，但是它也使得字符串很难在多线程程序中使用。然后 C++11 发生了，禁止(？)COW 实现，GCC5 有另一个 std::string 实现，针对小字符串进行了优化。然而出于二进制兼容性的原因，GCC 4.x 甚至在 C++11 模式下仍然使用 COW 字符串。

多年来，我训练自己寻找由滥用字符串引起的错误，如下例所示:

```
std::string a("A");
std::string a2 = a;

printf("'%s' '%s'\n", a.c_str(), a2.c_str());
*(char *)a2.c_str() = 'B';
printf("'%s' '%s'\n", a.c_str(), a2.c_str()); 
```

Enter fullscreen mode Exit fullscreen mode

结果，改变一个字符串的值会悄悄地“改变”其他一些字符串的值，因为它们都指向同一个内存结构:

```
'A' 'A'
'B' 'B' 
```

Enter fullscreen mode Exit fullscreen mode

GCC5 隐式地解决了这个问题:

```
'A' 'A'
'A' 'B' 
```

Enter fullscreen mode Exit fullscreen mode

这个问题在代码审查期间很容易发现，因为字符串在代码中彼此靠近，原始指针和类型转换都在尖叫“这里有 bug”。

最近我调试了另一个由合法但稍微不正确的 C++代码引起的 COW 字符串问题。原来 GCC C++库实现也优化了空字符串:std::string 的默认构造函数创建的字符串都指向同一个空字符串 singleton。使用第一种技术很容易弄乱空字符串。但是对于完全合法的操作符[]却忘记执行边界检查也很容易做到。在所有其他情况下，operator[]会创建共享内存结构的副本，但由于某种原因，它会悄悄地跳过对空字符串的复制，而不是进行复制，从而引发异常或中止程序。祝你好运，在远离表面的地方找到百万行代码的错误赋值！

```
std::string empty;
// in a code far, far away...
std::string empty2;

printf("'%s' '%s'\n", empty.c_str(), empty2.c_str());
empty2[0] = '?';
printf("'%s' '%s'\n", empty.c_str(), empty2.c_str());
std::cout << "'" << empty << "' '" << empty2 << "'\n"; 
```

Enter fullscreen mode Exit fullscreen mode

检查字符串大小的 C++代码将继续工作(如 std::cout ),但 C 风格的字符串和 I/O 看起来会很奇怪:

```
'' ''
'?' '?'
'' '' 
```

Enter fullscreen mode Exit fullscreen mode

哦，顺便说一下，GCC5 也隐含地限制了这种情况下的伤害范围:

```
'' ''
'' '?'
'' '' 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都证实了用 C++很容易搬起石头砸自己的脚，但也表明了与“旧的”C++相比，符合 C++11 的实现要好得多。