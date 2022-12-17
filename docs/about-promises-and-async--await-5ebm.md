# 关于承诺和异步/等待

> 原文：<https://dev.to/ardennl/about-promises-and-async--await-5ebm>

最近，我重温了[韦斯·博斯](https://twitter.com/wesbos)的[ES6 for everybody](https://es6.io/)课程，并对`Promise`和`async / await`模块做了一些笔记。这些笔记有点失控，现在它们变成了这个巨大的博客。这绝不意味着对每个人来说都是 ES6 的副本，我为这篇文章参考了很多资料，你可以在这篇文章的底部找到。

如果我做错了什么或错过了什么重要的东西，请随时纠正！你可以在我的[前端笔记 github repo](https://github.com/aderaaij/front-end-notes-links-resources-articles) 里找到这个帖子。

## 承诺

ECMAScript 2015 (ES6)为我们带来了`Promise`，这是一个本地对象，充当未知值的代理(或临时值)。承诺允许您创建处理异步操作最终成功或失败的处理程序。在这篇文章中，我将告诉你使用承诺的所有方法，如何使用自己的`Promise`功能，如何组合和链接承诺，以及如何用`async / await`让`Promise` api 变得更好。但是首先我们要回到绝对的基础:同步和异步代码。

## 同步/异步

我绝对相信你们大多数人可以跳过这一部分，但是如果你想知道我们在 JavaScript 中处理什么样的异步行为，请继续阅读。

当您创建立即返回值的函数或对象时，JavaScript 似乎是同步的。看起来每一行都是按顺序处理的，并在下一个进程开始运行之前返回值。

```
console.log('this will fire first');
console.log('this will fire second');
console.log('this will fire last'); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将准确地返回您所期望的结果，并按顺序将所有这些文本行记录到控制台。

然而，当其中一个进程比其他进程花费更长时间返回值时，我们可以看到该行为实际上是异步的。在下面的例子中，我们将在第二个`console.log`周围添加一个`setTimeout`，以便清楚地看到发生了什么。

```
console.log('this will fire first');
setTimeout(() => {
    console.log('this will fire second');
}, 500);
console.log('this will fire last'); 
```

Enter fullscreen mode Exit fullscreen mode

现在控制台中的日志记录是无序的，因为下一行不会等待上一行完成。概括一下:

*   “这将首先触发”会立即记录到控制台
*   `setTimeout`功能在计时器计时 500 毫秒时启动，在此时间内不会返回值。
*   “这将最后触发”被记录到控制台，因为它不等待`setTimeout`函数的结果。
*   500 毫秒后，控制台会记录“这将在第二时间触发”。

在我们的例子中，我们显然自己添加了延迟，只要我们知道超时值是多少，我们就可以处理延迟。但是在很多情况下，我们不知道什么时候会返回值，比如当我们获取数据或者处理一个复杂的过程，需要很长时间才能返回值。

我们过去通过使用回调来处理这个问题。一个函数将接受一个回调函数，这个函数将在耗时的进程返回某个东西时被调用。

```
function wait(ms, cb) {
    setTimeout(function() {
        console.log(`done after ${ms}ms`);
        cb();
    }, ms);
};

wait(1000, function() {
    console.log('here\'s our callback function');
}); 
```

Enter fullscreen mode Exit fullscreen mode

这看起来还不错。但是如果我们有另一个带回调函数的函数需要在我们的回调函数中调用，并且在那个函数中有另一个函数，那该怎么办呢？这将很快变得复杂，这就是我们通常所说的[回调地狱](http://callbackhell.com/)。

```
wait(300, function() {
    wait(600, function() {
        wait(500, function() {
            wait(400, function() {
                console.log('here\'s our final callback function');
            });
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

所有的缩进使得代码很难阅读。有很多方法可以解决这个问题，但这已经不重要了，因为我们已经有了`Promise`！

## `Promise` -简介

`Promise`是一个本地对象，作为一个未知值的临时值。承诺允许您创建处理异步操作最终成功或失败的处理程序

### 土著的承诺

#### 取

在我们开始兑现自己的承诺之前，让我们先来看看如何使用浏览器中已经存在的`promise`！几年来，`fetch`一直是我请求数据的首选 api。它非常干净，容易记忆和操作。如果您没有在 jQuery 中使用`ajax`请求，您可能会记得`XMLHttpRequest`，这不是获取数据的好方法。好了，不要再复制粘贴了，因为你很快就会记住`fetch`。我`promise`。

```
console.log(fetch('https://api.github.com/users/aderaaij')); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码仍然是异步的。我们没有捕捉承诺的结果，我们只是记录了对象本身。结果应该类似于`Promise {<pending>}`。这表明`user`变量确实是一个`promise`，并且在调用`console.log(user)`时的状态是`pending`。这很酷，但我们想看一些数据！

为了检查承诺的状态，我们可以添加一个`.then`方法。

```
fetch('https://api.github.com/users/aderaaij')
    .then(data => console.log(data)); 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们从 github 获取用户数据，当它成功返回时，我们直接记录它！那太容易了，不是吗？现在我们得到的数据仍然是“原始的”。`fetch`可以用来检索各种数据，所以它不只是假设你的数据是 JSON。幸运的是，我们可以用`json()`方法将其转换成 JSON 数据，这也返回了一个承诺。

每当`.then`方法返回一个值时，不管是不是`Promise`，你都可以在它上面标记另一个`.then`方法。当你返回一个值时，它作为一个立即解决的承诺被返回:`Promise.resolve(val)`。当你返回一个`Promise`时，下面的`.next`将在`Promise`被实际解析时被调用。

```
fetch('https://api.github.com/users/aderaaij')
    .then(data => data.json())
    .then(data => console.log(data)); 
```

Enter fullscreen mode Exit fullscreen mode

当`fetch`函数返回一个承诺时，我们对它调用`then`来转换数据。为了将数据转化成可用的 JSON，我们在上面调用`.json()`。因为`.json()`也返回了一个承诺，所以我们添加了另一个`.then`，可以对转换后的数据做任何我们想做的事情🎉。如果您需要组合多个函数，并且这些函数依赖于来自可能会也可能不会立即返回值的函数的数据，那么您可以看到这种链接是如何有用的。

但是如果有错误呢？

```
fetch('api.github.com/users/aderaaij')
    .then(data => data.json())
    .then(data => console.log(data)); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的脚本中，我们忘记了`https://`，所以浏览器现在在我的本地文件中寻找这个 api。如果运行这个，错误应该是`Uncaught (in promise)`。这意味着我们没有使用`catch`方法来标记由`fetch api`返回的承诺。

```
fetch('api.github.com/users/aderaaij')
    .then(data => data.json())
    .then(data => console.log(data))
    .catch(err => console.error('oh noes:', err)); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们添加了我们的`catch`方法，并抛出了一个显式错误。控制台消息现在应该提供更多的信息。

最后，我们还有`finally`方法。当最初的承诺被解决或拒绝时，最终返回一个`promise`。当你想让某件事发生时，不管函数是否解析，你都可以调用这个方法。基于承诺是否已经运行和完成来重置状态可能是有用的。浏览器支持仍然缺乏，但它在未来可能是有用的。

```
fetch('https://api.github.com/users/aderaaij')
    .then(data => data.json())
    .then(data => console.log(data))
    .catch(err => console.error('oh noes:', err))
    .finally(() => console.log('finally')); 
```

Enter fullscreen mode Exit fullscreen mode

*   [`Finally` - MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally)

#### `getUserMedia`

另一个很酷的浏览器内`promise`是`MediaDevices.getUserMedia()`方法，它提示用户使用网络摄像头或麦克风等媒体输入，并产生一个媒体流。当然，这个函数不能在没有用户许可的情况下运行，必须等待用户接受才能开始做一些事情。

```
const video = document.querySelector('video');
navigator.mediaDevices.getUserMedia({ video: true })
    .then(mediaStream => {
        video.srcObject = mediaStream;
        video.load();
        video.play();
    })
    .catch(err => console.error(Error("user said no 😡"))) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子`mediaDevices.getUserMedia({ video: true })`中，一个询问用户是否允许访问网络摄像头的提示。当用户接受或拒绝时，承诺解决或拒绝。当我们接受时，我们将视频元素的`mediaStream`设置为`sourceObject`，加载视频并播放。

## 建立自己的承诺

有很多情况下，我们想要做出自己的承诺，在这里我会告诉你如何做到这一点。

一个`Promise`的基数看起来是这样的:

```
const p = new Promise((resolve, reject) => {

}); 
```

Enter fullscreen mode Exit fullscreen mode

```
const p = new Promise((resolve, reject) => {
    resolve('yay');
}); 
```

Enter fullscreen mode Exit fullscreen mode

它在*中引入一个叫做`executor` ☠️的*函数，带有`resolved`和`reject`的参数。其思想是，无论承诺返回什么，要么解析并返回某种值，要么导致错误。

我们可以在新的`promise`中执行各种代码，每当我们得到想要返回和/或拒绝的代码时，我们就调用`resolve`和`reject`。

```
const p = new Promise((resolve, reject) => {
    resolve('This is awesome!');
});

p.then(console.log) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们立即解析值“这太棒了！”当我们将`then`方法应用于我们的`promise`时，我们立即得到被解析的值。

当你用`reject`替换`resolve`时，你会看到我们会得到一个`uncaught (in promise)`错误，同样的消息。我们也可以`catch`这个错误，并赋予它更多一点的意义。

```
const p = new Promise((resolve, reject) => {
    reject(Error('This is an error'));
});

p
    .then(console.log)
    .catch(err => console.error(err)); 
```

Enter fullscreen mode Exit fullscreen mode

现在，错误被捕获并记录下来。因为我们将`reject`消息包装在一个`Error`对象中，所以我们可以获得更多关于问题所在的信息。

在许多情况下，我们希望在承诺中同时返回一个`reject`和一个`resolve`。例如，您可以在接受参数的函数中返回一个承诺，并根据参数值返回`reject`或`resolve`。

```
 function isTonyStark(name) {
    return new Promise((resolve, reject) => {
        if (name === 'Tony') {
            resolve(`Welcome ${name}`);
        } else {
            reject(Error('Danger, Will Robinson, danger!'));
        }
    });
}

isTonyStark('Tony')
    .then(console.log)
    .catch(err => console.error(err)); 
```

Enter fullscreen mode Exit fullscreen mode

## 连锁承诺

当处理多个不会同时返回某些东西的进程时，承诺非常方便，尤其是当这些进程可能相互依赖时。有了承诺，您可以控制流程，并确保在前一个函数的数据返回之前不会执行下一个函数。

所以让我们画一个常见的场景，其中有两组数据:电影列表和英雄列表。数据集来自一个数据库调用，所以您并不确切知道什么时候可以得到它。你想创建一个函数，既返回电影信息，又返回主角的额外信息。

```
const movies = [
    { title: 'Thor 3, Ragnarok', company: 'Marvel', hero: 'Thor', id: 1 },
    { title: 'Black Panther', company: 'Marvel', hero: 'Black Panther', id: 2 },
    { title: 'Wonder Woman', company: 'DC', hero: 'Wonder Woman', id: 3 },
];

const heroes = [
    { name: 'Thor', team: 'Avengers' },
    { name: 'Black Panther', team: 'Avengers' },
    { name: 'Wonder Woman', team: 'Justice League', actor: 'Gal Gadot' },
]; 
```

Enter fullscreen mode Exit fullscreen mode

所以我们要做的第一件事，就是兑现我们的承诺。我们希望能够通过 ID 获取电影，所以我们开始:

```
function getMovieByID(id) {
    // We can immediately return a promise in our function, this is how we pass arguments
    return new Promise((resolve, reject) => {
        // Find the movie based on the movie ID
        const movie = movies.find(movie => movie.id === id);
        if (movie) {
            resolve(movie); // Resolve if we've got a movie
        } else {
            reject(Error('oh noes, no movie found'));
        }
    })
}

getMovieByID(3)
    .then(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们解决方案的第一部分。

下一步，我们需要制作另一个`promise`，这样我们就可以将它链接到我们的`getMovieById`上。当数据返回时，我们立即想开始使用它，并把它变成有用的东西。

我们的`hydrateData`函数接收从`getMovieById`承诺返回到`then`处理程序中的`data`对象。然后它返回我们正在制作的新的`Promise`。

```
function hydrateData(data) {
    // Return a new promise
    return new Promise((reject, resolve) => {
        // Find the hero by comparing the `hero` value in the `data` object to `hero.name`
        const info = heroes.find(hero => data.hero === hero.name);
        if (info) {
            data.hero = info; // Assigning info to data.hero (replacing the original `hero` value which was just a string)
            resolve(data);
        } else {
            reject(Error('have no heroes'));
        }
    });
}

getMovieByID(3)
    .then(data => hydrateData(data))
    .then((data) => {
        console.log(data);
    }); 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都在一起:

```
function getMovieById(id) {
    return new Promise((resolve, reject) => {
        const movie = movies.find(movie => movie.id === id);
        if (movie) {
            resolve(movie);
        } else {
            reject(Error('Movie not found'));
        }
    });
}

function hydrateData(data) {
    return new Promise((resolve, reject) => {
        const heroInfo = heroes.find(hero => data.hero === hero.name);
        console.log(heroInfo);
        if (heroInfo) {
            data.hero = heroInfo;
            resolve(data);
        } else {
            reject(Error('oh noe error'));
        }
    });
}

getMovieById(3)
    .then(data => hydrateData(data))
    .then((data) => {
        console.log(data);
    }); 
```

Enter fullscreen mode Exit fullscreen mode

## `Promise.all` -多重承诺

在某些情况下，您希望返回多个承诺，并在对这些数据进行处理之前等待所有承诺都得到解决。在这种情况下，您可以使用`Promise.all`。`.all`接受一个由[可重复项](https://developer.mozilla.org/nl/docs/Web/JavaScript/Reference/Iteration_protocols)(包括承诺)组成的数组，并在返回值之前等待*所有这些可重复项*被解析。

```
function printThor() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({
                name: 'Thor',
                bff: 'The Hulk',
                team: 'Avengers',
            });
        }, 500);
    });
}

function printQuotes() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(['This drink... I like it!', 'I need a horse!']);
        }, 1000);
    });
}

