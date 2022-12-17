# 面向 JavaScript 开发人员的 Python

> 原文：<https://dev.to/underdogio/python-for-javascript-developers>

所以最近我开始在纽约市一家名为 [Underdog.io](https://underdog.io) 的小初创公司工作，在那里我发现他们有一个主要用 Python 编写的后端，这是一种我以前很少接触的语言。

虽然我被聘用主要是因为我在 JavaScript 和 React 方面的经验，但我们团队的规模很小，这意味着我经常不得不钻研代码库的所有部分，以便发布一个特性。所以我必须尽快熟悉 Python。

不幸的是，我很难找到学习 Python 的好资源，这些资源不是针对以前没有编程经验的人的。我已经知道如何编程，并且熟悉其他语言，我只需要学习这种特定编程语言 Python 的语法和范例。

这就是这篇博客的由来。作为 JavaScript 开发人员的快速指南，他们希望快速掌握 Python，但不需要了解声明变量的含义或函数是什么。

这篇文章假设你使用的是 Python 3.0.1 的 **[，所以有些例子可能无法在旧版本的 Python 上使用。](https://www.python.org/download/releases/3.0.1/)**

## 语法

### 声明变量

用 Python 声明变量非常简单。像 JavaScript 一样，在声明变量时不必设置变量的类型。你也不需要声明变量的范围(`let` vs `var` ):

```
x = 5 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过给变量赋值来改变变量的类型:

```
x = 5 # x has a type of Integer x = 'Hewwo' # x is now a String! 
```

Enter fullscreen mode Exit fullscreen mode

与 JavaScript 不同，Python 中的变量总是块范围的。

### 区块

就语法而言，Python 比 JavaScript 更严格一些。在 Python 中，缩进一个空格甚至会阻止您的程序运行(！idspnonenote)。).这是因为 Python 使用缩进而不是大括号来创建块。例如，这是您在 JavaScript 和 Python 中定义块的方式:

### 在 JavaScript 中创建块

```
function exampleFunction () {
  // This is a block
  var a = 5;
}

{
  // This is also a block
} 
```

Enter fullscreen mode Exit fullscreen mode

### 在 Python 中创建块

```
# This is a block with its own scope 
def example_function():
  # This is also a block with its own scope
  x = 5
  print(x) 
```

Enter fullscreen mode Exit fullscreen mode

如果包含`print(x)`的行有一个或多个多余的空格，Python 解释器会抛出一个`IndentationError`，因为这些多余的空格会创建一个无效的块。

```
def example_function():
  x = 5

  # IndentationError!
    print(x) 
```

Enter fullscreen mode Exit fullscreen mode

如果同一行少了一个或多个空格，就像这样:

```
def example_function():
  x = 5
 print(x) 
```

Enter fullscreen mode Exit fullscreen mode

Python 解释器会抛出这个错误:

```
NameError: name 'x' is not defined 
```

Enter fullscreen mode Exit fullscreen mode

因为`print(x)`所在的块超出了声明`x`的块的范围。

### 控制流程

Python 中的`if...else`、`while`、`for`块与 JavaScript 非常相似:

#### 如果...其他

```
if x > 2:
  print('hai!')
elif x > 3:
  print('bye!')
else:
  print('hey now')

if not x:
  print('x is falsy!') 
```

Enter fullscreen mode Exit fullscreen mode

#### while 循环

```
while x > 0:
  print('hey now') 
```

Enter fullscreen mode Exit fullscreen mode

#### 为循环

For 循环类似于 JavaScript `foreach`循环:

```
ex_list = [1, 2, 3]

for x in ex_list:
  print(x) 
```

Enter fullscreen mode Exit fullscreen mode

## 类型

Python 的类型系统很像 JavaScript 的；它是有的，但不像 Java 或 C#等其他语言那样严格。

实际上，变量是有类型的，但是你不必像在 Java 这样的静态类型语言中那样声明变量的类型。

以下是 Python 内置数据类型的快速概述:

### [数字](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)

与 JavaScript 不同，Python 有不止一种数字类型:

*   整数:`1`、`2`、`3`
*   浮动:`4.20`，`4e420`
*   复数:`4 + 20j`
*   布尔:`True`，`False`

您可以在 Python 中对数字执行与在 JavaScript 中相同的操作。还有一个求幂运算符(**):

```
# a = 4 a = 2 ** 2 
```

Enter fullscreen mode Exit fullscreen mode

### [列举](https://docs.python.org/3/library/stdtypes.html#lists)

Python 中的列表类似于 JavaScript 中的数组。列表可以包含多种类型:

```
[4, "2", [0, "zero"]] 
```

Enter fullscreen mode Exit fullscreen mode

从列表中分割元素还有一个特殊的语法:

```
a_list = [1, 2, 3, 4, 5]

# 1, 2, 3 a_list[0:2]

# 4, 5 a_list[3:]

# 3, 4 a_list[2, -2] 
```

Enter fullscreen mode Exit fullscreen mode

以及一些方便的操作列表的内置方法:

```
# 3 len([1, 2, 3])

# 3, 2, 1 [1, 2, 3].reverse()

# 1, 2, 3 [1, 2].append(3) 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以用`+`操作符
连接两个列表

```
# 1, 2, 3, 4 [1, 2] + [3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

### [琴弦](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str)

Python 中的字符串很像 JavaScript 中的字符串。它们是不可变的，单个字符可以像数组中的元素一样被访问:

```
name = 'Mario'

# M print(name[0])

# Nope, name is still 'Mario' name[0] = 'M' 
```

Enter fullscreen mode Exit fullscreen mode

### [字典](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)

字典是关联数组，类似于 JavaScript 中的对象。事实上，字典可以用类似 JSON 的语法来声明:

```
# Dictionaries in python person = {
  'name': 'Mario',
  'age': 24
}

# Mario print(person['name']) 
```

Enter fullscreen mode Exit fullscreen mode

当试图获取一个不存在的键的值时，字典有一个返回默认值的简便方法:

```
# Because `gender` is not defined, non-binary will be returned person.get('gender', 'non-binary') 
```

Enter fullscreen mode Exit fullscreen mode

### [无](https://docs.python.org/3/library/constants.html#None)

`None`相当于 JavaScript 中的`null`。它表示缺少值，被认为是“虚假的”。

```
x = None

if not x:
  print('x is falsy!') 
```

Enter fullscreen mode Exit fullscreen mode

## 功能

像 JavaScript 一样，函数也是 Python 中的对象。这意味着您可以将函数作为参数传递，甚至可以将属性赋予函数:

```
def func(a, fn):
  print(a)
  fn()

func.x = 'meep'

# 'meep' print(func.x)

def another_func():
  print('hey')

# 5
# 'hey' func(5, another_func) 
```

Enter fullscreen mode Exit fullscreen mode

## 模块

Python 中的模块与 ES6 中的模块相差不远。

### 定义一个模块

Python 中的模块只是一个包含一些 Python 代码的文件。

```
# my_module.py hey = 'heyyy'

def say_hey():
  print(hey) 
```

Enter fullscreen mode Exit fullscreen mode

不像 JavaScript，你不必声明导出的是什么；默认情况下，所有内容都会被导出。

### 导入模块

您可以在 Python 中导入整个模块:

```
# importing my_module.py from another_module.py; both files are in the same
# directory import my_module

# Do things my_module.say_hey()
print(my_module.hey) 
```

Enter fullscreen mode Exit fullscreen mode

或者从模块中导入单个项目:

```
# another_module.py from my_module import hey, say_hey

# Do things say_hey()
print(hey) 
```

Enter fullscreen mode Exit fullscreen mode

你也可以安装其他人用 Python 的包管理器
[pip](https://pypi.python.org/pypi/pip) 编写的模块。

```
pip install simplejson 
```

Enter fullscreen mode Exit fullscreen mode

## 面向对象编程

Python 支持带有类和传统继承的面向对象编程，不像 JavaScript 具有带有原型继承的原型。

### [类](https://docs.python.org/3/tutorial/classes.html#classes)

```
# Defining a class class Animal:
  # Variable that is shared by all instances of the Animal class
  default_age = 1

  # Constructor
  def __init__(self, name):
    # Defining a publicly available variable
    self.name = name

    # You can define private variables and methods by prepending the variable
    # name with 2 underscores (__):
    self.__age = default_age

  # Public method
  def get_age(self):
    return self.__age

  # Private method
  def __meow():
    print('meowwww')

  # Defining a static method with the `staticmethod` decorator
  @staticmethod
  def moo():
    print('moooo')

# Creating an Animal object animal = Animal()

# Accessing public variables and methods print(animal.name)
print(animal.default_age)
print(animal.get_age())

# Accessing a static method Animal.moo()

# ERR!!!! .__age is private, so this won't work: print(animal.__age) 
```

Enter fullscreen mode Exit fullscreen mode

### [传承](https://docs.python.org/3/tutorial/classes.html#inheritance)

类可以从其他类继承:

```
# Inheriting from the Animal class class Human(Animal):
  def __init__(self, name, address):
    # Must call the __init__ method of the base class
    super().__init__(name)
    self.__address = address

  def get_address(self):
    return self.address

# Using the Human class human = Human('Mario', '123 Jane Street, Brooklyn, NY 11211')

# Human objects have access to methods defined in the Animal base class human.get_age()
human.get_address() 
```

Enter fullscreen mode Exit fullscreen mode

## 资源

除了本指南中的内容之外，Python 还有很多内容。我强烈推荐你查看一下 [Python 文档](https://docs.python.org/3/)中的教程和其他语言特性的细节。

记住，学习一门语言的最好方法是大量地写。所以开始编码吧！

如果你需要一个项目的想法，也许可以试着用 [Flask](http://flask.pocoo.org/) 创建一个简单的 API？