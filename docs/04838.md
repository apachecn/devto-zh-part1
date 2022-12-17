# 16 个最佳源代码宝石[收藏]

> 原文：<https://dev.to/lpasqualis/16-best-source-code-gems-collection>

这篇文章最初发表在 [CoderHood](https://coderhood.com) 上，作为 [16 个最佳源代码宝石【收藏】](https://www.coderhood.com/16-best-source-code-gems-collection/)。CoderHood 是一个致力于软件工程人类层面的博客。

[![](img/453c0909f987ac3d30375e0f973d0561.png)T2】](https://www.coderhood.com/16-best-source-code-gems-collection/)

开发者的个性以奇怪的方式闪耀。其中之一是(有时)微妙的幽默感、用词和态度，你可以在代码、代码注释和错误消息中找到它们。它常常感觉像是一股意识流，反映了某个时刻的人类现实。

我收集这些宝石，在这里我与你分享一些我最喜欢的。有些是我在自己工作的代码中发现的。其他的，我从各种渠道收集。所有这些都被剥夺了任何可能被视为知识产权的东西。

### 1 -通配符编译器。

```
Â Â /*
Â Â  * I don't know why the compiler hates me.
Â Â  * I changed this comment many times, and every
Â Â  * time it generated the same executable.
Â Â  * You should try. It's wild.
Â Â  */ 
```

Enter fullscreen mode Exit fullscreen mode

### 2——如果你到了这里。

```
Â Â /*
Â Â  * If you get here, I screwed up.
Â Â  * If I screwed up, I clearly don't know what I am doing.
Â Â  * If I don't know what I am doing, and you are looking at my code...
Â Â  *Â Â  ...we are both in trouble.
Â Â  */ 
```

Enter fullscreen mode Exit fullscreen mode

### 3 -经典，无用的文档。

```
Â Â int  i  =  0;Â Â // Set i to zero. 
```

Enter fullscreen mode Exit fullscreen mode

### 无论你做什么，我都能做得更好。

```
Â Â int  getRandomNumber()  {
Â Â Â Â Â Â return(rand()+1);Â Â // You have been one-upped
Â Â } 
```

Enter fullscreen mode Exit fullscreen mode

### 5——连试都不试。

```
Â Â /*
Â Â  * Don't even try to understand this.
Â Â  * I wrote it, and I have no idea why it works.
Â Â  * But it does. My subconscious is that good.
Â Â  */ 
```

Enter fullscreen mode Exit fullscreen mode

### 6——感觉恶心。

```
 Exception up  =  new  Exception("Something is really wrong.");
 throw  up;Â Â // ha ha 
```

Enter fullscreen mode Exit fullscreen mode

### 7 -崔(影响下的编码)。

```
 // drunk, fix later 
```

Enter fullscreen mode Exit fullscreen mode

### 8 -无可奉告

```
 /*
Â Â Â Â * Sorry, I have removed all comments from this code.
Â Â Â Â * If you can't understand it, you shoudn't touch it anyway.
Â Â Â Â */ 
```

Enter fullscreen mode Exit fullscreen mode

### 9 -真诚地

```
 public  boolean  thisCodeSucks()  {
Â Â Â Â Â Â return  true;  // sincerely
 } 
```

Enter fullscreen mode Exit fullscreen mode

### 10 -随机数

```
 int  getRandomNumber()  {
      return  4;  // Guaranteed to be random. I used a dice.
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 11 -这么久

不是代码，但仍然很有趣。

[![](img/3e864e2486d60ff6c7163623bb1a9608.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yuCTpDlN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l5b0vpmov69xfvfvayfa.jpg)

### 12 -人类的需求

这是取自[苹果象棋引擎代码](https://opensource.apple.com/source/Chess/Chess-311/Sources/MBCEngine.mm.auto.html)。

```
Â Â //
Â Â // Paradoxically enough, moving as quickly as possible is
Â Â // not necessarily desirable. Users tend to get frustrated
Â Â // once they realize how little time their Mac really spends
Â Â // to crush them at low levels. In the interest of promoting
Â Â // harmonious Human - Machine relations, we enforce minimum
Â Â // response times.
Â Â //

Â Â const  NSTimeInterval kInteractiveDelayÂ Â Â Â =  2.0;
Â Â const  NSTimeInterval kAutomaticDelayÂ Â =  4.0; 
```

Enter fullscreen mode Exit fullscreen mode

### 13 -教科书注释

```
Â Â /*
Â Â  * Explanation omitted as an exercise for the reader.
Â Â  */ 
```

Enter fullscreen mode Exit fullscreen mode

### 14 -过期代码

```
 // If you are reading this after 1/1/2010
 // there's something wrong.
 // This should have been ripped out by then.
 if  (DateTime.Now  >  new  DateTime(2010,  1,  1))  {
     throw  new  Exception("This code is now obsolete");
 } 
```

Enter fullscreen mode Exit fullscreen mode

### 15 位-“临时”代码

```
 // somedev1 -Â Â 6/7/02 Adding temporary tracking of Login screen
 // somedev2 -Â Â 5/22/07 Temporary my ass 
```

Enter fullscreen mode Exit fullscreen mode

### 16 -家庭问题

```
 /*
Â * The game of life
Â */
 if  (status==SINGLE)  {
Â Â Â Â Â Â money  =  1;
 }  elsif  (status==DIVORCING)  {
Â Â Â Â Â Â money  =  money  /  2;
 }  elsif  (status==DIVORCED)  {
Â Â Â Â Â Â money  =  money *  2;
 }  else  {  // status==MARRIED
Â Â Â Â Â Â money  =  rand()  *  monthly_salary  +  0;  // Savings is represented by zero.
 } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 如果你喜欢这篇文章，请保持联系！

*   在 CoderHood 上找到我所有的帖子。
*   在 LinkedIn 上加入我的职业网络。
*   在推特上关注我。
*   加入我的脸书主页。
*   最后，请在 dev.to 上关注我！