Promise
    .all([printThor(), printQuotes()])
    .then(([thor, quote]) => console.log(thor, quote)); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们有两个承诺，`500ms`后返回`printThor`，在`1000ms`后返回`printQuotes`。我们把它们放在一个数组中送给`Promise.all`，并称为`.then`。当*两个*承诺都被解决时，它返回数据。为了简单起见，我们在 arrow 函数参数中析构了这两个值。

但是，如果您从数组中获取数据，并且仍然需要将数据转换成有用的 JSON，该怎么办呢？在这种情况下，您可能想要返回另一个`Promise.all`。这次用一个`.map`函数映射响应并返回`.json()`。因为我们使用返回一个数组的`.map()`和返回一个`Promise`的`.json()`，我们基本上返回一个带有承诺的数组。

```
const dog = fetch('https://dog.ceo/api/breeds/image/random');
const dev = fetch('https://api.github.com/users/aderaaij');

Promise
    .all([dog, dev])
    .then(res => Promise.all(res.map(r => r.json())))
    .then(data => console.log(data)); 
```

Enter fullscreen mode Exit fullscreen mode

## 承诺 vs 回调

如果你记得我们在
写的那篇文章

```
function wait(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(console.log(`waited for ${ms}ms`));
        }, ms);
    });
}

wait(300)
    .then(res => wait(500))
    .then(res => wait(1000))
    .then(res => wait(700))
    .then(res => wait(300))
    .then(res => wait(900))
    .catch(err => console.error(err)); 
```

