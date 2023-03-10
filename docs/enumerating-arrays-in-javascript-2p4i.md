# 在 JavaScript 中枚举数组

> 原文：<https://dev.to/incessantmeraki/enumerating-arrays-in-javascript-2p4i>

枚举是以某种方式从数组中排列或选择元素的过程。它们也被称为排列组合。让我们看看在 JavaScript 中枚举数组的几种方法

## 列举所有排列

假设有 **k** 个位置要被填充，并且在一个数组中有 **n** 个元素可以填充这些位置。每个位置可以从 **n** 个元素中取任意值。然后，还有**n<sup>k</sup>T9】不同的补位方式。让我们看看评估这种枚举结果的方法。** 

```
 var enumAll = require('enum-all')

  var arr = ['a','b','c','1','2','3']

  //Generate all three letter password using characters from arr

  var output = enumAll(arr, 3)
  console.log(output) //['aaa', 'baa', 'caa' .... 213 more items] 
```

Enter fullscreen mode Exit fullscreen mode

## 枚举力量集合

幂集是包含数组中所有可能元素子集的集合。也就是说，给定一个包含 **n** 个元素的数组，其幂集包含各种大小元素的所有可能组合。幂集的大小是**2<sup>n</sup>T5】让我们来看看计算数组中元素幂集的方法。** 

```
 var powerSet = require('enum-powerset')

  var arr = ['Alice', 'Bob', 'Eve']

  //generate powerset of above elements
  var output = powerSet(arr)

  // Prints the powerset of three elements in arr
  console.log(output) 

  // [ [''],
  //   ['Alice'],
  //   ['Bob'],
  //   ['Alice,Bob'],
  //   ['Eve'],
  //   ['Alice,Eve]',
  //   ['Bob,Eve'],
  //   ['Alice,Bob,Eve' ]
  // ] 
```

Enter fullscreen mode Exit fullscreen mode

## 列举某种组合

幂集包含各种大小元素的所有可能组合。假设您只想从 **n** 个元素中得到大小为 **k** 的组合，那么这种枚举就派上了用场。从等于 n 的 n 个元素中选择 k 个元素总共有**NC<sub>k</sub>T7】种方法！/(k！*(n-k)！)哪里**！**指阶乘。让我们来看一个例子。** 

```
 var nck = require('enum-nck')

  var teams =['united', 'city', 'liverpool']

  //generate all possible football matches between above teams
  var output = nck(teams, 2)

  //prints the result
  console.log(output)
  //[ [ 'united', 'city' ],
  //  [ 'united', 'liverpool' ],
  //  [ 'city', 'liverpool' ], 
  //] 
```

Enter fullscreen mode Exit fullscreen mode

## 枚举排列

排列是一种评估数组中所有不同元素排列方式的方法。还有 **n！(factorial)** 排列元素的方式，如果每个元素都是唯一的。让我们看看元素排列的求值方法。

```
 var permutate = require('enum-permutate')

  var arr = ['0','1','2']

  //generate permutation of above elements
  var output = permutate(arr)

  //prints output
  console.log(output)
  //[ [ '0', '1', '2' ],
  //  [ '0', '2', '1' ],
  //  [ '1', '0', '2' ],
  //  [ '1', '2', '0' ],
  //  [ '2', '0', '1' ],
  //  [ '2', '1', '0' ] 
  //] 
```

Enter fullscreen mode Exit fullscreen mode

上面所有的例子都可以在 node.js 和 browser(通过 bundler，比如 browserify)中运行。