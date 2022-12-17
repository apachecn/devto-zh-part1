# 互动游戏可以用简单的 javascript 来制作

> 原文：<https://dev.to/abhishek1009/interactive-games-can-be-made-with-simple-javascript>

前几天刚开始学 javascript。我发现它非常具有互动性，而且简单易懂。用更少的代码就可以做出非常复杂的东西。所有现代的网站都是用 javascript 制作的，因为它有大量的库，而且非常强大。所有的 html 和 css 属性都可以用这个来操作或更改。如果你不知道什么是 html 和 css，那么让我告诉你它是什么。在一个简单的网页中，无论你看到什么，都是用 html 制作的。它有一堆标签，在这些标签之间的内容和网页上所有可见的东西都是由 css 决定的。现在你可以想象它的威力了。

## 哇哇游戏

这是一个简单的游戏。在加载游戏时，你会发现有两个部分的背景颜色是蓝色，其他的是黄色。如果你点击开始按钮，那么你的左边将会有六个随机放置的笑脸，右边是克隆的图像，除了你必须找到的那个，这也是游戏的目标。每次你点击右边的图片，你会得到五个以上的微笑，左边会多一个微笑。如果你打错了图像，游戏就会停止。

## 下面是制作游戏的 javascript 代码

```
var score =0;
var failure=0;
var level =0;
var failed_no =0;

function create5(){
    for(var i=1;i<=5;i++)
    {
        var img = document.createElement("IMG");
        img.setAttribute("src","smiley_face.gif");
        img.setAttribute("width", "60");
        img.setAttribute("height", "60");
        var left_div=document.getElementById("leftside");
        var left_div_height = left_div.offsetHeight;

        var top_position = Math.floor(Math.random()*(left_div_height-60)); 
        var left_position= Math.floor(Math.random()*(left_div_height-60));
        img.style.top=top_position +"px";
        img.style.left=left_position+ "px";
        left_div.appendChild(img);

        img.addEventListener("click",display_right);
        function display_right(){
            alert("you found the wrong image");
            failure=1;
            show_score1();
            failed();
        }

        var cln_img = img.cloneNode(true);
        var right_div=document.getElementById("rightside");
        right_div.appendChild(cln_img);
    } 
    if(failure<1){clone_image();}
}

function clone_image(){
    var extra_img = document.createElement("IMG");
    extra_img.setAttribute("src","smiley_face.gif");
    extra_img.setAttribute("width", "60");
    extra_img.setAttribute("height", "60");
    var left_div=document.getElementById("leftside");
    var left_div_height = left_div.offsetHeight;

    var top_position = Math.floor(Math.random()*(left_div_height-60)); 
    var left_position= Math.floor(Math.random()*(left_div_height-60));
    extra_img.style.top=top_position +"px";
    extra_img.style.left=left_position+ "px";
    left_div.appendChild(extra_img);

    extra_img.addEventListener("click",display_right);
    function display_right(){
        if(failure<1)
            {
                alert("you found the extra image");
                left_div.removeChild(extra_img);
                create5();
                show_score(); 
            }
        else{
            alert("This is the extra image but you already hit the wrong image,Sorry!!");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里定义了两个函数。一个是 create5()，另一个是 clone_image()。第一个用于在两侧创建 5 个图像，第二个用于在左侧创建额外的图像。

```
function show_score(){
    level=level+1;
    var your_level="";
    var mult =0;
    if(level<6)
    {mult=4;
    your_level="beginer";}
    else {
        if(level<12)
            {mult=6;
            your_level="intermediate";}
        else
            {mult=7;
            your_level="pro"}
         }
    score = score+mult;
    alert("Your score is "+score+" xp\nYou are in "+your_level+" level");

}

function failed(){

    failed_no=failed_no+1;
    if(failed_no==1){
        var fail = document.getElementById("info");
        var text = document.createTextNode("You have failed");
        fail.appendChild(text);
    } 
}
function show_score1()
{
   alert("Your final score is "+score+" xp"); 
} 
```

Enter fullscreen mode Exit fullscreen mode

这个 show_score 函数显示用户每次点击额外图像时的当前分数和级别。当他点击错误的图像时，失败的函数被调用。显示你失败了，最后一个函数显示最终分数。

```
function removeAll(){
    score =0;
    failure=0;
    level =0;
    failed_no =0;

    var left_div=document.getElementById("leftside");
    while (left_div.hasChildNodes()) 
    {   
        left_div.removeChild(left_div.firstChild);
    }

    var right_div=document.getElementById("rightside");
    while (right_div.hasChildNodes()) 
    {   
        right_div.removeChild(right_div.firstChild);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

函数 removeAll 用于删除所有图像。

### 你可以在这里玩游戏:[检测多出来的笑脸](http://mymess6.000webhostapp.com/Detect_the_extra_smiley_game_with_javascript/Game3.html)