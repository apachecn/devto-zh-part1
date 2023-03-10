# Rx 实用介绍

> 原文：<https://dev.to/jonstodle/a-practical-intro-to-rx-b9g>

如果你试图用反应式扩展和可观察编程开始编程，你会感到不知所措。你试图开始用一种新的方式思考，在某些情况下，与你开始编程以来所做的完全相反。

在这个介绍中，我会尽我所能帮助你获得正确的心态。为此，我会尽量避免使用你经常看到的 Rx 上的花哨词汇。我会避开数学理论和研究论文中的术语，尽可能使用更多的常用短语。

在这个介绍中，我将使用 JavaScript(和一些 HTML)和 RxJS。目前，我认为所有程序员都接触过 JavaScript，这使它或多或少成为一种通用语言。如果这会影响到你，你可以试着眯着眼睛假装这是 Java。

现在，让我们开始学习:

# 好的、旧的、有规律的事件

在 Rx 中，一切都从*可观察的*开始。然而，我们将从事件开始。更具体地说，单击事件。点击按钮上的事件实际上是这样的:

在 [CodePen](https://codepen.io) 上看乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 01](https://codepen.io/jonstodle/pen/wqoKvz/) 。

这个按钮目前没有为我们做任何事情，但是它做了一些事情:它生成事件。每当你点击按钮时，就会触发一个`click`事件。我们还没有开始监听这些事件，所以当这些事件发生时，我们什么也不做，但它们仍在发生。

即使没有人在场听到森林中一棵树倒下，它仍然会发出声音。按钮也是一样:它会产生点击事件，即使没有人在听。

我们开始听吧。使用这个按钮，我向 click 事件添加了一个简单的事件监听器:

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 02](https://codepen.io/jonstodle/pen/VzmvjZ/) 。

事件侦听器可能接收 1 个、2 个或多个事件。它甚至可能根本不接收任何事件。用户可能永远不会点击按钮。但是当用户*点击*按钮时，我们有一些如何处理的说明。

我们现在有了一个按钮，它是事件的*源，一个事件监听器，它让我们*得到那些事件*的通知，还有一个处理函数，它让我们*对新事件*做出反应。*

这是一些基本的东西，你可能比你的手背更清楚这一点，但现在对于情节的转折:一个可观察的是信号的*源，一个观察者让我们*得到这些信号的通知*，一个下一个处理者让我们*对新信号做出反应*。*

让我们仔细看看可观测量。

# 可观测量

我喜欢把一个被观测者描述为信号的生产者。信号只是由可观测物体发出的某种东西的通称。正如 DOM 元素有不同种类的事件一样，observable 也有不同种类的信号。

## 信号

所有的信号都带有一个值。你真正感兴趣的是它的价值。正如一个事件包含一个带有事件细节的值一样，一个信号包含一个带有某种数据的值。数据的种类取决于值的来源。

我在这个介绍中使用术语信号，但你可能在其他地方看到它们被称为*值*或*事件*。我更喜欢用信号这个通称来描述任何一种可观测的输出，并且只使用*下一个信号*的值。

### 下一个信号

*下一个信号*是您使用的最常见的信号。它被称为下一个信号，或者简称为*下一个*，因为它代表下一个要处理的值。

这些信号包含您想要的实际数据。对于基于事件的可观测量，这是事件数据；对于基于数组的可观察值，这是数组的值；而对于基于`Promise` s 的可观测量，这是承诺的结果。

每当一个可观测值有了新值，它就会产生下一个信号。

### 错误信号

*错误信号*仅在出错时产生。如果某种异常发生在一个可观测的对象上，就会产生一个错误信号。

错误信号的值是抛出的异常。

### 完成信号

当被观测者知道它不会再产生下一个信号时，就产生了*完整信号*。

例如，如果可观察对象基于一个数组，并且它已经为数组中的每个元素产生了下一个信号，那么它将产生一个完整的信号来通知您它已经完成了。

完整信号的值是`void`，意味着它没有值。完整的信号没有附加任何数据。

## 创造一个可观察的

让可观察对象产生信号的值的来源可能会有所不同，但你不必知道来源的细节。可观察对象是在价值的实际来源之上的一般抽象。

这意味着您能够以通用的方式处理来自不同来源的信号，这也是 Rx 如此强大的部分原因。来自一系列不同来源的信号在通过可观测对象传递时，看起来和表现都是一样的。

这有点像你如何迭代一个数组，不管它有什么样的内容。数组是对一组值的简单抽象，它为您提供了一组通用的方法来处理数组中的项。

虽然可以用`create`工厂方法手动创建 observables，但我认为这应该保留为中级主题。不是因为它非常困难，而是因为有更容易(并且不容易出错)的方法来创建可观测量。

虽然可观察对象可以是其自身的价值来源，但产生信号的常见方式是将现有的价值来源转换为可观察对象。每当一个新的值从源中获得，可观测的将为我们产生一个新的 next 信号。为了将值的来源转换成可观察值，我们使用了一系列的`from*()`方法。

我们要看的第一个`from*()`工厂方法是`fromEvent()`。这将事件从事件源转换成可观察的信号。你指定事件的来源和你想听的事件，`fromEvent()`会将这些事件转换成信号。

下面是它的使用方法:

```
Observable.fromEvent(button, "click"); 
```

这大致相当于这样:

```
button.addEventListener("click"); 
```

注意`addEventListener()`中缺少一个处理函数。这是因为它完全反映了对`fromEvent()`的调用。我们只是宣布，我们有兴趣知道什么时候有新的信号或事件，而不是当我们得到那个信号或事件时该做什么。

我们已经知道如何给`addEventListener()`添加一个处理程序，所以我们将看看如何为 observables 添加处理程序。这就是我们接触观察者的地方。

# 观察者

你很少直接和观察者一起工作，所以我不会说太多细节。您需要知道的是，观察器是三种处理程序的容器:

## 下一个()

当一个可观察对象发出下一个信号时，它是在通知我们，它有一个我们想要的新值。我们接收下一个信号的值作为处理程序中的参数。我们通过调用我们创建的可观察对象上的`subscribe()`来添加下一个信号的处理程序，并传入处理程序:

```
Observable.fromEvent(button, "click").subscribe(event => {  
    count++;
    output.innerText = "You clicked " + count + " times!";
  }); 
```

我们把这个叫做*订阅*；你现在已经订阅了可观察的。上面的代码实际上和这个一样:

```
button.addEventListener("click", event => {  
    count++;
    output.innerText = "You clicked " + count + " times!";
  }); 
```

## 错误()

当可观察对象发出错误信号时，一定是出了什么问题。我们通过添加一个处理程序作为`subscribe()`的第二个参数来处理这个问题。我们接收错误信号的值作为处理程序中的一个参数。:

```
Observable.fromEvent(button, "click").subscribe(event => {  
    count++;
    output.innerText = "You clicked " + count + " times!";
  },
  error => console.log("Something bad happened: " + error); 
```

上面的代码有点像这样:

```
try {  
  button.addEventListener("click", event => {
      count++;
      output.innerText = "You clicked " + count + " times!";
    });
} catch(error) {
  console.log("Something bad happened: " + error);
} 
```

## 完成()

当一个可观察对象产生下一个信号时，它将发送完整的信号。我们可以通过添加一个处理程序作为第三个参数来处理这个问题。因为 complete 的值是`void`，所以处理程序不接受任何参数:

```
Observable.fromEvent(button, "click").subscribe(event => {  
    count++;
    output.innerText = "You clicked " + count + " times!";
  },
  error => console.log("Something bad happened: " + error),
  () => console.log("I'm done.")); 
```

# 可观察的保证

当创造一个可观察的，任何可观察的，你会得到一些保证。这些保证与可观察对象的行为有关，可观察对象将*始终*遵守这些保证。这使得可观测量的工作更加容易和可预测。

## 1。一个可观测值将产生零个或多个 next 信号

正如我在事件部分提到的:当添加事件监听器时，您可能不会收到任何事件，您只是在监听一个或多个事件被触发的情况。

同样的道理也适用于 observables:订阅时你可能收不到任何 next 信号。你只是在陈述你有兴趣被告知是否有。在大多数情况下，您会期望收到至少一个值，但这并不保证。

您可能收不到下一个信号是有原因的。这些原因会在接下来的规则中提到。

## 2。如果可观察对象内部出现错误，将会发送一个错误信号，并且不会再发送其他信号

当一个可观察对象内部出现问题时，会发送一个*错误信号*。这包括当我们将一个值的来源转换成一个可观察值时，以及使用操作符时(我们将在后面讨论)。

误差信号的值是在可观测值内部产生的误差。到目前为止，在我们的例子中，我们只看了不会导致错误发生的简单可观察值。如果我们在一个可观察对象内部进行 web 调用，这个 web 调用可能会出错。在这种情况下，我们会收到一个包含适当错误的错误信号。

## 3。当可观察对象不再产生下一个信号时，它将发送一个完整的信号，并且不再发送信号

当你订阅时，你不知道你将会收到多少下一个信号(见保证#1)。可观察信号会告诉你它是否知道不会再有下一个信号产生。然后它将发送一个*完成信号*。

有可能从一个数组中创建一个可观察的。当可观察对象为数组中的每个项目产生下一个信号时，它会发送一个完整的信号通知订阅者它已经完成了值的产生。

在事件可观测的情况下，你永远不会收到完整的信号。事件永远不会真正完成；可能总会有更多的。由于可观测值可能产生更多的值，它也不会发出一个完整的信号。

# 订阅可观测量

请记住，一个可观测的只是一个产生信号的源的抽象。在这种情况下，我们已经看到了如何将事件源转换为可观察对象，但是正如前面提到的,`from*()`方法家族可以将许多不同的东西转换为可观察对象。

*   **`fromPromise()`** 将一个 JavaScript 承诺转化为可观察的。
*   **`from()`** 取一个数组，或者一个类似数组的对象，把每一项转换成结果可观测的下一个信号。

因为所有这些都创造了可观的。这意味着你也可以用普通的方式处理下一个信号。让我们将之前的处理程序提取到一个单独的函数中，并更改输出文本:

```
function handleNext(nextValue){  
  count++;
  output.innerText = "You have received " + count + " next signals!";
} 
```

我还修改了输出，使其更加通用。然后我们可以将它传递给`subscribe()`方法:

```
Observable.fromEvent(button, "click").subscribe(handleNext); 
```

这将产生相同的行为。我们现在也能够为其他可观测量重用该处理程序。让我们从数组中创建一个可观察值:

```
const numbers = [1, 2, 3, 4, 5];

Observable.from(numbers); 
```

我们可以把这个和我们的按钮处理程序和之前的
放在一起

```
Observable.fromEvent(button, "click").subscribe(handleNext);

Observable.from(numbers).subscribe(handleNext); 
```

这是结果:

在 [CodePen](https://codepen.io) 上看乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 03](https://codepen.io/jonstodle/pen/LjbpbK/) 。

你应该看到“你已经收到了 5 个下一个信号！”立刻。当将`numbers`数组转换为可观测值时，会立即产生下一个信号。该方法接收数组，遍历它，一次一个项目，并为每个项目产生下一个信号。对于每个下一个信号，调用`handleNext()`。

如果单击按钮，计数将继续，因为按钮上的单击事件产生的下一个信号将使用相同的处理程序。

# 冷热可观

这让我们很好地理解了冷热可观测量的概念。你会看到这个提到了很多地方，尤其是有人问有什么区别。我希望你，以你到目前为止所创造的不同的可观测性(事件可观测性和阵列可观测性)的知识，将掌握这个概念。

默认情况下，可观察对象是冷的。这意味着在您对它调用`subscribe()`之前，它不会产生任何值。就像一个常规函数在你调用它之前不会返回值一样，一个可观察的函数在你订阅它之前不会产生信号。

这就是阵列观测器的工作原理。当我们第一次调用`Observable.from(numbers)`时，没有发送下一个信号。我们只是为以后的使用准备可观测的。当我们在可观察对象上调用`subscribe()`时，它开始行动，并开始为数组中的每个项目产生下一个信号:

```
const numbersObservable = Observable.from(numbers); // No next signal is produced  
numbersObservable.subscribe(handleNext); // Next signals start being produced 
```

另一方面，一个热可观察对象，不管你是否调用`subscribe()`都会产生值。例如，这就是事件的工作方式。正如我前面提到的，仅仅因为你没有监听按钮上的点击事件，并不意味着它们没有被触发。

当我们调用`Observable.fromEvent(button, "click")`时，每次点击按钮都会产生下一个信号。当我们订阅它时，我们将接收从该点向前产生的所有 next 信号。在该时刻之前产生的所有后续信号都将丢失。这就像事件一样。

简而言之，冷可观测量就像常规函数，只有在被调用时才会产生值:在`subscribe()`被调用之前，它们不会产生信号。

热点观察就像事件，不管有没有人在听，它都会产生价值:从它们产生的那一刻起，它们就会产生下一个信号，不管你是否呼叫`subscribe()`。

虽然可观测物*在默认情况下应该是冷的，但你有时仍然会看到热的可观测物。

当把一个来源的价值转化为一个可观察的价值时，这往往是真实的。例如`fromEvent()`和`fromPromise()`就是这种情况。*

Rx 的一个缺点是，无法通过查看声明来判断一个可观察对象是热的还是冷的。你要么必须知道天气是冷还是热(正如我们所知道的`fromEven()`可观测量)，要么你必须订阅它，看看会发生什么。

这听起来像是一个主要的缺点，有些人说确实如此，但是在 95%的情况下，结果是可以预测的。只是要准备好偶尔被一个热门的可观察到的事物惊到。

# 订阅

当订阅一个可观察对象时，`subscribe()`将返回一个代表该订阅的令牌。这个令牌只有一个方法可以调用:`unsubscribe()`。

> **注:** `unsubscribe()`在 RxJS 4 和 Rx 的其他实现中称为`dispose()`。

通过呼叫`unsubscribe()`,你告诉被观察对象你对新的信号不再感兴趣。这将使可观察的关闭和清理。如果可观察对象已经分配了对象，它们将被标记为清理，异步操作将被中止。

`unsubscribe()`不会使可观察物产生完整的信号。完整的信号是可观察的方式告诉订户它已经产生了新的价值；`unsubscribe()`是订户告诉可观察对象它不再对*任何*信号感兴趣。

任何时候你知道你不再对任何信号感兴趣，你应该总是取消订阅。这确保了可观察对象不会在后台做任何你不感兴趣的工作(潜在的繁重工作)。

## 自动清理

当一个可观察物体完成了它的工作，并发出一个完整的信号时，它也会自我清理。也就是说如果收到完整的信号，就不用打`unsubscribe()`；同样的清理程序由可观察对象自动完成。

如果一个错误发生在一个可观察对象(或者一个操作符)内部，并且它产生了一个错误信号，那么这个可观察对象会像处理一个完整的信号一样自行清除。

# 运算符

运营商可能是入门 Rx 时最恐怖、最让人应接不暇的科目。仅 RxJS 就有超过 60 家运营商！刚开始的时候有很多东西要消化。我在这里告诉你两件事:

首先，你不必记住所有的运营商来开始使用 Rx。事实上，你只需要关心一两个。以后，当您需要或想要时，您可以通过寻找新的操作符来进行扩展。

第二，我将一遍又一遍地查看我看到的常用运算符。这些是你开始使用 Rx 时最可能需要的。

在我们进入操作符之前，我将解释它们是如何工作的:

到目前为止，我们只直接订阅了可观测数据，并处理了不同的信号。算符让我们在订阅它之前修改它。我们可以过滤掉我们不感兴趣的信号，或者在下一个处理程序接收下一个信号之前改变它的值。

官方对操作符的描述更专业一些，但是我喜欢用两种思维模式来看待操作符。下面我就两个都来解释一下。我希望你忽略操作符本身，只关注解释。后面将详细解释操作符实际做什么。

### *修改后的可观测*模型

这种心理模型将一个可观察对象及其所有操作者视为一个整体。最后，你只剩下一个你认同的可观察对象。

考虑一个基于按钮点击事件的可观察值:

```
const buttonClicks = Observable.fromEvent(button, "click"); 
```

我们要修改由可观测产生的下一个信号值。我们添加一个运算符:

```
const buttonClicks = Observable.fromEvent(button, "click")  
  .map(myMappingFunction); // Don't worry about what this means. We'll get back to it 
```

`buttonClicks`仍然是一个可观测值，运算符返回应用了运算符的原始可观测值。对订户来说，它看起来仍然像是一个没有附加算子的可观察对象；它所看到的只是某种可观察的东西。

当点击按钮时，观察对象仍会产生信号。这一点没有改变。改变的是下一个信号的值。

订阅可观察对象和之前一模一样:

```
buttonClicks.subscribe(handleNext); 
```

我们可以给可观察对象添加更多的操作符来进一步修改它:

```
const buttonClicks = Observable.fromEvent(button, "click")  
  .map(myMappingFunction) // Again, ignore all the operators, we'll get back to all of this afterwords
  .filter(myFilteringFunction)
  .mergeMap(myMergeMappingFunction)
  .take(myTakeValue); 
```

仍只是可观察到的。你可以像平常一样订阅它。它产生的信号发生了很大的变化，但可观测的仍然只是可观测的。

正如我前面提到的:*可观察的是在价值的实际来源之上的一般抽象*。除了抽象出值的来源，可观察对象还可以在将值发送给`subscribe()`之前修改它们。

操作符让你定制可观察对象应该如何修改值。

### *管道*模型

这个心智模型将可观察对象视为价值的来源，将操作者视为管道，将订阅者视为处理者。

让我们来看看与之前相同的可观测值:

```
const buttonClicks = Observable.fromEvent(button, "click"); 
```

当使用操作符时，通常的做法是将每个操作符写在新的一行上，对齐点。虽然这有助于可读性(特别是对于长操作符链)，但它也为这个心智模型创建了一个很好的视觉效果。

运算符可应用于任何可观察值，因此让我们展示在`subscribe()`中接收下一个信号值之前改变它们的另一种方法。

```
buttonClicks  
  .map(myMappingFunction)
  .subscribe(handleNext); 
```

管道模型是这样看的:

1.  `buttonClicks`产生一个值
2.  该值被传递到管道中的下一步:`map()`
3.  `map()`改变数值
4.  `map()`将值向下传递到管道中的下一步:`subscribe()`
5.  `subscribe()`处理数值

在这个模型中，价值从顶部开始向下传递，一次一步。每一步都是一个操作符。操作符执行一些操作并传递值。在底部，值由`subscribe()`处理。

管道模型可能是操作员工作方式最常见的心理模型。在谈论 Rx 时，你会经常看到提到“管道”。不过，我认为有些人可能会更好地理解修正后的可观测模型。

如何选择去思考，完全取决于你自己；你觉得怎样都行。

## 操作员解释

大多数运算符将函数作为第一个参数。该函数接收一个值作为参数并返回一个值。用代码来说，这是您传递给操作符的函数:

```
function(inValue) {  
  // Do some work
  return newValue;
}

// OR

inValue => {  
  // Do some work
  return newValue;
} 
```

你接收到的`inValue`是下一个信号的值。当你把一个操作符添加到一个可观察对象上时，下一个信号的值将依次传递给每个操作符。

在每个操作符中(除了一些例外)，你提供了一个函数，它接受下一个信号的值，进行某种运算或评估，并返回一个新值。然后，新值被传递给管道中的下一个操作符。

为了了解这是如何工作的，让我们来看一个操作符以及如何使用它。

### 地图

map 运算符采用一个函数，该函数接收一个值并返回一个值。您可以使用此运算符来转换值。您接收的值是下一个信号的值，并且您返回的值通常基于接收的值。

让我们看一些代码。这是我们的老歌`buttonClicks` :

```
const buttonClicks = Observable.fromEvent(button, "click"); 
```

基于事件的可观察值的下一个信号值是包含事件信息的对象。

让我们添加这段代码来测试一下:

```
buttonClicks.subscribe(value => output.innerText += value + "\n"); 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 04](https://codepen.io/jonstodle/pen/mMOeWV/) 。

在事件对象上有一个名为`target`的属性，它引用了触发事件的 DOM 对象。我们希望将可观察对象的下一个信号值改为触发事件的 DOM 对象。为此，我们使用了`map`操作符:

```
.map(event => { return event.target;Â }) 
```

我们从可观察对象接收下一个信号值，并简单地返回事件的`target`属性。让我们来测试一下:

在 [CodePen](https://codepen.io) 上看乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 05](https://codepen.io/jonstodle/pen/prNjea/) 。

还是；这不是很有趣的信息。让我们从 DOM 对象中获取按钮的文本:

```
.map(event => event.target.innerText) 
```

在 [CodePen](https://codepen.io) 上看乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 06](https://codepen.io/jonstodle/pen/Ojbymb/) 。

我们可以在`map`操作符中做任何我们想做的事情，只要我们返回一个值。例如，我们可以拆分按钮中的文本，并返回一个包含不同单词长度的数组:

```
.map(event => {
  const text = event.target.innerText;
  const textParts = text.split(" ");
  let returnArray = [];

  for(let i = 0; i < textParts.length; i++) {
    returnArray.push(textParts[i].length);
  }

  return returnArray;
}) 
```

在 [CodePen](https://codepen.io) 上看乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 07](https://codepen.io/jonstodle/pen/prNjPa/) 。

我们甚至可以完全忽略我们收到的价值，返回我们喜欢的任何东西

```
.map(_ => "Something completely different!") 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 08](https://codepen.io/jonstodle/pen/GvNpMq/) 。

注意，我们将收到的值赋给了`_`。这是函数式编程中的常见做法，表示我们不关心值，也不打算使用它。像这样使用时，下划线通常被称为*丢弃*。

### 滤镜

`filter`操作符接受一个值并返回一个布尔值。如果函数返回 true，传入的值将被传递给链中的下一个操作符。如果函数返回 false，传入的值将**而不是**传递给链中的下一个操作符。

你可以把`filter`想象成一个`if`语句。如果为真，继续；如果为假，则停止。

为了解释，我们将重复使用我们刚刚学过的`buttonClicks`和`map`操作符:我们将每次点击映射到自 epoch(1970 年 1 月 1 日)以来的当前毫秒数，然后过滤，只让具有偶数值的信号通过:

```
.map(_ => Date.now())
.filter(milliseconds => milliseconds % 2 == 0) 
```

在 [CodePen](https://codepen.io) 上看乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 09](https://codepen.io/jonstodle/pen/vJyrGa/) 。

当您单击该按钮时，只有在您碰巧单击了自 1970 年 1 月 1 日以来的偶数毫秒数时，您才会获得一个新值。在获得新值之前，您可能需要单击几次。

和`map`一样，`filter`可以简单也可以复杂。

### 组合测试

为了配合`combineLatest`，我们将引入一个新的可观察值:

```
const ticks = Observable.interval(1000); 
```

这个可观测值将每 1000 毫秒(例如每秒)产生一个新的 next 信号。第一个信号的值为`0`，第二个信号的值为`1`，第三个信号的值为`2`，依此类推。

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 10](https://codepen.io/jonstodle/pen/RZoJqO/) 。

使用`combineLatest`,您可以合并两个可观察值。每次两个原始可观测值中的任何一个产生信号时，`combineLatest`后产生的可观测值都会产生一个新信号。来自`combineLatest`的下一个信号的值是一个数组，包含每个原始可观察值的最近值:

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 11](https://codepen.io/jonstodle/pen/yoVEmX/) 。

如您所见，`combineLatest`不会产生任何值，直到每个原始观察值都产生了至少一个值。只有当您单击按钮时，您才会开始看到一个值。无论你是否点击按钮，`ticks`可观察值一直在产生新的值，这意味着你看到的第一个值可能不是`1,0`，而是两个可观察值产生的最新值。

`combineLatest`也支持映射函数作为第二个参数。该函数将接收两个参数；数组中的两个值。然后我们可以将这两个值转换成更有用的东西:

```
.combineLatest(ticks, (clickCount, tickCount) => `${clickCount} + ${tickCount} = ${clickCount + tickCount}`) 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 12](https://codepen.io/jonstodle/pen/rzWojX/) 。

值得注意的是，最好将映射函数作为参数提供给`combineLatest`，而不是在它后面使用一个`map()`。这样做对性能有一点好处。而且，我认为它更好地表达了你的意图。

### 合并

将两个可观测信号合并成一个可观测信号。`merge`算子后的结果可观测量将产生两个原始可观测量产生的相同的 next 信号，同时原始可观测量产生它们。

让我们看看这在实践中是如何工作的。这里，我们将`ticks`和`buttonClicks`结合起来，创建一个新的可观测值。这个新的可观测值将产生与两个原始可观测值相同的值:

```
.merge(ticks) 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 13](https://codepen.io/jonstodle/pen/yoVGrB/) 。

当`ticks`产生一个新值时，它将被显示。当`buttonClicks`产生一个新值时，它将被显示。

### 合并地图

这个运算符实际上是一个`map`和一个`merge`的组合，因此得名`mergeMap`。这个操作符以前被调用过，并且在 Rx，`flatMap`的其他语言实现中仍然被调用。

传递给这个操作符的函数是一个映射函数。你接收下一个信号值作为参数，并返回一个可观察值。你返回的可观测值将被合并到原始可观测值中。

对于您在`mergeMap`中收到的每个下一个信号值，您将产生一个新的可观察值。这个新的可观测值将产生全新的下一个信号值。当所有返回到`mergeMap`内部的可观测量产生下一个信号时，在`mergeMap`之后产生的可观测量将产生下一个信号。

让我们去掉`ticks`可观测值，只关注`buttonClicks`可观测值，它就是`map` :

```
buttonClicks  
  .map(_ => {
    count++;
    return count;
  }) 
```

这个可观察值将产生值`1`、`2`、`3`等。每点击一次按钮。

接下来，我们将基于由`buttonClicks.map()`可观察值:
产生的值创建一个新的区间可观察值

```
.mergeMap(clickCount => // 1
          Observable.interval(clickCount * 1000) // 2
          .map(value => `${clickCount * 1000}-interval has ticked ${value + 1} times`)) // 3 
```

让我们看一下这段代码:

1.  我们从`buttonClicks.map()`可观察对象接收点击计数。
2.  我们创建一个可观察的间隔，它将每隔等于`clickCount`的秒数滴答一次(如果`clickCount`是`3`，它将每 3 秒滴答一次)。
3.  然后，我们将区间可观测的下一个信号值更改为一个字符串，告诉我们特定的区间可观测值已经勾选了多少次(记住，区间可观测值从`0`开始，这就是为什么我们必须添加`1` : `value + 1`)。

然后这个`Observable.interval().map()`可观测值将被合并到原始可观测值中——原始可观测值就是`buttonClicks.map()`可观测值。

同样，对于每一次按钮点击，`count`增加`1`并传递给`mergeMap`。`mergeMap`然后产生一个`Observable.interval().map()`可观测信号，它将每隔等于`clickCount`的秒数产生一个新的 next 信号。然后`Observable.interval().map()`可观测值被合并到`buttonClicks`可观测值中。

所有曾经在`mergeMap`内部产生的`Observable.interval().map()`可观测物(作为来自`buttonClicks`的下一个信号的结果)将被合并在一起。每当一个合并的可观测量产生下一个信号时，在`mergeMap`之后产生的可观测量将产生下一个信号。

我故意在这里重复我自己很多次。掌握`mergeMap`是 Rx 编程中非常重要的一部分。如果你自己想出了使用`mergeMap`的解决方案，你可能已经开始最终“明白”了。

`mergeMap`是最难“搞定”的操作员之一。部分原因是有时很难理解这些“高阶”操作符，但也是因为在学习 Rx 的早期，你总是会遇到这种情况。

还有更多类似于`mergeMap`的操作符，但是在你开始“获取”Rx 之前，你通常不会开始使用它们。

你自己已经看完了整个`mergeMap`部分，我鼓励你再看一遍，看看第二遍你是否理解得更好。了解你现在所知道的，可能会帮助你更好地理解第一篇课文。

### [交换机映射](#switchmap)

`switchMap`与`mergeMap`非常相似:它接受一个函数，该函数接收下一个信号值作为参数，并返回一个可观察值。

不同的是，`switchMap`不是合并它内部产生的所有可观测量，而是只在最近的可观测量返回产生信号时产生下一个信号。第二个最近的可观察值将被取消订阅并被丢弃。

如果我们看和处理`mergeMap`一样的代码，但是去掉`mergeMap` :

```
.switchMap(clickCount => 
          Observable.interval(clickCount * 1000)
          .map(value => `${clickCount * 1000}-interval has ticked ${value + 1} times`)) 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 15](https://codepen.io/jonstodle/pen/ZJBgYV/) 。

`switchMap`之后的可观测值只有在它内部返回的最近可观测值产生下一个信号时才会产生下一个信号。这与每次*返回的所有*观察值产生下一个信号时`mergeMap`产生下一个信号形成对比。

换句话说:每次你点击按钮，`switchMap`会对它返回的前一个可观察对象调用`unsubscribe()`，只有当新的可观察对象产生下一个信号时，才会产生下一个信号。

### 取

这个操作符将允许你限制一个可观察到的下一个信号的最大数量。如果你正在处理一个永远不会完成的可观察对象(如`Observable.fromEvent()`)，但你只想知道下一个信号的具体数目，这就很有用。

当达到极限时，`take`将取消订阅它所调用的可观察对象，但`subscribe()`将收到来自`take`的完整信号。

假设我们想在按钮被点击时开始一个可观察的间隔，并隐藏按钮。我们只对按钮发出的*下一个*信号感兴趣，所以我们用`take`来限制它。这将确保在我们不再需要时清理对`buttonClicks`的订阅。

然后我们使用一个`switchMap`来订阅单个可观测区间。即使`take`将导致`buttonClicks`发送一个完整的信号，我们返回到`switchMap`内部的可观察对象将继续产生值，因此保持可观察对象的活性:

```
buttonClicks  
  .take(1)
  .switchMap(_ => {
    button.style.opacity = 0;
    return Observable.interval(1000);
  }) 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 16](https://codepen.io/jonstodle/pen/ZJLENq/) 。

### 接住

如果一个异常在 observable 内部被引发，那么您将会在您的`subscribe()`内部的错误处理程序中得到通知。有时，虽然您更希望下一个信号具有空值或默认值。

接受一个接收异常的函数，并返回一个可观察值。返回的可观测值将**取代**原来的可观测值。

最初的可观测信号发出了一个错误信号，这意味着它将无法再发送任何信号。`catch`将停止错误信号并将其传递给函数。然后，它将返回的可观察对象合并到原始可观察对象中。在 subscribe 中，看起来像是原始的可观察对象停止产生下一个信号，而返回的可观察对象接管了。

为了解释`catch`，我们将使用可靠的旧`buttonClicks`和`Observable.from()` :

```
const buttonClicks = Observable.fromEvent(button, "click");  
const numbers = Observable.from([1, 2, 3, 4]); 
```

然后我们将在`map` :
中故意引发一个异常

```
.map(_ => {
  count++;

  if(count === 3) {
    throw "Count is 3!";
  }
  return count;
})
.catch(error => numbers) 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 17](https://codepen.io/jonstodle/pen/oeBXZE/) 。

当我们第三次单击该按钮时，会引发一个异常。`catch`将停止错误信号，并用可观测的`numbers`代替。

您会注意到，在引发异常后，按钮停止工作。这是因为`buttonClicks`产生了一个错误信号。这与可观测性的保证是一致的:当一个误差信号产生时，将不会产生进一步的信号。

这很少是你想要的，所以你要做的是在一个`mergeMap`或`switchMap`中对你返回的可观察对象使用`catch`。当返回的可观测值引发异常时，`catch`将阻止错误信号合并到原始可观测值中，而是合并返回到`catch`中的可观测值。

在下面的例子中，我们返回一个在`switchMap`中可观察到的区间。可观察到的间隔产生的值的数量与点击按钮的数量相同。如果按钮被点击三次，可观察到的间隔产生三个值。

如果可观测的区间产生四个值(`0`、`1`、`2`、`3`)，`map`在看到`3`时会抛出异常。这使得区间可观测值发送一个错误信号，`catch`停止，并用`numbers`可观测值代替。

```
.switchMap(clickCount => 
          Observable.interval(100)
            .take(clickCount)
            .map(value => {
              if(value === 3) {
                throw "Value is 3!";
              }
              return value;
            })
            .catch(error => numbers)) 
```

参见 [CodePen](https://codepen.io) 上乔恩·斯特德尔([@乔恩·斯特德尔](https://codepen.io/jonstodle))的笔[介绍 Rx 18](https://codepen.io/jonstodle/pen/JyEYKm/) 。

# 结论

如果你已经通读了整篇文章:祝贺你！ðŸŽ‰

当你开始使用 Rx 的时候，阅读这一大堆文字是很困难的。我真诚地希望你已经对如何使用 Rx 有了一些了解。我不想变得太技术化，而是让你进入一种思维模式，在那里你可以开始思考如何在实践中使用 Rx。

这个介绍不会展示解决方案，但是它(希望)展示了如何使用 Rx 来达成解决方案。

我给你一个建议:

把整本书再读一遍。也许等几天——再读一遍。

这可能看起来有点消极，但是正如我在`mergeMap`一节中提到的:用你现在知道的新知识回过头来阅读第一部分，可能会帮助你更好地理解那些部分；这又导致了对最后部分的更好理解。

如果你还有任何问题，请在评论中或在推特上提问。

编码快乐！

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/a-practical-intro-to-rx/)T3】*