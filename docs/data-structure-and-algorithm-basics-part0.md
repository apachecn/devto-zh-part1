# 数据结构和算法基础部分[0]

> 原文：<https://dev.to/adroitcoder/data-structure-and-algorithm-basics-part0>

在你进入数据结构和算法#mypersonalnotebook 之前，我假设你对以下内容有基本的了解:

*   有条件的
*   重复结构(对于 loop 和 while 循环)
*   功能
*   变量作用域
*   递归
*   声明类

JavaScript 程序员都知道的基本知识。

#### 注:

*   所有输出都将显示在控制台上。
*   数据结构和算法将被分成几个部分。
*   如果你没有以上列出的基础知识，我会推荐你去拿一本[口才 Javascript](http://eloquentjavascript.net)
*   这里使用的所有代码都可以在我的 [Github](https://github.com/adroitcoder) 库中找到。

### 阵列

每种编程语言都包含某种形式的数组。数组是计算机编程中最常见的数据结构。

### JavaScript 中的  数组

javascript 中的数组是元素的线性集合，其中每个元素都可以通过索引(数字索引)来访问。

### 如何用 Javascript 创建数组

在 javascript 中创建数组的最推荐方式是使用计算成员访问操作符 **[]** 声明一个变量。

```
var myArray = []; 
```

Enter fullscreen mode Exit fullscreen mode

当您创建这样的数组时，您将得到一个长度值为 0 的数组。为了确保万无一失，我们来玩玩吧。

```
var myArray =  [];
console.log(myArray.length)//0 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用构造的模式创建一个数组。

```
var myArray =  new Array();
console.log(myArray.length)//0 
```

Enter fullscreen mode Exit fullscreen mode

##### 注:

*   javascript 中的数组是任何东西(函数、对象、原语等)的集合
*   计数从索引 0 而不是 1 开始

### 访问数组元素

可以使用 **[]** 操作符来访问数组元素。

```
var names =  ["Somto", "Ezeh",];
console.log(names[0] + '  ' names[1])// Somto Ezeh 
```

Enter fullscreen mode Exit fullscreen mode

### 写入数组元素

我们用 **[]**
给数组赋值

```
var numbers =  [];
for(var i= 0; i < 10; i++){
    numbers[i] = i+1;
}
console.log(numbers)//1,2,3,4,5,6,7,8,9,10 
```

Enter fullscreen mode Exit fullscreen mode

### 从字符串数组

在一个字符串上调用 **split()** 函数会将该字符串分解成单独的部分，并从该单独的字符串生成一个数组。

```
var string = "I Love javascript ";
var makeArray = string.split('  ');
console.log(makeArray);//["I","Love","javascript"] 
```

Enter fullscreen mode Exit fullscreen mode

### 加速器功能

使用 javascript *访问器*函数，我们可以访问数组的元素并返回一些值。

**indexOf()**

indexOf 函数循环遍历一个数组，查看传递给它的参数是否在目标数组中找到，并返回位置。

```
 var names = ["John","Samuel","Samuel","Obinna"];
 var position = names.indexOf("Samuel");
 console.log(position);//1 
```

Enter fullscreen mode Exit fullscreen mode

函数的作用是:返回与参数匹配的第一个元素的位置，如果没有找到，则返回-1。

**lastIndexOf()**
lastIndexOf 函数遍历一个数组，查看传递给它的参数是否在目标数组中找到，如果没有找到，则返回最后一个匹配的位置或返回-1。

```
 var names = ["John","Samuel","Samuel","Obinna"];
 var position = names.lastIndexOf("Samuel");
 console.log(position);//2 
```

Enter fullscreen mode Exit fullscreen mode

**toString()** 和 **join()** 返回用逗号分隔的数组的字符串表示。

```
 var myArray  = ["How to use","Accesor","Functions"];
  var makeString = myArray.join()
  console.log(makeString);//How to use,Accesor,Functions 
```

Enter fullscreen mode Exit fullscreen mode

为了去掉逗号，您向 *join()* 函数传递一个空引号。

```
 var myArray  = ["How to use","Accesor","Functions"];
  var makeString = myArray.join("  ")
  console.log(makeString);//How to use Accesor Functions 
```

Enter fullscreen mode Exit fullscreen mode

**concat()** 和 **splice()** 函数从现有数组中产生一个新数组。
**concat()**函数组合更多的数组并生成一个新数组，而 **splice()** 函数从目标数组的子集生成一个新数组。

*concat()*

```
var firstArray  = ["Princess", "Precious", "Patience", "Pessy"];
var secondArray = ["Adesunloye","Adedeji","Adetoro"];
var joinAll = firstArray.concat(secondArray);
console.log(joinAll);//["Princess", "Precious", "Patience", "Pessy","Adesunloye","Adedeji","Adetoro"] 
```

Enter fullscreen mode Exit fullscreen mode

*拼接()*T2】

```
var names  = ["Princess", "Precious", "Patience", "Pessy"];
var newArray = names.splice(2,3);
console.log(newArray);//["Patience","Pessy"] 
```

Enter fullscreen mode Exit fullscreen mode

### 赋值函数

**pop()**

函数的作用是:从一个数组的末尾移除一个元素并返回值。

```
var numbers   = [1,2,3,4,5,6];
var remove  = numbers.pop();
console.log(remove);// 6 
```

Enter fullscreen mode Exit fullscreen mode

向数组添加元素
有两种方法可以向数组添加元素。
它们是 *push()* 和 *unshift()* 但是我们要先说一下 *push()* 函数。

**push()**

函数的作用是:在数组末尾添加一个元素。

```
var array = [1,2,3,4,5,6,7,8];
array.push(9)
console.log(array) // 1,2,3,4,5,6,7,8,9 
```

Enter fullscreen mode Exit fullscreen mode

**un shift()**
*un shift()*函数将一个元素添加到数组的开头。

```
var array = [2,3,4]; 
array.unshift(1);
console.log(array)//1234 
```

Enter fullscreen mode Exit fullscreen mode

**shift()**
*shift()*函数从数组的开头移除一个元素并返回值

```
var array = ["I", "LOVE", "ARRAYS"];
var firstElementRemoved = array.shift(); 
console.log(firstElementRemoved);// I

//You can also decide to discard the return value 

var array = ["I", "LOVE", "ARRAYS"];
            array.shift();
console.log(array);//["LOVE", "ARRAYS"] 
```

Enter fullscreen mode Exit fullscreen mode

**splice()**
*splice()*函数在数组中间添加或删除一个元素。
在数组中间添加或删除元素需要以下参数。

*   起始索引(要开始添加元素的位置)。
*   要删除的元素数(添加元素时为 0)。
*   要添加到数组中的元素。

```
var array = [10,20,30,70,80,90];
var newArray = [40,50,60]; 
array.splice(3,0,newArray); 
console.log(array); //10,20,30,[40,50,60],70,80,90 
```

Enter fullscreen mode Exit fullscreen mode

传递给 *splice()* 函数的元素可以是一个列表，不一定是数组的集合。

```
var array = [10,20,30,70,80,90];
array.splice(3,0,40,50,60);
console.log(array); //10,20,30,40,50,60,70,80,90 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个使用 *splice()* 从数组中移除元素的例子:

```
 var intro = ["Hello", "Callme", "John", "Samuel"];
var removeintro = intro.splice(1,2);
console.log(removeintro);//Callme,John 
```

Enter fullscreen mode Exit fullscreen mode

**reverse()**
*reverse()*函数反转数组元素的顺序。

```
var array = [1,2,3,4,5,6,7,8,9];
var reverseArray = array.reverse();
console.log(reverseArray);//9,8,7,6,5,4,3,2,1 
```

Enter fullscreen mode Exit fullscreen mode

**sort()**
*sort()*函数按字典顺序对数组的元素进行排序:关于[按字典顺序的更多信息](https://en.m.wikipedia.org/wiki/Lexicographical_order)

```
var names = ["Williams","Joe","John","Samuel","Somto","Ezeh"]; 
names.sort(); 
console.log(names);// Ezeh,Joe,John,Samuel,Somto,Williams 
```

Enter fullscreen mode Exit fullscreen mode

耶！一切正常，现在让我们看看排序函数是如何处理数字的。

```
var numbers = [1,2,3,4,50,60,70,80,5,6,7];
numbers.sort();
console.log(numbers);//1,2,3,4,5,50,6,60,7,70,80 
```

Enter fullscreen mode Exit fullscreen mode

哎哟...出乎我们的意料。别担心，我们可以通过向 *sort()* 函数传递一个函数来解决这个问题。

```
function compare(num1, num2) {
   return num1 - num2;
 }
var nums = [1,2,3,4,50,60,70,80,5,6,7]; 
var sorted = nums.sort(compare);
console.log(sorted)// 1,2,3,4,5,6,7,50,60,70,80 
```

Enter fullscreen mode Exit fullscreen mode

是啊！它工作了。

#### 数据结构&算法基础部分【1】即将面世...