# 了解 RapidJson–第 2 部分

> 原文：<https://dev.to/_no_rules_/understanding-rapidjson--part-2-ckg>

嘿，伙计们，在我之前关于 Rapidjson 的[博客](https://dev.to/_no_rules_/understanding-rapidjson-dpi-temp-slug-6318743)中，很多人在评论中要求详细的例子，所以这里是**理解 Rapidjson** 的第二部分，有一个稍微详细的例子。希望这对大家有所帮助。

我们将立即改进我在上一篇博客中的最后一个例子，并修改 **changeDom** 函数，向 Dom 树中添加更复杂的对象。

```
template <typename Document>
void changeDom(Document& d){
Value& node = d["hello"];
node.SetString("c++");
Document subdoc(&d.GetAllocator());
subdoc.SetObject(); // starting the object
Value arr(kArrayType); // the innermost array
Value::AllocatorType allocator;
for (unsigned i = 0; i < 10; i++)
// adding values to array , this function expects an allocator object
arr.PushBack(i, allocator);
// adding the array to its parent object and so on , finally adding it to the parent doc object
subdoc.AddMember("New", Value(kObjectType).Move().AddMember("Numbers",arr, allocator), subdoc.GetAllocator());
// finally adding the sub document to the main doc object
d.AddMember("testing",subdoc, d.GetAllocator());
d["f"] = true;
d["t"].SetBool(false);
} 
```

在这里，我们创建了类型为 **kArrayType** 和 **kObjectType** 的值对象，并从内到外将它们附加到它们的父节点。

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
  ],
 "testing": {
     "New": {
         "Numbers": [
             0,
             1,
             2,
             3,
             4,
             5,
             6,
             7,
             8,
             9
         ]
     }
 }
} 
```

上面的 **changeDom** 也可以使用**pretty writer**对象编写如下:

```
template <typename Document>
void changeDom(Document& d){
Value& node = d["hello"];
node.SetString("c++");
Document subdoc(&d.GetAllocator()); // sub-document
// old school write the json element by element
StringBuffer s;
PrettyWriter<StringBuffer> writer(s);
writer.StartObject();
writer.String("New");
writer.StartObject();
writer.String("Numbers");
writer.StartArray();
for (unsigned i = 0; i < 10; i++)
writer.Uint(i);
writer.EndArray();
writer.EndObject();
writer.EndObject();
subdoc.Parse(s.GetString()); // Parsing the string written to buffer to form a sub DOM

d.AddMember("testing",subdoc, d.GetAllocator()); // Attaching the Sub DOM to the Main DOM object
d["f"] = true;
d["t"].SetBool(false);
} 
```

[![](img/dd3754585da04dae2b9449ac80327645.png) ](http://feeds.wordpress.com/1.0/gocomments/subhoworld.wordpress.com/444/) [ ![](img/f819129691070369c387f356cd302496.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fyKh9wak--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://pixel.wp.com/b.gif%3Fhost%3Dcodeflu.blog%26blog%3D35076457%26post%3D444%26subd%3Dsubhoworld%26ref%3D%26feed%3D1)