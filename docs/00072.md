# 如何在 C++中使用多个文件

> 原文：<https://dev.to/iamthebryguy94/how-to-use-multiple-files-in-c-adn>

当我还是 C++领域的新手时，我个人在试图弄清楚如何将类与 main.cpp 文件分开时遇到了相当大的麻烦。

所以我想我会在这里传递我的知识来帮助其他开始学习 C++的人——我们将使用一个“Person”类作为例子。

从技术上讲，您可以使用一个文件来保存通用的“Main”类和“Person”类——但是这将使可重用性难以组织和理解。因此，尽可能细粒度地保持类通常是一个很好的例子，我的意思是每个文件一个类。

为了让您了解当两个类都在同一个文件中时会是什么样子，请注意下面的代码:

```
int main(int argc, char** argv) {
    std::cout << "Hello World" << std::endl;
}

class Person
{
private:
    std::string m_givenname;
    std::string m_familyname;

public:
    Person() {};
    ~Person() {};

}; 
```

Enter fullscreen mode Exit fullscreen mode

按照上面设置代码的方式，我们只能访问这个文件中的类。更不用说当你试图以这种方式使用类时，Visual Studio 会抛出一些错误。不可避免的是，较大的 C++应用程序将增长到不止一个文件，因此创建易于扩展的类在以后创建更复杂的软件时将非常有用。

需要创建两个文件(如果使用 Visual Studio，创建起来很简单)，它们是一个头文件，包含所有预定义的函数原型，这类似于函数名及其参数的初始规划；和一个 CPP 文件，它保存了函数本身的内部结构。

再次以我们的“Person”类为例，我将展示下面的文件示例:

" Person.h "(头文件)

```
#include <iostream> 
class Person
{
private:
    std::string m_givenname;
    std::string m_familyname;

public:
    Person();    //Constructor prototype
    ~Person();   //Destructor prototype
}; 
```

Enter fullscreen mode Exit fullscreen mode

" Person.cpp" (CPP 文件)

```
#include "Person.h" 
Person::Person()
{

}

Person::~Person()
{

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，有了这个类的配置——当您希望使用这个类时，您所需要做的就是包含一个(类名可能因您的情况而异)

`#include "Person.h"`

，然后就像平常一样继续创建一个实例。

这样可以避免将类文件加载到软件处理的每个文件中，这样可以节省时间和系统资源，因为您可以决定何时何地使用它。