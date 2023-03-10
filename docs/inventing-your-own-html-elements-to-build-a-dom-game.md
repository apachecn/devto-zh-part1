# 发明自己的 HTML 元素来构建一个 DOM 游戏

> 原文：<https://dev.to/eerk/inventing-your-own-html-elements-to-build-a-dom-game>

## 用 HTML 自定义元素构建 DOM 游戏

这是一个小实验，展示了如何在 DOM 中创建自己的 HTML 元素来构建游戏。

### 这些自定义元素是什么？

自定义元素是一种 HTML 元素，允许您添加自己的属性和方法。例如，基本的 [HTMLElement](https://developer.mozilla.org/en/docs/Web/API/HTMLElement) 有一个`style`属性和一个`click()`方法。通过扩展 HTML 元素，我们可以获得所有现有的功能，并且可以添加我们自己的功能。

在这个实验中，我们有一个元素`Car`，它有一个`x`和`y`属性和一个`update()`方法:

```
class Car extends HTMLElement {

    public x: number;
    public y: number;

    constructor(){
        super();
        console.log("A car was created!");
    }

    public update(): void {
        console.log("The car's update method was called!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在将 Car 元素添加到 DOM 之前，我们必须注册它，将我们的类连接到一个 HTML 标签。注意 html 标签需要包含一个连字符:

```
window.customElements.define("car-component", Car); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以通过放置标签将汽车添加到 dom 中:

```
<body>
    <car-component></car-component>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

在我们的游戏中，我们更喜欢通过代码添加汽车。我们可以创建一个新的`Car`实例，并在一行中将其添加到 DOM:

```
document.body.appendChild(new Car()); 
```

Enter fullscreen mode Exit fullscreen mode

这将导致一个`<car-component></car-component>`被添加到您的 HTML 结构中，并且消息`A car was created!`将出现在控制台中。这个 HTML 元素将有一个`x`和`y`属性和一个`update()`方法。

### DOM 操纵

您可以在 HTML 文档中查询 car 组件，并使用新的`for of`循环调用它们的`update()`方法。

```
let cars : NodeListOf<Car> = document.getElementsByTagName("car-component") as NodeListOf<Car>;

for(let c of cars){
    c.update();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 生命周期

定制元素有生命周期挂钩:当汽车被添加到 DOM 或从 DOM 中删除时，这些方法会被自动调用。

```
class Car extends HTMLElement {

    public connectedCallback(): void {
        console.log("A car was added to the DOM");
    }

    public disconnectedCallback():void{
        console.log("hey! someone removed me from the DOM!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 游戏循环

我们的游戏类将创建一个玩家元素并开始游戏循环。一个游戏循环使用`requestAnimationFrame`每秒更新我们的游戏元素 60 次。

*   游戏循环将通过使用模操作符每秒向 DOM 添加一个`new Car()`。
*   游戏循环会找到所有的`<car-component>`标签并调用它们的更新方法。

游戏在 window.load 后用`new Game()`实例化，注意游戏类本身不需要扩展 HTMLElement。

```
class Game {

    private counter:number = 0;

    constructor() {
        document.body.appendChild(new Player());
        requestAnimationFrame(() => this.gameLoop());
    }

    private gameLoop(){
        this.counter++;
        if(this.counter%60 == 0) {
            document.body.appendChild(new Car());
        }

        let cars : NodeListOf<Car> = document.getElementsByTagName("car-component") as NodeListOf<Car>;

        for(let c of cars){
            c.update();
        } 

        requestAnimationFrame(() => this.gameLoop());
    }
}

window.addEventListener("load", function() {
    new Game();
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 拆卸汽车

当一辆车离开屏幕或者撞到玩家时，我们可以很容易地将其移除。由于 car 是从 HTMLElement 扩展而来的，我们可以使用`remove()`方法，将它从 DOM 中移除。游戏循环不再调用 update 方法，如果我们愿意，我们可以使用`disconnectedCallback()`在汽车完全移除之前执行一些最终代码。

```
public disconnectedCallback():void{
    console.log("the car is removed from the game!");
}

public update(): void {
    this.x += this.speed;

    if (this.x > window.innerWidth) {
        this.remove();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 造型和动画

请注意，我们的自定义元素的样式完全是在 CSS 中完成的。首先我们声明所有的文档元素都将使用`position:absolute`，然后我们为每个单独的元素声明大小和背景图像:

```
body * {
    position: absolute;
    display: block;
    margin:0px; padding:0px;
    box-sizing: border-box;
    background-repeat: no-repeat;
}

car-component {
    width:168px; height:108px;
    background-image: url('img/car.png');
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`css transform`来定位我们的元素，这样我们就可以使用 GPU 来实现平滑的动画。

```
this.style.transform = `translate(${this.x}px, ${this.y}px)`; 
```

Enter fullscreen mode Exit fullscreen mode

### 扩展其他元素

这个例子只使用了`extends HTMLElement`，但是我们也可以扩展一个`HTMLDivElement`、`HTMLButtonElement`等等。在这个例子中，通过简单地在 CSS 中设置`display:block`，我们所有的元素都被视为`<div>`。

### 打字稿

这个实验是用 Typescript 构建的，但是您可以通过删除类型信息，用纯 Javascript 轻松地重新构建它。您可以检查`main.js`文件来查看 Javascript 的等价内容。

要编译这个项目，您可以安装带有`npm install -g typescript`的 Typescript，然后在终端的项目文件夹中键入`tsc -p`。

### 浏览器支持

上面的实验在 Safari 和 Chrome 中都有效。扩展特定元素(如 HTMLButtonElement)还不能在任何浏览器中工作。使用 [polyfill](https://github.com/webcomponents/custom-elements/blob/master/custom-elements.min.js) 获得所有浏览器的支持。

### 为 NodeList 中的循环

Safari 和 Firefox 中的`NodeList`和`HTMLCollection`还不支持`for of`循环，因为它们在技术上不是数组。使用
启用

```
NodeList.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
HTMLCollection.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator]; 
```

Enter fullscreen mode Exit fullscreen mode

从以下网址下载工作项目:

[https://github.com/KokoDoko/game-custom-elements](https://github.com/KokoDoko/game-custom-elements)