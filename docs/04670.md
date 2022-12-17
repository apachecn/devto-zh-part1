# 基本的普通 JavaScript 函数

> 原文：<https://dev.to/amit_merchant/essential-vanilla-javascript-functions>

# 基本的普通 JavaScript 函数

> 一些普通形式的 JavaScript 中缺失的函数(灵感来自 [PHP](https://en.wikipedia.org/wiki/PHP) )。你可以把它当作编程练习来尝试。

# 数组函数

## `array_unique()`

> 从数组中删除重复项

```
function array_unique(arr){
    var seen = {};
    var ret_arr = [];
    var key;
    var i;

    function keyify(obj){
        var ret = "";
        var j;

        if (Object.prototype.toString.call(obj) === "[object Object]" || Object.prototype.toString.call(obj) === "[object Array]"){
            for (j in obj){
                ret += "~" + j + "^" + keyify(obj[j]) + "%";
            }
            return ret;
        }else{
          return obj;
        }
    }

    for(i = 0; i < arr.length; i++){
        key = keyify(arr[i]);
        if(!(key in seen)){
            ret_arr.push(arr[i]);
            seen[key] = true;
        }
    }

    return ret_arr;
}

array_unique([4,5,4,6,7,8,2,6]);
// [4, 5, 6, 7, 8, 2]
array_unique([{a: 'val'}, {b: 'val', c: 'val'}, 'a', 'b', [1,2,3,4], {a: 'val'}, [1,2,3,4], [{a: 'val'}, {b: 'val'}], [{a: 'val'}, {b: 'val'}]]);
// [{a: 'val'}, {b: 'val', c: 'val'}, 'a', 'b', [1,2,3,4], [{a: 'val'}, {b: 'val'}]] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_merge()`

> 合并两个数组

```
function array_merge(arr1, arr2){
    for(var i=0; i<arr2.length; i++){
        arr1.push(arr2[i]);
    }

    return arr1;
}

array_merge([1, 2, 3], [4, 5]);
// [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_chunk()`

> 将一个数组分割成数组块

```
function array_chunk(arr, count){
    var temp_arr = [];

    for(var i=0; i<arr.length;){
        var chunk_arr = [];
        for(var j=0; j<count; j++){
            if(!arr[i])
                break;
            chunk_arr.push(arr[i]);
            i++;
        }
        temp_arr.push(chunk_arr);
    }

    return temp_arr;
}

array_chunk([1,2,3,4,5,6,7,8,9], 4);
// [ [ 1, 2, 3, 4 ], [ 5, 6, 7, 8 ], [ 9 ] ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_collapse()`

> 将数组集合折叠成一个平面数组

```
 function array_collapse(...arrays){
    var collapse_arr = [];

    for(var i=0; i<arrays.length;i++){
        for(var j=0; j<arrays[i].length; j++){
            collapse_arr.push(arrays[i][j]);
        }
    }

    return collapse_arr;
}

array_collapse([1, 2, 3, 4], [5, 6], ["hello", "world"]);
// [ 1, 2, 3, 4, 5, 6, 'hello', 'world' ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_diff()`

> 返回`arr1`中不存在于`arr2`
> 中的值

```
function array_diff(arr1, arr2){
    var temp_arr = [];

    for(var i=0; i<arr1.length; i++){
        if(arr2.indexOf(arr1[i]) == -1){  
            temp_arr.push(arr1[i]);
        }
    }

    return temp_arr;
}

array_diff([4,5,6,7, "unicorn"], [5, 6, 7]);
// [ 4, 'unicorn' ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_intersect()`

> 返回两个提供的数组中的公共值

```
function array_intersect(arr1, arr2){
    var temp_arr = [];

    for(var i=0; i<arr1.length; i++){
        if(arr2.indexOf(arr1[i]) != -1){  
            temp_arr.push(arr1[i]);
        }
    }

    return temp_arr;
}

array_intersect([4,5,6,7, "unicorn"], [5, 6, 7, 8]);
// [ 5, 6, 7 ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_map()`

> 将数组中的每个值发送到用户自定义函数，该函数返回新值

```
function array_map(arr, func){
    var temp_arr = [];

    if(typeof func !== "function")
        throw "Second parameter should be a function";

    for(var i=0; i<arr.length; i++){
        temp_arr.push(func(arr[i]));
    }

    return temp_arr;
}

array_map([1, 2, 3, 4, 5], function (value) {
    return value * 2;
});
// [ 2, 4, 6, 8, 10 ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_reject()`

> 使用给定的回调函数过滤数组。如果该项应该从结果数组
> 中移除，回调应该返回`true`

```
function array_reject(arr, func){
    var temp_arr = [];

    if(typeof func !== "function")
        throw "Second parameter should be a function";

    for(var i=0; i<arr.length; i++){
        if(func(arr[i]))
            temp_arr.push(arr[i]);
    }

    return temp_arr;
}

array_reject([1, 2, 3, 4, 5], function (value) {
    return value > 3;
});
// [ 4, 5 ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_split()`

> 将数组分成给定数量的组

```
function array_split(arr, count){
    var temp_arr = [];
    var arr_length = arr.length;

    var chunk = Math.floor(arr_length/count);

    for(var i=0; i<arr.length;){
        var chunk_arr = [];

        if(temp_arr.length == (count-1))
            chunk = chunk + (arr_length-i);

        for(var j=0; j<chunk; j++){
            if(!arr[i])
                break;
            chunk_arr.push(arr[i]);
            i++;
        }

        temp_arr.push(chunk_arr);
    }

    return temp_arr;
}

array_split([1,2,3,4,5,6,7,8,9], 4);
// [ [ 1, 2 ], [ 3, 4 ], [ 5, 6 ], [ 7, 8, 9 ] ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_take()`

> 返回一个具有指定项数的新数组

```
function array_take(arr, count){
    var temp_arr = [];

    if(count<0){
        count = Math.abs(count);
        for(var i=(arr.length-count); i<arr.length; i++){
            temp_arr.push(arr[i]);
        }
    }else{
        for(var i=0; i<count; i++){
            temp_arr.push(arr[i]);
        }
    }

    return temp_arr;
}

array_take([1,2,3,4,5,6,7,8,9], 4);
// [ 1, 2, 3, 4 ] 
```

Enter fullscreen mode Exit fullscreen mode

> 您也可以传递一个负整数来从数组末尾获取指定数量的项目:

```
array_take([1,2,3,4,5,6,7,8,9], -3);
// [ 7, 8, 9 ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array_pad()`

> 将具有指定值的指定数量的项目插入到数组

```
 function array_pad(arr, size, value){
    for(var i=0; i<size; i++){
        arr.push(value);
    }
    return arr;
}

array_pad([1,2,3,4], 2, "unicorn");
// [ 1, 2, 3, 4, 'unicorn', 'unicorn' ] 
```

Enter fullscreen mode Exit fullscreen mode

## `range()`

> 创建一个包含一系列元素的数组

```
function range(start, end){
    var temp_arr = [];

    for(var i=start; i<=end; i++){
        temp_arr.push(i);
    }

    return temp_arr;
}

range(5, 11);
// [ 5, 6, 7, 8, 9, 10, 11 ] 
```

Enter fullscreen mode Exit fullscreen mode

# 字符串函数

## `chunk_split()`

> 将字符串分割成一系列更小的部分

```
function chunk_split(string, length, end){
    var temp_string = '';

    for(var i=0; i<string.length; i++){
        temp_string += string[i];
        if((i+1)%length==0)
            temp_string += end;
    }

    return temp_string;
}

console.log(chunk_split("Hello", 1 , "."));
// H.e.l.l.o.
console.log(chunk_split("Hello", 1 , ".."));
// H..e..l..l..o.. 
```

Enter fullscreen mode Exit fullscreen mode

## `str_pad()`

> 将字符串填充到新长度

```
function str_pad(string, size, value){
    for(var i=0; i<size; i++){
        string += value;
    }

    return string;
}

str_pad("unicorn", 5 , ".");
// unicorn..... 
```

Enter fullscreen mode Exit fullscreen mode

## `strrev()`

> 反转一个字符串

```
function strrev(string){
    var temp_string = '';

    for(var i=string.length-1; i>=0; i--){
        temp_string += string[i];
    }

    return temp_string;
}

strrev("unicorn");
// nrocinu 
```

Enter fullscreen mode Exit fullscreen mode

## `similar_text()`

> 计算两个字符串之间的相似度

```
function similar_text(string1, string2){
    var seen = {};
    var similar_count = 0;

    for(var i=0; i<string1.length; i++){
        if((string2.indexOf(string1[i]) !== -1 && !(string1[i] in seen)) 
                || string1[i]=='  ')
        {
            similar_count++;
            if(string1[i]!='')
                seen[string1[i]] = true;
        }
    }

    return similar_count;
}

similar_text("Hello World","Hello Peter");
// 6 
```

Enter fullscreen mode Exit fullscreen mode

最初发布于[Amit merchant 1990/essential-vanilla-JavaScript-functions](https://github.com/amitmerchant1990/essential-vanilla-javascript-functions)。