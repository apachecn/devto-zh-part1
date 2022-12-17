# tuple——c#中的一种新数据结构

> 原文：<https://dev.to/mandarbadve/tuple-a-new-data-structure-in-c>

*本帖最初发表于[blog.mandarbadve.com](http://blog.mandarbadve.com/2016/01/20/tuple-a-new-data-structure-in-c/)T3】*

Tuple 是 C#中一种有趣的数据结构。它包含数字和序列。您可以创建 1 到 7 个元素的元组。此外，您可以使用 TRest 元素创建八个元素的元组。TRest 元素允许您添加嵌套元组。

让我们看看基本元组的例子。我们有学生实体有七个科目的分数。我们可以通过使用元组来定义这种数据结构。

```
var tupleStudent = Tuple.Create("Mandar Badve", 87, 45, 76, 78, 64, 71, 80);
Console.WriteLine(""+ tupleStudent.Item1 + " got " + tupleStudent.Item2 + " marks in first subject.");
//// OUTPUT: MandarÂ BadveÂ gotÂ 87Â marksÂ inÂ firstÂ subject.
Console.WriteLine(""+ tupleStudent.Item1 + " got " + tupleStudent.Rest.Item1 + " marks in seventh subject.");
//// OUTPUT: Mandar Badve got 80 marks in seventh subject. 
```

在上面的例子中，第一个控制台将打印使用元组的项目 1 访问的学生的名字，以及使用元组的项目 2 访问的第一个主题中得到的标记。在第二个控制台中，我们使用元组的 Rest 属性访问嵌套元组。

### 你可以用两种方式创建元组。

*   使用构造函数

```
var tupleUsingConstructor = new Tuple<string, int, int, int, int, int, int>("Mandar Badve", 87, 45, 76, 78, 64, 71); 
```

*   使用辅助方法 Create()

```
var tupleByHelperMethod = Tuple.Create("Mandar Badve", 87, 45, 76, 78, 64, 71, 80); 
```

### 创建方法有不同的重载如下:

##### 描述

|
|创建< T1 > (T1) |创建一个 1 元组，或单元组|
|创建< T1，T2 > (T1，T2) |创建一个 2 元组，或对。|
|创建< T1，T2，T3 > (T1，T2，T3) |创建一个三元组。|
|创建< T1，T2，T3，T4 > (T1，T2，T3，T4) |创建一个 4 元组，或四元组。|
|创建< T1，T2，T3，T4，T5 > (T1，T2，T3，T4，T5) |创建一个五元组。|
|创建< T1，T2，T3，T4，T5，T6 > (T1，T2，T3，T4，T5，T6) |创建一个六元组。|
|创建< T1，T2，T3，T4，T5，T6，T7 > (T1，T2，T3，T4，T5，T6，T7) |创建一个七元组。|
|创建< T1，T2，T3，T4，T5，T6，T7，T8 > (T1，T2，T3，T4，T5，T6，T7，T8) |创建一个 8 元组，或者|