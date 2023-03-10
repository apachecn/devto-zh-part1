# 参与一些 JavaScript 战争

> 原文：<https://dev.to/maxwell_dev/solving-some-javascript-warfare>

我对 JavaScript 的学习非常模糊。我知道大部分的基本知识，可以做一些简单的跑步，但是还没有想出下一步应该重点做什么。框架？ES2016？节点？我离基本面越远，就越难决定走哪条路。

当我不断决定的时候，我发现了一个保持我写作敏锐的资源- [CodeWars](https://www.codewars.com/) ！

它让我使用这个温和的点击诱饵标题(我有点抱歉)，并继续建立我的基本 JS 技能。它以一个基本前提为中心:获得大量不同的代码困境来解决，并将你的答案与其他人进行比较。这对我的写作风格和解决问题也很有帮助。

### 一个 JavaScript 战役的例子

到目前为止，我解决的最棘手的 CodeWars 挑战之一是:

> 鲍勃正准备通过智商测试。这项测试中最常见的任务是找出给定数字中的哪一个与其他数字不同。鲍勃观察到一个数字通常在均匀度上不同于其他数字。帮助 Bob——为了检查他的答案，他需要一个程序在给定的数字中找到一个均匀度不同的数字，并返回这个数字的位置。(请记住，你的任务是帮助鲍勃解决一个真正的智商测试，这意味着元素的指数从 1 开始，而不是从 0 开始。)

你有几个测试来检查它的工作，但我增加了一些额外的安全措施。

```
Test.assertEquals(iqTest("2 4 7 8 10"), 3);
Test.assertEquals(iqTest("3 5 7 10 11"), 4);
Test.assertEquals(iqTest("1 2 4"), 1);
Test.assertEquals(iqTest("2 1 3"), 1);
Test.assertEquals(iqTest("2 4 8 1"), 4);
Test.assertEquals(iqTest("1 3 5 2"), 4); 
```

Enter fullscreen mode Exit fullscreen mode

#### 第一步:找到需要的任务

对于像这样的问题，我喜欢将最终的任务分解成更简单的任务，解决这些问题，并一起使用这些小的解决方案。有两个简单的任务:

1.  检查一个数字是奇数还是偶数。
2.  找出一个数组是否只有一个奇数或偶数。

#### 第二步:解决需要的任务

我为每个任务编写了一个函数。第一个，检查一个数字是奇数还是偶数，很简单。

```
const checkStatus = num => (parseInt(num) % 2) ? 'odd' : 'even'; 
```

Enter fullscreen mode Exit fullscreen mode

第二个稍微难一点。它使用上面的函数使其可读性更好，但我可能会在以后重构它。

```
const findUniqueStatus = array => {
  let numEvens = 0;

  array.forEach(function(value){
    if (checkStatus(value) == 'even') { numEvens++; }
  });

  return (numEvens === 1) ? 'even' : 'odd'
} 
```

Enter fullscreen mode Exit fullscreen mode

维奥拉。两个任务都解决了。现在把它们放在一起。

#### 第三步:制作最终函数

现在我把两个函数都拿过来，放在一个单独的函数里，用它们来解智商测试。

```
function iqTest(numbers){
  const numArr = numbers.split('  ');

  const checkStatus = num => (parseInt(num) % 2) ? 'odd' : 'even';

  const findUniqueStatus = array => {
    let numEvens = 0;

    array.forEach(function(value){
      if (checkStatus(value) == 'even') { numEvens++; }
    });

    return (numEvens === 1) ? 'even' : 'odd'
  }

  let statuses = numArr.map(checkStatus),
      uniqueStatus = findUniqueStatus(numArr);

  return statuses.indexOf(uniqueStatus) + 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

最终结果比较长，但是很容易得到。阅读它将带您完成以下基本步骤:

1.  `numArr`是从中获取唯一状态的数字数组。
2.  `checkStatus`表示一个数是奇数还是偶数。
3.  从数组中获取唯一的状态。
4.  `statuses`是来自初始数组的偶数/奇数状态。
5.  `uniqueStatus`是来自初始数组的唯一状态。
6.  该函数返回唯一状态在非数字数组中的位置
7.  结果:告诉您原始数组中唯一值的索引。

从纯粹实用的角度来看，这个解决方案可能更有效。有人只用四行就解决了。

```
function iqTest(numbers){
  var nums = numbers.split("  ").map(x => x % 2);  
  var sum = nums.reduce((a,b) => a + b);  
  var target = sum > 1 ? 0 : 1;

  return nums.indexOf(target) + 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

但我会用我最喜欢的编码语录来回答这个问题:

> “任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”*~马丁·福勒*

所以期待更多的编码挑战！当我在 JavaScript 的荒野中不断寻找自己的路时，它们很好地保持了正轨。