Enter fullscreen mode Exit fullscreen mode

这使得我们的代码更加扁平，因此可读性更好。

## 异步/等待

核心上，Async / Await 是建立在承诺之上的。
为了异步/等待任何东西，你需要一个返回承诺的函数。
`Await`总是需要在标有`async`的函数内调用。没有顶级 await。

```
function wait(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(console.log(`waited for ${ms}ms`));
        }, ms);
    });
}

const go = async () => {
    await wait(600);
    await wait(1200);
    await wait(1800);
}
go(); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们采用了在前面的代码块中创建的完全相同的`Promise`，并在标有`async`的函数中调用它。只要在你的函数前面添加`await`，你的代码就会同步运行，每个`wait`函数都会等待，直到前一个函数被解析。这个 API 甚至比`.then`方法更清晰，而且似乎性能也有所提高。在撰写本文时，大多数浏览器都支持`async / await`。

你也可以将一个函数的返回值放入一个变量:

```
function wait(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(ms > 500) {
                resolve(`waited for ${ms}ms`);
            } else {
                reject(Error(`you should wait longer than ${ms}ms!`));
            }
        }, ms);
    });
}

const go = async () => {
    const res1 = await wait(600);
    console.log(res1);
    const res2 = await wait(1000);
    console.log(res2);
    const res3 = await wait(1400);
    console.log(res3);
};

