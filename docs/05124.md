# 理解可观测量

> 原文：<https://dev.to/jvanbruegge/understanding-observables>

反应式编程最近获得了很大的吸引力。像 RxJS 和 Most.js 这样的库和 Cycle.js 这样的框架使得编写复杂的异步行为变得很容易。但是理解那些可观察的事物或流(从现在开始我将使用两个可互换的术语)是如何工作的通常很难解释。以我的经验来看，如果你能自己建造一些东西，你就明白了。这就是为什么我们将在本文中构建一个玩具 RxJS！

# 我们想要达到什么目标

由于许多人不熟悉流，这里有一个简短的总结:流是随时间变化的数组。我的意思是:

```
const myArray = [1, 2, 3, 4];

const myValue = myArray
    .map(i => i * 2)
    .reduce((acc, curr) => acc + curr, 0);
console.log(myValue); 
```

在这段代码中，我们取了一个数组，并对其中的所有元素求和。但是，如果我们从外部来源获取值，比如从 API 获取值，会怎么样呢？然后我们可以使用承诺:

```
const myValuePromise = getData() //uses a promise based API
    .then(data => data
        .map(i => i*2)
        .reduce((acc, curr) => acc + curr, 0)
    )
    .then(console.log); 
```

这个也很好用。但是如果我们从 websocket 获取数据呢？一个 websocket 在未来不像承诺那样是单一的价值，而是很多的价值！这就是流变得有用的地方:

```
let websocket = new Websocket(/* ... */);
const websocketStream = Observable.create(observer => {
    websocket.onMessage = (msg) => observer.onNext(msg);
    websocket.onClose = () => observer.complete();

    return () => websocket.close();
});

const myValueStream = websocketStream
    .map(i => i * 2)
    .scan((acc, curr) => acc + curr, 0)
    .subscribe(console.log); 
```

现在，每当一个新值通过 websocket 到达时，`scan`就会发出新的总和。如果您想等到 websocket 关闭后再打印最终的总和，可以使用`reduce`。

# 建造玩具 RxJS

现在我们知道了如何使用流，是时候开始构建流库了。让我们先问我们，*什么* whe 我们希望*什么时候*发生。我们希望有一些*观察者*能够*订阅*一个*可观察的*。然后，观察器将接收来自上游的值。因此，简单地说，我们将首先定义我们的可观测性。这里我将使用 typescript，因为它有助于理解正在发生的事情。

```
interface Observer<T> {
    next(t: T): void;
    complete(): void;
} 
```

如您所见，观察者是一个具有`next`和`complete`功能的对象。现在我们需要可观察的。为此我们将自下而上开始，这意味着现在，我们的可观测值只需要一个`subscribe`方法。

```
interface Observable<T> {
    subscribe(observer: Observer<T>): void;
} 
```

天真地说，我们只用一个方法创建一个对象。让我们复制我们的 websocket 示例:

```
let websocket = new Websocket(/* ... */);
const websocketStream = {
    subscribe(observer) {
        websocket.onMessage = msg => observer.next(msg);
        websocket.onClose = () => observer.complete();
    }
} 
```

好了，这看起来就像真正的 RxJS 例子。唯一的区别是缺少清理，但为了简单起见，我不会涉及这一点。接下来，我们必须定义一个映射函数，它接受一个函数和一个可观察值，并返回一个新的值:

```
function map<T, U>(fn: (t: T) => U): (s: Observable<T>) => Observable<U> {
    return stream => ({
        subscribe(observer: Observer<U>) {
            stream.subscribe({
                next: (value: T) => observer.next(fn(value)),
                complete: observer.complete
            });
        }
    });
} 
```

我们基本上只是创建了一个工厂函数，该函数订阅了先前的观察值，内部观察器应用该函数并将值返回给下一个观察器。同样，Typescript 有助于理解正在发生的事情。

现在我们可以这样做(扩展前面的例子):

```
const myValueStream = map(i => i * 2)(websocketStream); 
```

虽然这是可行的，但它不是最漂亮的 API。我们习惯于在可观察对象上调用函数。幸运的是，这个问题很容易解决:

```
class Stream<T> implements Observable<T> {
    constructor(public subscribe: (o: Observer<T>) => void) {}

    public compose<U>(operator: (s: Stream<T>) => Stream<U>): Stream<U> {
        return operator(this);
    }

    public map<U>(fn: (t: T) => U): Stream<U> {
        return this.compose(map(fn));
    }
} 
```

现在我们有了一个 ES6 `class`，它得到了一个`subscribe`方法作为构造函数参数，并且在它的原型上有了`map`。这意味着我们的示例如下所示:

```
let websocket = new Websocket(/* ... */);
-const websocketStream = {
-    subscribe(observer) { +const websocketStream = new Stream(observer => {
        websocket.onMessage = msg => observer.next(msg);
        websocket.onClose = () => observer.complete();
    }
}

const myValueStream = websocketStream
    .map(i => i * 2); 
```

