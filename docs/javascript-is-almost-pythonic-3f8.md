# JavaScript 几乎是 pythonic 式的

> 原文：<https://dev.to/massa142/javascript-is-almost-pythonic-3f8>

## 多行字符串

*   Python3.6

```
print("""string text line 1
string text line 2""") 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
console.log(`string text line 1
string text line 2`) 
```

Enter fullscreen mode Exit fullscreen mode

## 表达式插值

*   Python3.6

```
a = 5
b = 10
print(f'Fifteen is {a + b} and not {2 * a + b}.') 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
var a = 5
var b = 10
console.log(`Fifteen is ${a + b} and not ${2 * a + b}.`) 
```

Enter fullscreen mode Exit fullscreen mode

## 箭头功能

*   Python3.6

```
numbers = [1, 2, 3, 4]

list(map(lambda x: x * 2, numbers))
# or [x * 2 for x in numbers] 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
var numbers = [1, 2, 3, 4]
numbers.map(v => v * 2) 
```

Enter fullscreen mode Exit fullscreen mode

## 解构

*   Python3.6

```
numbers = (1, 2, 3)
x, y, z = numbers 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
var numbers = [1, 2, 3]
var [x, y, z] = numbers 
```

Enter fullscreen mode Exit fullscreen mode

## 传播算子

*   Python3.6

```
import datetime
date_fields = (2017, 12, 4)
date = datetime.date(*date_fields)

numbers = [1, 2, 3, 4]
first, *remaining = numbers

first = [1, 2]
second = [3, 4]
combined = first + second 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
var dateFields = [2017, 12, 4]
var date = new Date(...dateFields)

var numbers = [1, 2, 3, 4]
var [first, ...remaining] = numbers

var first = [1, 2]
var second = [3, 4]
var combined = [...first, ...second] 
```

Enter fullscreen mode Exit fullscreen mode

## 休息符

*   Python3.6

```
from functools import reduce
def product(*numbers):
    return reduce(lambda x, y: x * y, numbers)

print(product(1, 2, 3, 4)) 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
function product(...numbers) {
    return numbers.reduce((x, y) => x * y)
}
console.log(product(1, 2, 3, 4)) 
```

Enter fullscreen mode Exit fullscreen mode

## 默认参数

*   Python3.6

```
def multiply(a, b=1):
    return a * b 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
function multiply(a, b = 1) {
  return a * b
} 
```

Enter fullscreen mode Exit fullscreen mode

## 类

*   Python3.6

```
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    def __str__(self):
        return f"({self.x}, {self.y})" 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
class Point {
    constructor(x, y) {
        this.x = x
        this.y = y
    }
    toString() {
        return `(${this.x}, ${this.y})`
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 子类

*   Python3.6

```
class ColorPoint(Point):
    def __init__(self, x, y, color):
        super().__init__(x, y)
        self.color = color
    def __str__(self):
        return "{} in color {}".format(super().__str__(), self.color) 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
class ColorPoint extends Point {
    constructor(x, y, color) {
        super(x, y)
        this.color = color
    }
    toString() {
        return `${super.toString()} in ${this.color}`
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## Getter & Setter

*   Python3.6

```
class SmartPoint(Point):
    @property
    def hypotenuse(self):
        return sqrt(self.x ** 2 + self.y ** 2)

    @hypotenuse.setter
    def hypotenuse(self, z):
        self.y = sqrt(z ** 2 - self.x ** 2) 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
class SmartPoint extends Point {
    get hypotenuse() {
        return Math.sqrt(this.x ** 2 + this.y ** 2)
    }
    set hypotenuse(z) {
        this.y = Math.sqrt(z ** 2 - this.x ** 2)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 模块

*   Python3.6

```
import math
print(math.log(42))

from math import log
print(log(42))

from math import *
print(log(42)) 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
import math from 'math'
console.log(math.log(42))

import { log } from 'math'
console.log(log(42))

import * from 'math'
console.log(log(42)) 
```

Enter fullscreen mode Exit fullscreen mode

## 异步功能

*   Python3.6

```
async def getProcessedData(url):
    try:
        v = await downloadData(url)
    except Exception:
        v = await downloadFallbackData(url)
    await processDataInWorker(v) 
```

Enter fullscreen mode Exit fullscreen mode

*   现在是 2017 年

```
async function getProcessedData(url) {
  let v
  try {
    v = await downloadData(url) 
  } catch (e) {
    v = await downloadFallbackData(url)
  }
  return processDataInWorker(v)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 引用

*   [JS 正在变成大蟒蛇](http://treyhunner.com/javascript-is-like-python/#/)
*   [JavaScript 承诺相当于 Python 的 asyncio](https://quentin.pradet.me/blog/javascript-promises-are-equivalent-to-pythons-asyncio.html)
*   [JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript)