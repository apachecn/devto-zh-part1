# 实践中的 RxJS:如何用 streams 做一个 typeahead！

> 原文：<https://dev.to/sammyisa/rxjs-in-practice-how-to-make-a-typeahead-with-streams>

## 我们将做什么

你知道当你在 Twitter 搜索栏中输入内容，它会试图猜测你想搜索什么吗？假设你开始输入“SammyI”，第一个结果是我的 twitter 句柄， [@SammyIs_Me](https://twitter.com/SammyIs_Me) 。

这就是我们要做的(除了可能不适合 Twitter)。

#### 但首先，看家

[上次](https://dev.to/sammyisa/what-is-rxjs-when-seen-from-a-mile-away)我们谈到了流以及我们在这些流上所做的操作，但是我没有使用正确的术语。这些溪流被称为可观测的，我将在未来这样称呼它们。

## 起始代码

让我们跳过基本的 HTML，就做这样的:

```
<input type="text" 
       name="typeahead" 
       class="typeaheadInput" /> 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意，你可以添加一些 CSS 来使它看起来更好。接下来，开始的 Javascript——一个~~流~~ observable，它在输入文本框每次改变时发送新数据，以及一个将输入记录到控制台的函数:

```
const inputStream$ = Rx.Observable
        .fromEvent(input, 'input')
        .map(e => e.target.value);

inputStream$.subscribe(text => console.log(text)); 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至“净化”可观察的事物，只从事件中获得有用的数据。

## 获取搜索数据

为了获得搜索/建议数据，我们将使用 [Datamuse API](https://api.datamuse.com) 。我们将使用建议端点来获得一些单词建议，比如:

```
GET https://api.datamuse.com/sug?s=sammy
Response:
[{"word":"sammy","score":35841},
{"word":"sammy sosa","score":35639}, 
... ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们把这个要求加到我们的`subscribe`中，我们的可观察量，我们有:

```
inputStream$.subscribe(text => {
    fetch(`https://api.datamuse.com/sug?s=${text}`)
    .then( resp => resp.json() )
    .then( resp => console.log(resp) )
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们向控制台显示来自 API 的所有建议的数组。我们还没有完成，但你可以从这里看到最终产品！

## 使搜索数据也成为可观察的

我们不断地从 datamuse 获得数据流，难道我们不能让另一个数据流被消费吗？是的，我们可以！

本节中有一些新的、重要的概念需要处理，所以在继续之前，请确保您已经很好地掌握了这些概念。

首先，我们不希望在每一个单独的冲程中都达到 datamuse 端点*。如果我们这样做了，我们将得到关于`h`、`he`、`hel`、`hell`、`hello`的建议，我们只需要关于`hello`的建议。*

所以，我们将**去抖**可观测的。“去抖”的意思是“一直等到我们在 x 毫秒内没有收到新的事件，然后获取最新的事件，这就是新的可观察事件”。因此，在我们之前的例子中，在我们停止输入一秒钟后，只有`hello`会被发送到可观察对象。试试看，把之前的`inputStream$`可观测值:

```
const inputStream$ = Rx.Observable
        .fromEvent(input, 'input')
        .map(e => e.target.value)
        .debounceTime(2000); 
```

Enter fullscreen mode Exit fullscreen mode

在输入框中键入，然后等待两秒钟。盯着控制台。

让搜索结果成为新的可观察对象！

```
const suggestionsStream$ = inputStream$
    //Fetch the data
    .mergeMap( text => fetch(`https://api.datamuse.com/sug?s=${text}`) )
    //Get the response body as a json
    .mergeMap( resp => resp.json() )
    //Get the data we want from the body
    .map( wordList => wordList.map(item => item.word) ); 
```

Enter fullscreen mode Exit fullscreen mode

我保证我会很快进入，但首先我必须请你相信它。如果你在处理一个承诺，用`mergeMap`代替`map`
现在我们有了一个给我们一系列建议的可观察对象，我们把这个数组放在某个地方。

由于这比我预期的要长一些，我们将在 div 中列出这些建议:

```
//We made a div of class 'suggestion' for this
const suggestions = document.querySelector('.suggestions');
suggestionsStream$.subscribe(words => {
    suggestions.innerText = words.join('\n');
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试一下吧！输入一些东西，等两秒钟，看看结果！

## 最终代码

```
<input type="text" 
    name="typeahead" 
    class="typeaheadInput" />
<div class="suggestions"></div>
<script>
//Get the suggestions div
const suggestions = document.querySelector('.suggestions');
//Get the input component
const input = document.querySelector('.typeaheadInput');

//Input stream
const inputStream$ = Rx.Observable
                .fromEvent(input, 'input')
                .map(e => e.target.value)
                .debounceTime(2000);

//Suggestions stream
const suggestionsStream$ = inputStream$
                .mergeMap( text => fetch(`https://api.datamuse.com/sug?s=${text}`) )
                .mergeMap( resp => resp.json() )
                .map( body => body.map(item => item.word) )

//Handle the stream
suggestionsStream$.subscribe(words => {
    suggestions.innerText = words.join('\n');
});
</script> 
```

Enter fullscreen mode Exit fullscreen mode

下一次我们将解释什么是`mergeMap`(可能更简短，它不仅仅是一个承诺处理器！)而且我们会和 RxJS 一起潜入动画！如果您有任何问题/更正/建议/赞美，您可以通过 Twitter [@SammyIs_Me](https://twitter.com/SammyIs_Me) 联系我。