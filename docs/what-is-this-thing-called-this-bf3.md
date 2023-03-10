# 这个东西叫什么？

> 原文：<https://dev.to/aurelkurtula/what-is-this-thing-called-this-bf3>

描述`this`最简单的方法是，当调用时，它引用被赋予*的对象。例如，考虑这个对象:*

```
const song = {
  title: 'What is this thing called love',
  writer: 'Cole Porter',
  artist: 'Elsie Carlisle',
  performed_by: function(){
    return this.artist
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当调用`song.performed_by()`方法时，`this`指的是`song`。**但是在我们实际执行代码之前，`this`指的是空的**。

考虑下面两行:

```
console.log(this.someVariableName)
console.log(someVariableName) 
```

Enter fullscreen mode Exit fullscreen mode

第一行输出`undefined`，第二行抛出一个错误，告诉我们`someVariableName`未定义。如果我们定义变量而不传递值，那么两种情况下的输出是相同的

```
console.log(this.someVariableName)
let someVariableName;
console.log(someVariableName) 
```

Enter fullscreen mode Exit fullscreen mode

这告诉我们`this`真的在等着被分配给什么。

## 改变上下文`this`

多年来，很多人都唱过这首歌，让我告诉你我目前最喜欢的版本:

```
let performers = [
  {
    artist: 'sant andreu jazz band',
    listen_at: 'v=kZS2Kx1Hr9I'
  },
  {
    artist: 'Rebecca Ferguson',
    listen_at: 'v=O0FwMORV2Og'
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

当我运行`song.performed_by`我希望上述艺术家之一被返回！我们通过使用`call`、`apply`甚至`bind`来做到这一点。

这是那首歌我最喜欢的版本:

```
song.performed_by.call(performers[1])
//or
song.performed_by.apply(performers[1]) 
```

Enter fullscreen mode Exit fullscreen mode

`performed_by`中的`this`引用了另一个对象，所以上面代码的输出是`Rebecca Ferguson`。

`call`和`apply`的区别在于我们传递参数的方式。在上面的场景中没有区别，但是如果我们要传递参数，`apply`将它们作为数组传递。

## 让我们再做一个例子

让我们稍微修改一下代码，以便返回标题和艺术家。

```
let song = {
  ...
  performed_by: function(){
    return `${this.title} by ${this.artist}`
  }
}
let performers = [
  {
    artist: 'sant andreu jazz band',
    listen_at: 'v=kZS2Kx1Hr9I'
  },
  {
    artist: 'Rebecca Ferguson',
    listen_at: 'v=O0FwMORV2Og'
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

运行`song.performed_by()`将返回标题和歌曲。但是运行`song.performed_by.call(performers[0])`会返回`undefined by sant andreu jazz band`，那是因为`performers[0]`对象没有标题。让我们修改代码。

```
let song = {
  ...
  performed_by: function(value){
    let title = this.title ? this.title : value
    return `${title} by ${this.artist}`
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以添加标题作为一个参数

```
song.performed_by.call(performers[0], 'What is this thing called love') 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用`apply`,那么标题必须以数组的形式传递(很明显，我们必须引用它为`title[0]`)

```
song.performed_by.call(performers[0], ['What is this thing called love']) 
```

Enter fullscreen mode Exit fullscreen mode

## 使用`bind`

`bind`类似于`call`,但是当我们想将`this`赋给一个对象而不调用它时使用

```
let announcement = function(title){
    return `${title} by ${this.artist}`
}.bind(performers[0])
announcement('What is this thing called love') 
```

Enter fullscreen mode Exit fullscreen mode

事实上，上面的片段完美地展示了`call`和`bind`的区别

```
let announcementFunction = function(title){ ... }.bind(performers[0])
let announcementValue = function(title){ ... }.call(performers[0]) 
```

Enter fullscreen mode Exit fullscreen mode

因为`call`匿名函数已经执行，并且值被附加到`announcementValue`

使用`bind`的有用地方可能是在事件监听器上

```
document.querySelector('a').addEventListener('click', theMan.bind(song) ) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创建`theMan`,它将接受`song`对象中的任何内容

```
const band = function(){
    return `${this.artist} band`
}.bind(performers[0])
function theMan(e){
    console.log(`${this.writer} wrote a great song 
        and the girls from ${band()} sing an amazing cover`)
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们点击锚标签时，我们得到消息`Cole Porter wrote a great song and the girls from sant andreu jazz band sing an amazing cover`

* * *

就这些，感谢阅读。另外，如果你在音乐方面和我一样，你会喜欢我列出的 youtube 链接