# (Im)。可变-->(OOP)对象和 CPython

> 原文：<https://dev.to/ianliujohnston/immutable--oopbjects-and-cpython>

# 简介

Python 编程语言以新颖的方式使用数据结构。它建立在 C 中的原始数据类型的基础上，形成一个名为`PyObject`的数据结构，它是所有数据类型继承的超类。由于这种抽象，Python 中的一切都是对象。这意味着 Python 中的所有东西都是一个类，可以有自己的方法和属性，换句话说:自己的函数和变量。例如:在 C 语言中，一个有符号整数可以有一个从-2，147，483，648 到+2，147，483，647 的值，但是在 Python 中，一个有符号整数可以是任何值，远远超过这些限制。在这篇文章中，我试图提供一个关于 Python 如何处理对象和对象可变性的 C 级解释。

* * *

## 所有类的根，PyObject

为了开始讨论对象，必须首先描述`PyObject`,因为所有其他对象都继承自这个结构。

```
typedef struct _object {
        _PyObject_HEAD_EXTRA
        Py_ssize_t ob_refcnt;
        struct _typeobject *ob_type;
} PyObject; 
```

Enter fullscreen mode Exit fullscreen mode

> *源代码在这里找到:Python-3.4.3/Include/object.h 第 105–109 行。*

这里，`_PyObject_HEAD_EXTRA`是扩展到
的宏

```
#define _PyObject_HEAD_EXTRA             \
        struct _object *_ob_next;        \
        struct _object *_ob_prev; 
```

Enter fullscreen mode Exit fullscreen mode

> *源代码可以在这里找到:第 70–73 行的 Python-3 . 4 . 3/Include/object . h*

它表明`PyObject`结构的部分定义是两个指针，`_ob_next`和`_ob_prev`(类型`_object`)，它们“支持所有活动堆对象的双向链表”*(来自 object.h 的注释，第 69 行)*。声明的任何新对象都将在匿名堆中为其分配空间，并将被双重链接到先前分配的对象。

行:`Py_ssize_t ob_refcnt;`统计动态分配给任何活动的`PyObject`的引用的数量。该计数用于跟踪堆中的内存分配。`ob_refcnt`声明为类型`Py_ssize_t`，在需要 C 级有符号整数来索引 Python 序列的地方使用。*(参见:Tim Peters 上栈溢出)*

最后一行结构`_typeobject *ob_type;`是该结构的主体。类型`_typeobject`声明了一些变量，这些变量控制对象放入内存、释放、为打印或文档提供消息、获取对象的方法或属性等方式。`*ob_type`指针是为每个对象类提供基本框架的抽象。为简洁起见，这里我就不包含源代码了，参考下面的 C 文件:*Python-3 . 4 . 3/Doc/includes/typestruct . h .*

拥有这样一个超类的灵活性使得这种语言能够被松散地类型化。函数不必严格定义它们可以接受什么，对象可以很容易地转换，方法可以在任何类型的对象上调用，而不需要显式转换(有时)。因为它是一种松散类型的语言，所以应该在需要时执行输入检查和错误处理，以确保使用正确的数据类型。

这里有一个非常简单的例子来说明 Python 的松散类型:

```
>>> a = 4 
>>> b = 5.24
>>> a
4 
>>> b
5.24
>>> a = [1, 2, 3]
>>> a
[1, 2, 3]
>>> a = b
>>> a
5.24
>>> b
5.24 
```

Enter fullscreen mode Exit fullscreen mode

这里我把`a`赋值为整数:`4`，`b`赋值为浮点数:`5.24`。然后我将`a`指定为列表对象`[1, 2, 3]`。然后我将`a`赋值为`b`，瞧:`a`现在是一个浮点数。它在 3 种类型之间转换，没有任何类型的铸造！这一切都是因为每个对象都继承了超类`PyObject`。

## 分类物体

## 具有 id( )和类型()

因为一切都是对象，所以每个变量都可以在被请求时提供关于它们自己的数据。有许多内置函数可以返回关于对象的数据，本文涉及的有`id()`、`type()`和`isinstance().`

* * *

`id()`是一个内置方法，返回传递给它的对象的内存地址。其用法如下:

```
>>> a_list = [1, 2, 3]
>>> id(a_list)
140603371157192
>>> 
```

Enter fullscreen mode Exit fullscreen mode

`id()`函数在源代码中定义如下:

```
static PyObject *
builtin_id(PyObject *self, PyObject *v)
{
        return PyLong_FromVoidPtr(v)
}
PyDoc_STRVAR(id_doc,
"id(object) -> integer\n\ \n\
Return the identity of an object. This is guaranteed to be unique among\n\
simultaneously existing objects. (Hint: it's the object's memory address.)"); 
```

Enter fullscreen mode Exit fullscreen mode

> *源代码可以在这里找到:Python-3 . 4 . 3/Python/bltinmodule . c 第 996–1006 行。*

函数`PyLong_FromVoidPtr(v)`将对象从指针值(十六进制内存地址)转换成 Python 对象。(从`PyDoc_STRVAR`开始的第二部分是`help(id)`函数调用的文档。)

`id()`函数在某些情况下可能返回相同的内存地址，例如

```
>>> a = 15
>>> b = 15
>>> id(a)
8922176
>>> id(b)
8922176 
```

Enter fullscreen mode Exit fullscreen mode

这是因为这两个整数是同一个对象。在变量`a`被赋值之前，解释器被加载到内存中，261 个整数的数组被预分配。该数组中的值从-5 到+257 增加 1，以加快访问小数字的速度。这些值由宏`NSMALLNEGINTS(-5)`和`NSMALLPOSINTS (+257)`扩展而来。函数`int _PyLong_Init(void);`在 for 循环中线性填充数组，并进行错误检查。这意味着值-5 和+257 之间的任何数字将总是具有相同的内存地址，因为它们是在解释器加载到内存中时分配的。由于这种预分配，这些小整数也是不可变的。
*源代码在这里找到:Python-3 . 4 . 3/Objects/long object . c 第 5075 行到第 5111 行。*

`type()`也是一个内置函数，如果需要，它会返回一个包含类名和其他信息的字符串。下面是一个使用类型获取对象类信息的例子:

```
>>> a = [1, 2, 3] 
>>> type(a)
<class 'list'> 
>>> type(a).__base__ 
(<class 'object'>)
>>> type(a).__mro__ 
(<class 'list'>, <class 'object'>)
>>> b = True
>>> type(b)
<class 'bool'>
>>> type(b).__base__
(<class 'int'>)
>>> type(b).__mro__
(<class 'bool'>, <class 'int'>, <class 'object'>) 
```

Enter fullscreen mode Exit fullscreen mode

此示例还显示了如何使用 type 来显示对象如何从层次结构中的多个类继承。`a`和`b`都是对象类的实例。`a`只继承了另一个类:list 对象，而`b`同时继承了 int 和 bool 类。要查看所有可用方法的列表，请尝试`type(object).__dict__`。

这就是`type()`在源代码:
中的实现方式

