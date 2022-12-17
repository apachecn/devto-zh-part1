# JavaScript 中的 Includes()与 indexOf()

> 原文：<https://dev.to/adroitcoder/includes-vs-indexof-in-javascript>

ES2016 规范包括数组数据结构的`includes()`方法。`includes()`方法检查数组是否包含某个元素，根据情况返回`true`或`false`。
但是在 ES5 中，我们习惯于用`indexOf()`方法来执行这样的操作。

使用`includes()`方法。

```
const array = [1,2,3,4,5,6];

if(array.includes(4) ){
console.log("true 4 was found in the array")// true 4 was found in the array
} 
```

让我们用`indexOf()`方法进行同样的操作。

```
const array = [1,2,3,4,5,6];

if(array.indexOf(4) > -1 ){
console.log("true 4 was found in the array")// true 4 was found in the array
} 
```

使用`includes()`方法检查`NaN`

```
Â constÂ  array = [NaN];

if (array.includes(NaN)){
console.log("true. NAN was found in the array");// true. NAN was found in the array
} 
```

这就是用`indexOf()`方法开始分崩离析的地方。

```
const  array = [NaN];
if (array.indexOf(NaN) == -1){
    console.log("NaN not found in the array");//NaN not found in the array
} 
```

使用`includes()`方法检查`undefined`。

```
const array = [, , , ,];

if(array.includes(undefined)){
console.log("true array elements are undefined");// true array elements are undefined
} 
```

让我们看看`indexOf()`方法将如何处理这个操作。

```
const array = [, , , ,];

if(!array.indexOf(undefined) == -1 ){
console.log("true. array elements are undefined");
}else {
console.log("Sorry can't find undefined");// Sorry can't find undefined
} 
```

`includes()`方法不区分-0 和+0

```
const a = [-0].includes(+0);
console.log(a);//true 
```

类型化数组也有一个方法`includes()`

```
let array = Uint8Array.of(2,6,4);
console.log(array.includes(4));//true 
```

### 总结

*   includes 方法发现 *NaN* 和 *undefined* ，而 indexOf 方法没有。
*   includes()方法不区分-0 和+0(这不是一个错误，但清楚地表明了 javascript 是如何工作的。[检查 javascript 数字类型](http://speakingjs.com/es5/ch11.html#two_zeros)
*   从 [MDN 阅读更多关于 Array.prototype.includes()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)