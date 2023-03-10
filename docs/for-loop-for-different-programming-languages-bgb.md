# 不同编程语言中的 For 循环

> 原文：<https://dev.to/rattanakchea/for-loop-for-different-programming-languages-bgb>

编程通常涉及处理多余的任务。`for`循环有助于缩短代码，减少繁琐的任务。但是对于不同的语言,`for`的使用方式是不同的。这篇文章旨在为 Java、Javascript 和 PHP 提供一些处理字符串、数组和对象的`for`循环例子。

# 循环一串

## Java

```
String str = "hello";
for (int i=0; i < str.length(); i++){
    System.out.print(str.charAt(i));
}
//another less optimal solution is to convert the str into an char array
//using str.toCharArray();
//see loop an Array section in Java below 
```

Enter fullscreen mode Exit fullscreen mode

注意:
length()和 charAt(index)是 String 对象类的方法。

## JavaScript

```
var str = "hello";
for (var i=0; i < str.length; i++){
    console.log(str.charAt(i));
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:
在 JavaScript 中，我们可以通过两种方式声明 string:

```
var str1 = 'primitive';  //datatype is primitive: string
var str2 = new String('string object');  //string as object 
```

Enter fullscreen mode Exit fullscreen mode

因为原语没有方法和属性，所以 str1 被自动装箱为包装类 String(如在 s2 中)。那么 str1 就变成了一个 String 对象，以 length 为属性，以 charAt 为方法，以此类推。

# PHP

它不像 Java 那么简单，JavasScript 循环一个字符串。一种方法是将字符串转换成数组，然后我们可以循环数组。另一种方法是使用 helper 方法，substr()来获取字符串中的每个字符。

```
//method 1: use for loop
for($i=0; $i < count($array); $i++){
    echo $array[$i];
}

//method 2: convert a string to an array first, then we can loop the array
//use str_split() function to split a string by character
$str = "hello";
$array = str_split($str);  //split by character into an array
foreach($array as $value){
    echo $value;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 循环排列

## Java

```
int[] nums = new int[5];
for (int i=0; i < nums.length; i++){
    nums[i] = i; }
    System.out.print(Arrays.toString(nums)); //[0, 1, 2, 3, 4]
    //or use for (:) as for each loop
for(int i : nums){
    System.out.print(i);  //01234
}
//you may compare for(:) loop with foreach loop in PHP and other language. 
```

Enter fullscreen mode Exit fullscreen mode

注意:数组是一个固定大小的容器对象。数组的长度是在创建数组时确定的。数组具有长度属性，而不是对象中的长度方法。实际上，length 是数组的一个公共 final 字段。
点击此处阅读更多第 10 章。数组([http://docs . Oracle . com/javase/specs/jls/se7/html/jls-10 . html # jls-10.7](http://docs.oracle.com/javase/specs/jls/se7/html/jls-10.html#jls-10.7))

## Javascript

```
var nums = ["hi", "hello", "you"];
//sequential loop
for (var i=0; i < nums.length; i++){
    console.log(num[i]));   //hihelloyou
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:javascript 还有另一个循环 for-in，常用于 Javascript 对象循环。

```
var obj = {  "a": 1,  "b": 2,  "c": 3};
for (var prop in obj) { 
  if (obj.hasOwnProperty(prop)) {
// or if (Object.prototype.hasOwnProperty.call(obj,prop)) for safety..
     alert("prop: " + prop + " value: " + obj[prop])  }
} 
```

Enter fullscreen mode Exit fullscreen mode

阅读更多内容:在 JavaScript 中循环数组([http://stack overflow . com/questions/3010840/Loop-through-array-in-JavaScript](http://stackoverflow.com/questions/3010840/loop-through-array-in-javascript))

# 循环一个对象

## Java

在 Java 中，要循环一个对象数组，我们可以使用顺序循环或者迭代器或者 for(:)循环##

```
ArrayList<String> list = new ArrayList<String>();
list.add("a");
list.add("b");
Iterator<String> itr = list.iterator();
while(itr.hasNext()){
    //do something with itr.next();
    //for example itr.remove();
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//using foreach loop
for( String s : list){
    //do something with s
    // s is local String variable
    //modify s does not modify the list
} 
```

Enter fullscreen mode Exit fullscreen mode

## PHP

在 PHP 中，像在 array 中一样使用 foreach 循环来循环一个对象。

```
foreach ($objects as $obj){
    echo $obj->property;
}
//or below
foreach ($obj as $key => $value) {
    echo "$key => $value\n";
} 
```

Enter fullscreen mode Exit fullscreen mode

references
JavaScript 中的字符串文字和字符串对象有什么区别？([http://stack overflow . com/questions/17256182/JavaScript 中的字符串文字和字符串对象的区别是什么](http://stackoverflow.com/questions/17256182/what-is-the-difference-between-string-literals-and-string-objects-in-javascript))

str _ split-Manual([http://us.php.net/str_split](http://us.php.net/str_split))
数组(Java 教程>学习 Java 语言>语言基础知识)([https://docs . Oracle . com/javase/tutorial/Java/nutsandbolts/Arrays . html](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/arrays.html))