# 我可以隐藏类型吗？

> 原文：<https://dev.to/saitoatsushi/can-i-hide-the-type-87k>

不能从类外部访问私有成员。除非通过指针或引用提供间接方法，否则私有成员只能是内部成员。

我可以为类型指定`private`。

例如，

```
#include <iostream> 
class foo {
private:
  class bar {};
public:
  static bar make_bar(void) {
    return bar();
  }
  static void print_bar(bar) {
    std::cout << "bar" << std::endl;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我希望躲起来。我不希望你制造`foo::bar`类型的物体。

在 C++11 及更高版本中，可以使用带有`auto`或`decltype`的私有类型。

```
int main(void) {
  foo::make_bar(); // OK
  foo::print_bar(foo::make_bar()); // OK
  foo::bar x = foo::make_bar();    // Error : foo::bar is private
  auto x = foo::make_bar();        // OK : You can use `auto` in C++11 and later
  decltype(foo::make_bar()) y = foo::make_bar(); // OK : You can use `decltype` in C++11 and later

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

有办法隐藏类型吗？