现在实现`scan`相当容易，所以我们将改为实现`reduce`,它等待最后一个值到达，然后发出一次结果:

```
function fold<T, U>(fn: (acc: U, curr: T) => U, seed: U): (s: Stream<T>) => Stream<U> {
    return stream => new Stream(observer => {
        let accumulator = seed;
        stream.subscribe({
            next: value => {
                accumulator = fn(accumulator, value);
            },
            complete: () => {
                observer.next(accumulator);
                observer.complete();
            }
        });
    });
} 
```

可以看到，我们有一个内部状态，它会根据来自前一个流的每个事件进行更新。一旦前一个流完成，我们发出值并完成。我们可以用同样的方式实现`scan`,只是我们会在每次有新值时发出，而不是在完成时发出。

这样，我们现在可以复制我们的 websocket 示例(假设我们已经像添加`map`一样将`scan`添加到 Stream 类中):

```
let websocket = new Websocket(/* ... */);
const websocketStream = new Stream(observer => {
    websocket.onMessage = (msg) => observer.onNext(msg);
    websocket.onClose = () => observer.complete();
});

const myValueStream = websocketStream
    .map(i => i * 2)
    .scan((acc, curr) => acc + curr, 0)
    .subscribe({
        next: console.log,
        complete: () => {}
    }); 
```

让我们更进一步。我们想要一个初始的 HTTP 请求和通过 websocket 的未来更新。没有溪流，这是很难做到的。为此，我们首先需要将承诺转化为现金流:

```
function fromPromise<T>(p: Promise<T>): Stream<T> {
    return new Stream<T>(observer => {
        p.then(data => observer.next(data));
    });
} 
```

然后，我们需要一种方法将数组流转换为单个项目流(假设我们的 API 返回一个数据数组，而 websocket 返回单个项目)。我们可以将它分成一个将数组转换成流的函数和另一个“展平”流的函数:

```
function fromArray<T>(array: T[]): Stream<T> {
    return new Stream(observer => {
        array.forEach(e => {
            observer.next(e);
        });
        observer.complete();
    });
}

function flatMap<T, U>(fn: (t: T) => Stream<U>): (s: Stream<T>) => Stream<U> {
    return stream => new Stream<U>(observer => {
        stream.subscribe({
            next(s: Stream<U>) {
                s.subscribe({
                    next: observer.next,
                    complete: () => {}
                });
            },
            complete: () => observer.complete()
        });
    });
} 
```

正如你在`fromArray`中看到的，我们只是把每一个元素放入流中。`flatMap`这里有趣多了。我们首先订阅外部流，在我们接收到的每个新的内部流上，我们也订阅外部流，并将所有值输出给下一个观察者。

让我们使用我们的新方法(假设我们已经向 Stream 类添加了 flatMap):

```
let websocket = new Websocket(/* ... */);
const websocketStream = new Stream(observer => {
    websocket.onMessage = (msg) => observer.onNext(msg);
    websocket.onClose = () => observer.complete();
});

let httpStream = fromPromise(getData())
    .flatMap(data => fromArray(data));

const myValueStream = websocketStream
    .map(i => i * 2)
    .scan((acc, curr) => acc + curr, 0)
    .subscribe({
        next: console.log,
        complete: () => {}
    }); 
```

缺少的最后一点是合并这两个流:

```
function merge<T>(...streams: Stream<T>[]): Stream<T> {
    return new Stream(observer => {
        let numCompleted = 0;
        streams.forEach(s => {
            s.subscribe({
                next: value => observer.next(value),
                complete: () => {
                    numCompleted++;
                    if(numCompleted === streams.length) {
                        observer.complete();
                    }
                }
            });
        });
    });
} 
```

如您所见，我们只是订阅所有的流，并在其中任何一个流发出时发出一个值。如果所有的流都完成了，我们就完成了这个流。至此，我们终于可以完成我们的示例了:

```
let websocket = new Websocket(/* ... */);
const websocketStream = new Stream(observer => {
    websocket.onMessage = (msg) => observer.onNext(msg);
    websocket.onClose = () => observer.complete();
});

let httpStream = fromPromise(getData())
    .flatMap(data => fromArray(data));

const myValueStream = merge(httpStream, websocketStream)
    .map(i => i * 2)
    .scan((acc, curr) => acc + curr, 0)
    .subscribe({
        next: console.log,
        complete: () => {}
    }); 
```

# 包装完毕

如果您有复杂的异步行为，可观察性会非常有用。自己写也没那么难！我在这里展示的玩具 RxJS 并不是 mayor 流库的实现方式，因为闭包在 Javascript 中的性能开销很大。但是核心思想保持不变。

我希望你喜欢这篇文章，并学到一些新东西。如果你对反应式编程感兴趣，看看 [Cycle.js](https://cycle.js.org/) ，这是一个完全反应式的框架，我是其核心团队的一员。