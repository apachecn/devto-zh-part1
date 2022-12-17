# Javascript 中 Async/Await 的神奇神秘指南

> 原文：<https://dev.to/satansdeer/magical-mystery-guide-for-asyncawait-in-javascript-kke>

你好。今天我们将看看**异步**和**等待**关键字，它们允许你暂停函数的执行，因此让你写**异步**代码，读起来像**同步**。

但是首先让我们看看 Javascript 中处理异步的其他方法。你知道，只是为了让你**欣赏**async/await 如何让你写更多可读的异步代码。

## 首先我们进行了复试

想象一下，我们有一些**magical world DAPI**，我们需要得到一些虚构世界的英雄的任务列表。

对于回调，它看起来有点像这样。

```
getWorld(function(err, world){
  if (err) {
    // handle error; 
    return
  }
  getHero(world.heroId, function(err, hero){
    if (err) {
      //handle error; 
      return
    }
    getQuests(hero.questsIds, function(err, quests){
      if (err) {
        //handle error; 
        return
      }
      console.log(quests);
    }
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

看起来不太好，对吧？大量的嵌套，而且你必须在每次回调中单独处理错误，这很容易出错。您可能会在处理完一个错误后忘记添加 return 语句，或者犯另一个愚蠢的错误。

我们能改善这一点吗？

## 是的，言而有信

让我们想象一下，我们的 **MagicalWorldAPI** 被更新，现在它返回`Promise`对象。让我们适应它。

```
getWorld().then(function(world){
  return getHero(world.heroId)
}).then(function(hero){
  return getQuests(hero.questsIds)  
}).then(function(quests){
  console.log(quests)  
}).catch(function(err){
  //handle error
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在`catch`函数中有了一个回调函数，在这里我们可以处理来自这个链的任何部分的错误。好多了，但是代码还是很难读。如果我们能让它看起来同步就好了…

## 异步/等待？还没有，发电机

```
const co = require('co')

co(function* (){
  var world = yield getWorld();
  var hero = yield getHero(world.heroId);
  var quests = yield getQuests(hero.questsIds);
  console.log(quests);
}).catch(function(err){
  //handle error 
}) 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们使用虚构的 **API** 的部分现在看起来很好，但是其他代码很神秘！函数声明中的那个`*`是做什么的，那些`yield`语句是什么？

`function`语句后的星号使其创建一个**生成器函数**，并且`yield`关键字暂停**生成器函数**的执行，跟随`yield`关键字的表达式的值被返回给生成器的调用者。

而`co`是一个很好的函数，可以解析一个**生成器函数**并返回一个承诺。

所以在这个例子中，`getWorld()`返回一个承诺。然后`yield`暂停进一步执行我们的星形符号函数，并将`getWorld()`的结果传递给`co`函数。`co`函数接受承诺，解析它并将值传递回生成器函数，在那里它被赋给`world`变量。

然后对其他变量重复同样的操作。

## 最后，我们开始异步/等待

让我们再次重写我们的代码。

```
async function(){
  try{
    var world = await getWorld();
    var hero = await getHero(world.heroId);
    var quests = await getQuests(hero.questsIds);
    console.log(quests);
  }
  catch(err){
    //handle error
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来很眼熟，对吧？我们只是把`yield`改成了`await`，而不是`fuction*`，我们现在有了`async function`语句，这里我们不使用`co`函数。

哦，还有一件事，我们现在使用`try/catch`来处理错误。这很好，因为我们现在可以用同样的方式处理同步和异步代码错误。

那么这里发生了什么？

`async function`语句定义了一个**异步函数**。当一个**异步函数**被调用时，它返回一个承诺。当**异步函数**返回值时，将用返回值解析`Promise`。当**异步函数**抛出异常时，`Promise`将被拒绝。

另外，**异步函数**可以包含一个`await`表达式，它暂停异步函数的执行并等待传递的承诺的解析，然后恢复**异步函数的**执行并返回解析后的值。

执行流程将与前面的示例非常相似。当我们偶然发现第一个`await`语句时，我们的**异步函数**将暂停，直到`getWorld()`承诺被解析。然后**异步函数**将被解除暂停，解析后的值将被赋给`world`变量。

然后对其他变量重复同样的操作。

## 总结

今天我们学习了使用`async`语句可以创建**异步函数**。

在该函数中，您可以在返回`Promise`的表达式前面使用`await`语句。

当**异步函数**将被执行时，它将在`await`语句所在的地方暂停，直到`Promise`被解析。

我们还了解到，使用`async/await`你可以通过给它一个更同步的流程来简化异步代码的阅读。