go(); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，第一个承诺的结果在 600 毫秒后记录，而第二个和第三个承诺的结果将在 3600 毫秒后一起记录。

## 错误处理

用`async`代码块处理错误有点笨拙。您可以用一个`try`和`catch`块将您的`await`语句括起来，就像这样:

```
function wait(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(ms > 500) {
                resolve(`waited for ${ms}ms`);
            } else {
                reject(Error(`you should wait longer than ${ms}ms!`));
            }
        }, ms);
    });
}

const go = async () => {
    try {
        const res1 = await wait(600);
        console.log(res1);
        const res2 = await wait(600);
        console.log(res2);
        const res3 = await wait(300);
        console.log(res3);
        const res4 = await wait(600);
        console.log(res4);
    } catch (err) {
        console.error('something went wrong...', err);
    }
}

go(); 
```

Enter fullscreen mode Exit fullscreen mode

```
function wait(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(ms > 500) {
                resolve(`waited for ${ms}ms`);
            } else {
                reject(Error(`you should wait longer than ${ms}ms!`));
            }
        }, ms);
    });
}

const go = async () => {
    try {
        const res1 = await wait(600);
        console.log(res1);
        const res2 = await wait(600);
        console.log(res2);
        const res3 = await wait(300);
        console.log(res3);
        const res4 = await wait(600);
        console.log(res4);
    } catch (err) {
        console.error('something went wrong...', err);
    }
}

go(); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们已经将所有的`await`承诺包装在我们的 try 块中，如果有一个被拒绝，我们将在我们的`catch`块中捕获该错误。

你也可以做一个“高阶组件”来包装 go 函数并捕捉所有的错误。这是我完全从[韦斯·博斯](http://wesbos.com/)那里学到的东西，你应该看看[他在`async/await`](https://youtu.be/9YkUCxvaLEk) 的演讲，他对此做了更深入的探讨。

```
function wait(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(ms > 500) {
                resolve(`waited for ${ms}ms`);
            } else {
                reject(Error(`you should wait longer than ${ms}ms!`));
            }
        }, ms);
    });
}

