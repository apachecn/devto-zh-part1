# 把这个解释成我五岁

> 原文：<https://dev.to/lamaalrajih/explain-this-like-im-five-4ea0>

所以，我做 freeCodeCamp 已经有一段时间了，我需要对以下内容的解释:

```
function largestOfFour(arr) {
  var results = [];
  for (var n = 0; n < arr.length; n++) {
    var largestNumber = arr[n][0];
    for (var sb = 1; sb < arr[n].length; sb++) {
      if (arr[n][sb] > largestNumber) {
        largestNumber = arr[n][sb];
      }
    }

    results[n] = largestNumber;
  }

  return results;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 FCC 提供的初学者解决方案，我写了正确的解决方案，但我的代码是错误的，我不明白为什么需要 var 结果或 var largestNumber。为什么我不能原样返回 arr[n][sb]呢？