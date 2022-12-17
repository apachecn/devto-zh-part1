# 学习打字稿的基础开始写 Angular

> 原文：<https://dev.to/abelagoi/learn-the-basis-of-typescript-to-start-writingangular-87i>

Typescript 是 javascript 的超集，这意味着任何 javascript 代码都是有效的 typescript，但 typescript 有一些 javascript 中没有的附加功能，例如强类型(在声明时指定变量的类型，这可以使我们的应用程序更加可预测和易于调试)，将面向对象的功能引入 javascript 等等。

浏览器不支持类型脚本，所以我们需要将类型脚本转换成 javascript。

我们需要在我们的系统上安装 typescript 才能使用它，在您的终端上运行下面的代码来安装 typescript:

```
sudo npm install -g typescript 
```

Enter fullscreen mode Exit fullscreen mode

打开文本编辑器，创建一个新文件 main.ts

```
function log(message) { 
    console(message);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let message = 'Hello Typescript';
log(message); 
```

Enter fullscreen mode Exit fullscreen mode

在终端的项目目录下运行下面的命令

```
tsc main.ts 
```

Enter fullscreen mode Exit fullscreen mode

上面的 tsc main.ts 命令创建了一个新文件 main.js，它是被传输的 main.ts 的 javascript 版本。

我们可以使用下面的命令从终端运行 transpiled main.js

```
node main.js 
```

Enter fullscreen mode Exit fullscreen mode

注意:您不需要在 angular 应用程序中手动运行 tsc main.js，因为 **ng serve** 会自动为您运行。

**打字稿类型:**

我们在 typescript 中有不同的类型，包括:

```
let a: number //e.g 1, 2, 3
let b: boolean //e.g true, false
let c: string //e.g "abel agoi"
let d: any //this can take any other types
let e: number[] //array of numbers e.g [1, 3, 54]
let f: any[] //any array e.g [1, "abel agoi", true] 
```

Enter fullscreen mode Exit fullscreen mode

注意:还有一种类型叫做 enum，我没有提到，你可以自己去看看。

**箭头功能**:

在 javascript 中，我们可以像下面这样声明一个函数:

```
let log = function (message) {
    console.dir(message);
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用箭头函数(= >)在 typescript 中实现相同的功能，如下图

```
let log = (message) => { 
    //we simply remove the function 
    console.dir(message);
}

//above can even be shorten to
let log = (message) => console.dir(message);

