# 探索 Javascript 中的合成

> 原文：<https://dev.to/damcosset/exploring-composition-in-javascript>

## 简介

插入另一个关于函数式编程的介绍...

## 作文

组合就是创建小功能，并用它们创建更大更完整的功能。把一个功能想象成一块砖，组合就是你如何让这些砖一起工作来建造一堵墙或一栋房子。

你可能遇到过数学作文，写得像这样:f(g(x))。函数 **f** 是由 x 的函数 **g** 或者 **g** 后的 **f** 等于 x 的 **g** 的**f**后的*组成的，因为我们是从右到左，从内到外对函数求值:*

`f <-- g <-- x`

前一个函数的输出成为下一个函数的输入。 **x** 是 **g** 的输入。 **g(x)** 的输出成为 **f** 的输入。

## 例子？

好吧，那我们写点什么吧。假设你是一家负责处理文本的公司。你收到一堆单词，而你的客户希望以某种方式收回它们。

一个客户给你发短信说:

> 我希望短于 5 个字符的单词大写。

我们创建了三个函数来执行这些操作。一个函数获取文本并返回小写单词。第二个函数查找短单词并使用大写字母。最后，第三个函数从接收到的数组中重新创建文本。

```
function words( text ){
  return String( text )
    .toLowerCase()
    .split(/\s/)
}

function shortUpper( words ){
  return words.map( word => {
    if( word.length < 5 ){
      return word.toUpperCase()
    } else {
      return word
    }
  })
}

function createText( array ){
  return array.join('  ')
} 
```

Enter fullscreen mode Exit fullscreen mode

客户端发送文本，我们让我们的函数工作:

```
const text = 'Hello! My name is Damien and I love Javascript. Make this exciting.'

const allWords = words(text)
const upperText = shortUpper( allWords )
const newText = createText( upperText )

//hello! MY NAME IS damien AND I LOVE javascript. MAKE THIS exciting. 
```

Enter fullscreen mode Exit fullscreen mode

太好了！客户得到了他想要的。问题是:我们的工作人员必须手动获取**单词**和 **shortUpper** 函数的输出，将它们带到下一个函数，并打开该函数的引擎。工作量很大，我们能自动化吗？

*提示戏剧性的音乐*

### 输入作文

我们希望函数的输出被发送到下一个函数，而不需要我们自己去做。像这样:

```
const newText = createText( shortUpper( words( text ) ) )

//hello! MY NAME IS damien AND I LOVE javascript. MAKE THIS exciting. 
```

Enter fullscreen mode Exit fullscreen mode

我们从左到右阅读，但是，正如我前面提到的，我们从内到外执行:

`createText <-- shortUpper <-- words <-- text`

我们甚至决定为这个流行的需求创建一个函数:

```
function upperLessThan5( text ){
  return createText( shortUpper( words( text ) ) )
}

upperLessThan5( text )
//hello! MY NAME IS damien AND I LOVE javascript. MAKE THIS exciting. 
```

Enter fullscreen mode Exit fullscreen mode

我们公司还有一个流行的需求:替换“.”由！！!'同时使每个单词的第一个字符大写。我们有一些函数来处理这个问题:

```
function exclamationMarks( words ){
  return words.map( word => word.replace('.', '!!!'))
}

function upperFirstChar( words ){
  return words.map( word => {
    return `${word[0].toUpperCase()}${word.substr(1)}`
  })
}

function firstCharUpperExclamation( text ){
  return createText( exclamationMarks( upperFirstChar( words( text ) ) ) )
}

firstCharUpperExclamation( text )
//Hello! My Name Is Damien And I Love Javascript!!! Make This Exciting!!! 
```

Enter fullscreen mode Exit fullscreen mode

酷，我们可以通过组合几个更小的函数来组合函数！

## 要疯了！

该公司的首席执行官非常高兴。由于排版，工厂转换文本的速度非常快。但是他想要更多！

> 如果我们有一个函数，它把所有的函数都作为输入，只让构图自己发生，会怎么样？我们可以称之为**作曲**。

工程师们聚在一起集思广益。他们决定用现有的两种产品进行试验。他们想出了这个:

