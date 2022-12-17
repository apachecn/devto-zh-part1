# 了解 RapidJson

> 原文：<https://dev.to/_no_rules_/understanding-rapidjson-77p>

随着新技术的出现，软件需要发展和适应。我的新任务是让 cppagent 生成 Json (JavaScript 对象表示法)格式的输出。上周，我花了一些时间尝试不同的库，最终决定使用 Rapidjson。Rapidjson 是一个针对 c++的 json 操作库，快速、简单，兼容不同平台的不同 c++编译器。在本帖中，我们将看看生成、解析和操作 **json** 数据的示例代码。对于想使用这个库的人，我强烈建议他们先玩玩并理解[示例](https://github.com/miloyip/rapidjson/tree/master/example)代码。

首先，我们将编写一个简单的程序来编写一个示例 json，如下所示(与示例中的 [simplewriter.cpp](https://github.com/miloyip/rapidjson/blob/master/example/simplewriter/simplewriter.cpp) 相同) :

```
{
    "hello" : "world" ,
    "t" : true ,
    "f" : false ,
    "i" : 123 ,
    "pi" : 3.1416 ,
    "a": [
        0,
        1,
        2,
        3
    ]
} 
```

要生成 Json 输出，您需要:

*   一个 **StringBuffer** 对象，一个写 Json 输出的缓冲对象。
*   一个用于将 Json 写入缓冲区的 **Writer** 对象。在这里，我使用了 **PrettyWriter** 对象来编写人类可读的、适当缩进的 json 输出。
*   函数 **StartObject/EndObject** 分别启动和关闭一个 json 对象括号“ **{** ”和“ **}** ”。
*   函数 **StartArray/EndArray** 开始和结束一个 json 数组对象，即“ **[** ”和“ **]** ”。
*   在 **writer** 对象上调用函数 String()、Uint()、Bool()、null()、Double()分别写入字符串、无符号整数、布尔、Null、浮点数。

```
#include "rapidjson/stringbuffer.h"
#include "rapidjson/prettywriter.h"
#include <iostream>

using namespace rapidjson;
using namespace std;

template <typename Writer>
void display(Writer& writer );

int main() {
 StringBuffer s; 
 PrettyWriter<StringBuffer> writer(s);
 display(writer);
 cout << s.GetString() << endl; // GetString() stringify the Json
 }

template <typename Writer>
void display(Writer& writer){
 writer.StartObject(); // write "{"
 writer.String("hello"); // write string "hello"
 writer.String("world");
 writer.String("t"); 
 writer.Bool(true); // write boolean value true
 writer.String("f");
 writer.Bool(false);
 writer.String("n");
 writer.Null(); // write null
 writer.String("i");
 writer.Uint(123); // write unsigned integer value
 writer.String("pi");
 writer.Double(3.1416); // write floating point numbers
 writer.String("a");
 writer.StartArray(); // write "["
 for (unsigned i = 0; i < 4; i++)
 writer.Uint(i);
 writer.EndArray(); // End Array "]"
 writer.EndObject(); // end Object "}"
} 
```

接下来，我们将操作 Json 文档，并将键“Hello”的值更改为“C++”，

操纵:

*   首先，您需要将 json 数据解析成一个**文档**对象。
*   接下来，您可以使用一个**值**来引用所需节点/键的值，或者您可以直接以**doc _ object[key ']**的形式访问它们。
*   最后，您需要调用 **Accept** 方法，传递 **Writer** 对象，将文档写入 StringBuffer 对象。

下面的函数将关键字" hello "，" t "，" f "分别改为" c++ "，false，true。

```
template <typename Document>
void changeDom(Document& d){
// any of methods shown below can be used to change the document
Value& node = d["hello"]; // using a reference
node.SetString("c++"); // call SetString() on the reference
d["f"] = true; // access directly and change
d["t"].SetBool(false); // best way
} 
```

现在把所有这些放在一起:

```
Before Manupulation
{
     "hello": "world",
     "t": true,
     "f": false,
     "n": null,
     "i": 123,
     "pi": 3.1416,
     "a": [
        0,
        1,
        2,
        3
     ]
}
After Manupulation
{
     "hello": "c++",
     "t": false,
     "f": true,
     "n": null,
     "i": 123,
     "pi": 3.1416,
     "a": [
        0,
        1,
        2,
        3
      ]
} 
```

显示上述输出的最终代码:

```
#include "rapidjson/stringbuffer.h"
#include "rapidjson/prettywriter.h"
#include "rapidjson/document.h"
#include <iostream>

using namespace rapidjson;
using namespace std;

template <typename Writer> 
void display(Writer& writer);

template <typename Document>
void changeDom(Document& d);

int main() {
 StringBuffer s;
 Document d;
 PrettyWriter<StringBuffer> writer(s);
 display(writer);
 cout << "Before Manupulation\n" << s.GetString() << endl ;
 d.Parse(s.GetString());
 changeDom(d);
 s.Clear(); // clear the buffer to prepare for a new json document
 writer.Reset(s); // resetting writer for a fresh json doc
 d.Accept(writer); // writing parsed document to buffer
 cout << "After Manupulation\n" << s.GetString() << endl;
 }

template <typename Document>
void changeDom(Document& d){
Value& node = d["hello"];
node.SetString("c++");
d["f"] = true;
d["t"].SetBool(false);
}

template <typename Writer>
void display(Writer& writer){
 writer.StartObject();
 writer.String("hello");
 writer.String("world");
 writer.String("t");
 writer.Bool(true);
 writer.String("f");
 writer.Bool(false);
 writer.String("n");
 writer.Null();
 writer.String("i");
 writer.Uint(123);
 writer.String("pi");
 writer.Double(3.1416);
 writer.String("a");
 writer.StartArray();
 for (unsigned i = 0; i < 4; i++)
 writer.Uint(i);
 writer.EndArray();
 writer.EndObject();
} 
```

[![](img/d3900faf53f2b23d46a36e37ef26468b.png) ](http://feeds.wordpress.com/1.0/gocomments/subhoworld.wordpress.com/251/) [ ![](img/707ed1b0edbffd2dfd3d64a94ab190ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s----4bL3op--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://pixel.wp.com/b.gif%3Fhost%3Dcodeflu.blog%26blog%3D35076457%26post%3D251%26subd%3Dsubhoworld%26ref%3D%26feed%3D1)