```
PyObject *
PyObject_Type(PyObject *o)
{
        PyObject *v;
        if (o == NULL)
                return null_error();
        v = (PyObject *)o->ob_type;
        Py_INCREF(v);
        return v;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 源代码可以在这里找到:Python-3 . 4 . 3/Include/abstract . h，第 30–40 行。

前几行声明了一个新的 python 对象，并检查指针是否为空。下一行`v = (PyObject *)o->ob_types;`将新的`PyObject`的值指定为传递给函数的对象的`ob_type`字段。由`o`指向的结构被转换成基类`PyObject`以得到字段`ob_type`，该字段存储在基类中。

方法`type()`和`isinstance()`看起来似乎可以在相同的情况下使用，但是它们有完全不同的功能。

```
>>> a = [1, 2, 3]
>>> b = True
>>> type(a)
<class 'list'>
>>> isinstance(a, list)
True
>>> isinstance(a, int)
False
>>> type(b)
<class 'bool'>
>>> isinstance(b, list)
False
>>> isinstance(b, int)
True
>>> isinstance(b, bool)
True 
```

Enter fullscreen mode Exit fullscreen mode

`isinstance()`递归搜索传递对象的继承，在其继承层次中找到基本对象类。实际的递归函数在函数中的 *Objects/abstract.c 中定义:PyObject_IsInstance，第 2485–2512 行*。方法`isinstance(True, int)`将总是指向基类，它是一个 int。像这样调用它时返回 True 的原因是:`isinstance(True, bool)`，因为 bool 是 int 的子类。这与`type()`的功能不同，因为`type()`只显示最后继承的类。

在源代码:

```
static PyObject *
builtin_isinstance(PyObject *self, PyObject *args)
{
    PyObject *inst;
    PyObject *cls;
    int retval;
    if (!PyArg_UnpackTuple(args, "isinstance", 2, 2, &inst, &cls))
        return NULL;

    retval = PyObject_IsInstance(inst, cls);
    if (retval < 0)
        return NULL;
    return PyBool_FromLong(retval);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 源代码可以在这里找到:Python-3 . 4 . 3/Python/bltinmodule . c 第 2159–2173 行。

创建了两个指向`PyObjects`的本地指针`inst`(实例)和`cls`(类)以及一个状态值`retval`。c 不包含 Python 元组，所以它调用一个方法来解包可能已经传递到函数中的元组。`isinstance()`也可以叫:`isinstance(variable, (class1, class2, class3))`。调用函数`PyArg_UnpackTuple(args, "isinstance", 2, 2, &inst, &cls))`时，`PyObject`指针被赋值，同时检查是否为空。

* * *

## 把所有的东西放在一起

一些不可变的对象是整型、浮点型、元组、字符串和冻结集，一些可变的类型是列表、集合、字典和一些用户定义的类。在这里，我们可以观察可变数据类型和不可变数据类型的区别。

字符串可能看起来是可变的，但是如果一个字符串与另一个字符串`string += other_str`连接在一起，第三个字符串就会在前两个字符串的基础上创建出来。为了节省内存，如果两个字符串相同，就会创建一个指向内存中相同位置的新指针。

下面是这个用`id()`函数说明的例子:

```
>>> a = "concat"
>>> b = "concat"
>>> c = "enate"
>>> id(a)
139904216780000
>>> id(b)
139904216780000
>>> id(c)
139904216780056
>>> print(a)
concat
>>> a += c
>>> print(a)
concatenate
>>> id(a)
139904216801712
>>> a += b
>>> print(a)
concatenateconcat
>>> id(a)
139904216784520 
```

Enter fullscreen mode Exit fullscreen mode

对于两个以上的字符串，内存效率更高的方法是将字符串附加到一个可变的数据类型(在本例中是一个列表)，然后将其转换成一个字符串。

```
>>> new_list=["concat"]
>>> d = "enate"
>>> id(new_list)
139904217591048
>>> id(d)
139904216780056
>>> for num in range(1,4):
...     new_list.append(d)
...
>>> print(new_list)
['concat', 'enate', 'enate', 'enate']
>>> id(new_list)
139904217591048
>>> new = "".join(new_list)
>>> print(new)
concatenateenateenate
>>> id(new)
139904176767744 
```

Enter fullscreen mode Exit fullscreen mode

这里，不是创建五个字符串，而是创建两个。当你改变一个不可变的对象时，它实际上改变了对象的身份。也就是:对象的内存地址会改变到内存中不同的位置。

内存地址用`id()`函数显示，而关于不同级别的类继承的信息用函数`type()`和`isinstance()`显示。这些内置的类方法继承自基类，对象由结构`PyObject`定义，所有活动对象(局部函数范围内的对象)作为双向链表存储在匿名堆内存中。从`PyObject`继承的对象导致语言被松散地转换，减少了将对象显式类型转换为其他类型的需要。虽然看起来 Python 的语法比 C 之类的结构化语言更简单，但是在它的内部有很多东西可以让概念得以体现。

* * *

## 来源:

主要来源:Python-3.4.3 源代码[https://www.python.org/downloads/release/python-343/](https://www.python.org/downloads/release/python-343/)

[内置函数- Python 3.4.5 文档](https://docs.python.org/3.4/library/functions.html#type)

[通用对象结构- Python 3.4.5 文档](https://docs.python.org/3.4/c-api/structures.html)

[Python 对象:可变与不可变](https://codehabitude.com/2013/12/24/python-objects-mutable-vs-immutable/)