//and if you are only passing one parameter, it can be shorten to
let log = message => console.dir(message); //not readable though 
```

Enter fullscreen mode Exit fullscreen mode

**接口:**

将许多参数传递给如下函数是一种不好的做法:

```
let myFunction = ( 
    a: number, 
    b: number, 
    c: string, 
    d: any, 
    e: number[], 
    f: any[]
) => { 
        console.log('something);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过将所有参数封装到一个对象中，并使用如下的**接口**(由 typescript 提供)将单个对象传递给函数来避免这种情况:

```
interface MyParameters { 
    a: number, 
    b: number, 
    c: string, 
    d: any, 
    e: number[], 
    f: any[] 
    ... 
    ...
}

let myFunction = (myParameters: MyParameters) {} 
```

Enter fullscreen mode Exit fullscreen mode

**类:**

将相关的变量(属性)和函数(方法)组合到一个代码块中是一个很好的习惯，这个代码块在编程中称为一个类。创建一个名为 myPoint.ts 的新文件，如下:

```
class MyPoint { 
    x: number; 
    y: string;

    draw() { 
        //note the way we access the x property below using this 
        console.log(this.x); 
        console.log(this.y); 
    }

    getDistanceBtw(another: AnotherPoint) {
        //get distance 
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

**访问类属性和方法:**

因为我们已经将相关的属性和方法分组到一个类中。我们需要能够访问那些属性和方法。我们可以通过创建类的实例来访问方法的属性。

```
let myPoint = new MyPoint() //where MyPoint is the class name

myPoint.x = 2; //this is you setting the property of x 
myPoint.y = "a"; //this is you setting the property of a

myPoint.draw(); //we access the draw method 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过在终端中输入下面的命令来编译和运行这个类:

```
tsc myPoint.ts | node myPoint.js 
```

Enter fullscreen mode Exit fullscreen mode

在分配类属性之前，我们需要首先创建一个类的实例。如果在实例化类时有更好的方法来动态分配属性呢？是的，有。构造函数。

**构造器:**

构造函数是每当我们实例化一个类时自动调用的方法。构造函数允许我们在实例化一个类时分配属性。

```
let myPoint = new MyPoint()

myPoint.x = 2; 
myPoint.y = "a"; 
```

Enter fullscreen mode Exit fullscreen mode

以上内容现在可以缩短为以下:

```
let myPoint = new MyPoint(2, "a"); 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要更新我们的类，以允许使用构造函数

```
class MyPoint { 
    x: number; 
    y: string;

    constructor (x: number, y: string) { 
        this.x = x; 
        this.y = y; 
    }

    draw() { //note the way we access the x property below using this                 
        console.log(this.x); 
        console.log(this.y); 
    }

    getDistanceBtw(another: AnotherPoint) {
     //get distance 
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

**构造函数中的可选参数:**

如果我们想使用一个构造函数，但又让它成为可选的，该怎么办？是的，有可能。我们需要使用“？”在如下的构造函数中。那个“？”符号允许我们声明一个参数是可选的。

```
class MyPoint { 
    x: number; 
    y: string;

    constructor (x?: number, y?: string) { 
       //note the "?" before ":" 
       this.x = x; 
       this.y = y; 
    }

    draw() { //note the way we access the x property below using this     
       console.log(this.x); 
       console.log(this.y); 
    }

    getDistanceBtw(another: AnotherPoint) { 
        //get distance 
    }

}

//The below will work for instance A of MyPoint

let myPointA = new MyPoint()
myPoint.x = 2; 
myPoint.y = "a";
myPoint.draw();

//The below will work for instance B of MyPoint

let myPointB = new MyPoint(2, "b");
myPointB.draw();

//The below will work for instance A of MyPoint
let myPointC = new MyPoint(2); //note: we didnt pass parameter for Y
myPointC.draw(); 
```

Enter fullscreen mode Exit fullscreen mode

**访问修饰符:**

访问修饰符是我们应用于一个属性或一个类的成员的一个关键字，用来从外部控制它的访问。在 typescript 中我们基本上有三个访问修饰符:public，protected，private。默认情况下，所有成员都是公共的，这意味着可以从类外部访问/修改它们。例如，将属性 **x** 和 **y** 设置为私有将不允许它们在类
之外被访问

```
class MyPoint { 
    private x: number; 
    private y: string;

    //we can also set an access modifier on methods like below 
    public draw() { 
        //draw something 
    }
}

let myPoint = new MyPoint();
myPoint.x = 3; 
```

Enter fullscreen mode Exit fullscreen mode

由于 private 关键字，访问上面的 myPoint.x 实例将导致错误。

**我喜欢在构造函数中使用的 Typescript 助手**

我们给我们的类添加了一个构造函数，如下:

```
private x: number;public y: string;

constructor (x: number, y: string) { 
    this.x = x; 
    this.y = y;
} 
```

Enter fullscreen mode Exit fullscreen mode

多亏了 typescript，我们可以将上面的内容缩短为:

```
constructor (private x: number, public y: string) {} 
```

Enter fullscreen mode Exit fullscreen mode

对，就是这样。Typescript 将自动为我们处理其余的工作(我打赌你会在 angular 应用程序中经常看到这一点)。我们不需要:

```
private x: number;
public y: string; 
```

Enter fullscreen mode Exit fullscreen mode

和

```
this.x = x;
this.y = y; 
```

Enter fullscreen mode Exit fullscreen mode

**getter 和 setter**

假设 MyPoint 类的当前状态如下所示

```
class MyPoint {

    constructor (private x?: number, private y?: string) {}

    draw() { 
        //draw something 
    }

    drawAnotherThing() { 
        //draw another thing 
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们当然知道，由于应用了 private 修饰符，我们将无法访问 MyPoint 类的 x 和 y 属性，但是如果我们希望能够访问它们，我们需要使用如下的 getter 和 setter:

```
class MyPoint {

    constructor (private x?: number, private y?: string) {}

    getX() { 
        //this is use to get X 
        return this.x; 
    }

    setX(value) { 
        //this is use to set X 
        this.x = value; 
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

由于我们不能在初始化 MyPoint 类后直接设置 X，
我们使用 setX() (setters)来设置 X 的值，如下:

```
let myPoint = new MyPoint();
myPoint.setX = 4;
console.log( myPoint.getX() ); //this will give us 4; 
```

Enter fullscreen mode Exit fullscreen mode

Setters 和 Getters 可以让您在设置和获取值时对 X 的值设置约束。

**另一个我喜欢使用 Setters 和 Getters 的打字助手**

不使用 myPoint.getX()来获取 X 的值，如果我可以做类似于
的事情呢

```
myPoint.X = 4; //accessing X like its a property when its actually a getter 
```

Enter fullscreen mode Exit fullscreen mode

然后我需要在 getter 和 setter 函数名之前创建一个空格

```
class MyPoint {

    constructor (private x?: number, private y?: string) {}

    get X() { 
        //note the space before the X 
        return this.x; 
    }

    set X(value) { 
        //note the space before the Y 
        this.x = value; 
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

以下划线(_)开始命名我们的属性也是一种常见的做法，这样我们就可以有类似下面的内容

```
class MyPoint {

    constructor (private _x?: number, private _y?: string) {}

    get x() { 
        //note the space before the X 
        return this._x; 
    }

    set x(value) { 
        //note the space before the Y 
        this._x = value; 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**模块**

一个真实的应用程序会有不止一个类。我们必须能够使我们的类可以在另一个类中使用，因此需要模块化。首先让我们将我们的 **myPoint.ts** 类修改如下:

```
export class MyPoint { //note the export keyword added

    constructor (private _x?: number, private _y?: string) {}

    get x() { 
        return this._x; 
    }

    set x() {
        //note the space before the Y
    }

    draw() {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

关键字 **export** 将使 MyPoint.ts 可见，并在**导入**它的另一个类中使用。

我们需要在 **main.ts** 中导入 MyPoint.ts 类才能使用它。

```
import { MyPoint } from './myPoint';

class Main {
    let MyPoint = new MyPoint(4, 'good to go');
    MyPoint.draw();
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:main.ts 和 myPoint.ts 在同一个目录下。

我很高兴与您分享开始学习 Angular 所需的基本打字稿。

感谢阅读