// First we make a function that takes in our async function as an argument
const catchErrors = (fn) => {
    // And return a function
    return function() {
        // Which returns our async function, which is a promse on which we can call `.catch`
        return fn().catch((err) => {
            console.error('uhoh', err);
        });
    };
};

const go = async () => {
    const res1 = await wait(600);
    console.log(res1);
    const res2 = await wait(600);
    console.log(res2);
    const res3 = await wait(300);
    console.log(res3);
    const res4 = await wait(600);
    console.log(res4);
}

const wrappedFunc = catchErrors(go); 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就不必为每个承诺编写不同的`try`和`catch`块。你只有在需要解决很多承诺的时候才想这么做，否则你最好编写你的`try / catch`块，写一些更具体的错误消息。

### 等待多个承诺

你需要小心`await`和多重承诺，它不是`Promise.all`的替代品。在你的承诺之前添加一个`await`语句使你的代码真正同步，所以如果你用`await`解析两个`fetch`承诺，其中一个不会在另一个完成之前开始获取数据。在很多情况下，你希望它们同时发射，然后等待两者的结果返回。

```
async function getDogs() {
    // Store the promise in a variable
    const dog1 = fetch('https://dog.ceo/api/breeds/image/random');
    const dog2 = fetch('https://dog.ceo/api/breeds/image/random');

    const results = await Promise.all([dog1, dog2]); // Wait until both promises are ready
    // Array destructure, await our res.json() promises
    const [mut1, mut2] = await Promise.all(results.map(res => res.json()));
    console.log(mut1, mut2);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有多个承诺，我们甚至可以映射它们并在`Promise.all`中返回它们。

```
async function getDogs(breeds) {
    const promises = breeds.map((breed) => {
        return fetch(`https://dog.ceo/api/breed/${breeimg/random`);
    });

    const results = await Promise.all(promises);
    const data = await Promise.all(results.map(r => r.json()));
    console.log(data);
}
getDogs(['husky', 'malamute', 'terrier']); 
```

Enter fullscreen mode Exit fullscreen mode

## 鳍

这就是现在的总结！如果您有任何问题或意见，请随时评论或联系我！

## 资源列表

*   [Wes Bos 的 ES6 for every one](https://es6.io)
*   [dotJS 2017 - Wes Bos - Async +在 YouTube 上等待发言](https://www.youtube.com/watch?v=9YkUCxvaLEk)
*   [JavaScript . info 上的承诺链接](https://javascript.info/promise-chaining)
*   stackoverflow.com 上[诺言`then` vs `then` + `catch`](https://stackoverflow.com/questions/33278280/promise-then-vs-then-catch)
*   [异步堆栈跟踪:为什么`await`会打败`.then()` - mathiasbynens.be](https://mathiasbynens.be/notes/async-stack-traces)
*   [使用 Promises - MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
*   [承诺对象- MDN web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
*   [`Promise.all` - MDN 网络文档](https://developer.mozilla.org/nl/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)
*   [3 个关于承诺的事实-由 K 在开发中写给](https://dev.to/kayis/3-facts-about-promises-215c)
*   [ES6 卡塔斯](http://es6katas.org/)

*   [异步/等待 caniuse.com](https://caniuse.com/#search=await)

*   [关于 async/await 和 Promises 的问题 Massimo Artizzu 关于开发的文章](https://dev.to/maxart2501/gotchas-about-asyncawait-and-promises-9di)

*   [等待异步承诺——由发展到](https://dev.to/rhymes/awaiting-for-async-promises-in-javascript-3gbd)上的韵文写成

*   [迭代协议- MDN 网络文档](https://developer.mozilla.org/nl/docs/Web/JavaScript/Reference/Iteration_protocols)

*   Mahmoud Felfel 对 javascript 可迭代程序、迭代器和生成器的介绍

*   2ality.com 上 ECMAScript 6 中的可迭代程序和迭代器