```
function composeThreeFunctions(fn3, fn2, fn1){
  return function composed( firstValue ){
    return fn3( fn2( fn1( firstValue ) ) )
  }
}

function composeFourFunctions(fn4, fn3, fn2, fn1){
  return function composed( firstValue ){
    return fn4( fn3( fn2( fn1( firstValue ) ) ) )
  }
}

const upperLessThan5 = composeThreeFunctions( createText, shortUpper, words )
upperLessThan5( text )

//hello! MY NAME IS damien AND I LOVE javascript. MAKE THIS exciting.

const exclamationFirstCharUpper = composeFourFunctions( createText, upperFirstChar, exclamationMarks, words)

exclamationFirstCharUpper( text )

//Hello! My Name Is Damien And I Love Javascript!!! Make This Exciting!!! 
```

Enter fullscreen mode Exit fullscreen mode

我们的函数将所有需要的函数作为参数。它返回一个以原始值作为参数的函数，并返回所有按正确顺序组合的函数。注意秩序！我们从内的**执行到**外的**。您指定的最后一个函数将首先执行。函数如何记住参数中指定的所有函数？[关闭！！！](https://dev.to/damcosset/explaining-closures-to-myself)！**

现在，我们可以用三个或四个函数任意组合。但是首席执行官想要通用的东西。

> 如果我们只需要组合两个函数会怎么样？还是五个？十个？我不希望我的工厂里有无限多的功能。你能创建一个函数，只接受任意数量的函数并组合吗？

最后，工程师们想出了用**合成**的函数:

```
function compose( ...fns ){
  return function composed( value ) {
    let listOfFunctions = fns.slice()
    while( listOfFunctions.length > 0 ){
      value = listOfFunctions.pop()( value )
    }

    return value
  }
}

const upperLessThan5 = compose( createText, shortUpper, words )
upperLessThan5( text )

//hello! MY NAME IS damien AND I LOVE javascript. MAKE THIS exciting.

const exclamationFirstCharUpper = compose( createText, upperFirstChar, exclamationMarks, words )
exclamationFirstCharUpper( text )

//Hello! My Name Is Damien And I Love Javascript!!! Make This Exciting!!! 
```

Enter fullscreen mode Exit fullscreen mode

*compose* 函数将函数列表作为参数。我们使用 rest 操作符(...)将它聚集成一个数组。我们以原始值作为参数返回一个函数。在这个函数内部，我们创建了一个函数数组的本地副本(如何创建？[closuuuure](https://dev.to/damcosset/explaining-closures-to-myself)。然后我们用最后一个函数的输出调用数组的最后一个函数。 *pop()* 返回数组的最后一个元素，并将其从数组中移除。最后一个 listOfFunctions 元素的输出成为下一个元素的输入。当数组为空时，我们返回最终值。

我是说，这太棒了。现在我们可以疯狂了。

### 恐鸟的例子！！！

我现在只是随便玩玩。但是天空是极限。

```
const upperLessThan5Exclamation = compose( createText, shortUpper, exclamationMarks, words )
// OOOOOORRRR
const exclamationUpperLessThan5 = compose( createText, exclamationMarks, shortUpper, words )

// Same thing, different order

upperLessThan5Exclamation( text )
exclamationUpperLessThan5( text )
//hello! MY NAME IS damien AND I LOVE javascript!!! MAKE THIS exciting!!!
//hello! MY NAME IS damien AND I LOVE javascript!!! MAKE THIS exciting!!!

function replaceAbyE( words ){
  return words.map( word => {
    return word.replace(/a/gi, 'e')
  })
}

function replaceJavascriptWithPython( words ){
  return words.map( word => {
    if( word == /javascript/i ){
      return 'Python'
    } else {
      return word
    }
  })
}

const everything = compose( createText, shortUpper, exclamationMarks, upperFirstChar, replaceAbyE, replaceJavascriptWithPython, words )

everything( text )
//Hello! MY NEME IS Demien END I LOVE Python MEKE THIS Exciting!!! 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我就说到这里。我想看看像 Ramda 这样的 librairies 是如何实现组合的，但这确实是一种有趣的编写代码的方式。我的实现当然只是一种可能。你可以创造一个不同的。您可以实现管道功能(从右到左)...我可能会在另一篇文章中探讨这个问题。

爱情！