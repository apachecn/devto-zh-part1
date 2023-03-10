# 您没有使用的有用的 JS 函数:Array.filter

> 原文：<https://dev.to/aeiche/useful-js-functions-you-arent-using-arrayfilter>

假设你有一个教室里的学生名单，你想知道有多少是女生。你可以这样写:

```
var students = [
  {name:"Davey", gender:"male",age:"10"},
  {name:"Susie", gender:"female",age:"10"}, 
  {name:"Frank", gender:"male", age:"10"},
  {name:"Lindsay", gender:"male", age:"11"},
  {name:"Terry", gender:"female", age:"10"},
  {name:"Melissa", gender:"female", age:"11"}
]

var girlsCount = 0;
for(s = 0; s < students.length; s++){
    if(students[s].gender === "female"){
        girlsCount++;
    }
}

console.log("The Number of girls is: " + girlsCount);
//The Number of girls is: 3 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很好的，实用的方法。如果您利用 Javascript 的语言特性，您可以节省一些时间。试试 Javascript 的`filter`函数附在每个数组上！

```
var girls = students.filter(function(s){return s.gender === "female" });
console.log("The Number of girls is: " + girls.length); 
```

Enter fullscreen mode Exit fullscreen mode

返回一个新数组，它是你调用它的数组的子集，其中回调函数返回 true 或 false。一个`true`值将在新数组中包含该项。一个`false`值就把它漏掉了。这使得编写过滤器变得很容易，无论您需要多简单或多复杂。在我们的类中，假设我们想找到所有 11 岁的学生，并且他们的名字中有字母“e”。有了过滤器，我们只需要返回`true`，如果一个学生对象有这两样东西:

```
var eAndEleven = students.filter(function(s){return s.name.match(/e/gi) && s.age == 11});
eAndEleven.map(function(s){console.log(s.name)}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 [`Array.map`函数](https://dev.to/aeiche/useful-js-functions-you-arent-using-arraymap)将所有匹配的学生输出到我们的控制台。

说到`Array.map`，假设在生成数组的子集之前，您需要对数组进行一些转换。因为这些方法返回新的数组，所以您可以将它们链接在一起使用:

```
 students
  .map(function(s){ s.age++; return s})
  .filter(function(s){ return s.age < 12})
  .map(function(s){console.log(s.name)}) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码给每个人的年龄加一年，然后过滤掉小于 12 的人，最后的`map`将过滤后的数组输出到控制台。

和`map`一样，`filter`允许你把事情做得更简洁，并利用内置的语言特性让你的开发者生活更轻松。

如果您使用 ES6/ES2015，您可以利用箭头功能使您的代码更加紧凑:

```
 students
  .map(s=>{ s.age++; return s})
  .filter(s=>{ return s.age < 12})
  .map(s=>{console.log(s.name)}) 
```

Enter fullscreen mode Exit fullscreen mode

*我在 twitter 上收到了很多关于我的第一篇文章的评论，[你没有使用的有用的 JS 函数:Array.map](https://dev.to/aeiche/useful-js-functions-you-arent-using-arraymap) ，它鼓励把它作为一个系列，以及要包括的东西。感谢大家的反馈和建议。*