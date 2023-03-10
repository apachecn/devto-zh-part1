# Snapchat 过滤器:它们是如何工作的？

> 原文：<https://dev.to/aubreyarcangel/snapchat-filters-how-do-theywork-4c83>

* * *

每天大约有五次，我和 1.8 亿日常用户一起打开 Snapchat，与家人和朋友分享我生活中的小瞬间。(说实话，我几乎总是发我家狗狗的视频。)

[![](img/204086181ace1ad2167688f63ae4904b.png)T2】](http://www.instagram.com/beeandclara)

我每周都会浏览几次 Snapchat 的镜头(我们称之为 Snapchat 滤镜)，看看有没有什么新东西。

[![](img/e54476fe3943be8a231c4db1bc079fdd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kSHhH9Bo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5zeziturle4rt1zguqlo.jpg)

我是 Snapchat 的忠实粉丝，不仅因为我限制了脸书和 Instagram 的使用，还因为我从小就是照片技术的崇拜者。Snapchat 通过与朋友交换面部表情或让动画彩虹从你嘴里掉出来来娱乐的能力建立在长期的钦佩之上，并引起了我的兴趣。

[![](img/905951ab83ed2cb98d110a3ae2c41aa6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VbOpJJ0W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ny8rijek7dyu02maxee.jpg)

我在 2016 年开始使用 Snapchat，我想知道他们的过滤器背后的增强现实技术是如何工作的。由于我需要一个关于我的 Fullstack Academy stackathon 项目的想法，这是我深入了解幕后发生的事情的机会。这项技术来自于 2015 年被 Snapchat 收购的乌克兰科技公司 Looskery。

在深入 Snapchat 的技术之前，让我们从 30，000 英尺的高度来看一下计算机视觉的宇宙——这是计算机如何从你的相机接收数据来识别物体的。

#### **什么是计算机视觉？**

[![](img/0d857b7810abbab3dc0a0422d6bdc1a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XrnpzVmO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7rfpqy8zm471lpumlp5q.png) 
**[计算机视觉](https://en.wikipedia.org/wiki/Computer_vision)** 是一个跨学科领域，研究计算机如何翻译来自数字图像或视频的大量数据【研究如何让计算机获得高级理解】。从工程的角度来看，它寻求将人类视觉系统通常做的任务自动化。

计算机视觉的原因是:

*   脸书可以在照片中标记您的朋友；
*   你可以用手机存支票；
*   自动驾驶汽车可以避免撞到其他汽车或树木；和
*   你可以发你长着狗鼻子的照片。

#### 计算机是如何看到人脸的？

当计算机看一幅图像时，它只能看到 1 和 0。

[![](img/9c51ddcafd0fe63ce988875e25d0d140.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L8y0yCH3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzl81ytnem6374w98r7s.png)

所有人脸都有相似的特性:

*   鼻梁比两边要轻
*   眼部比脸颊上部更暗
*   眼窝比前额黑

[![](img/43b664254bd58dd7a9de28f44b83ad9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fl7NPpAb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ckgbjufpb1irtxvqntg.png)

使用 **[Viola-Jones 算法](https://en.wikipedia.org/wiki/Viola%E2%80%93Jones_object_detection_framework)** ，计算机扫描图像数据以检测亮暗像素区域之间的对比。当在图像的一个特定部分的像素数据中找到足够多的面部特征匹配时，计算机可以识别照片中的人脸。

[![](img/15b53f1523af806e2060de7a4ed8a46d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dCUPoC5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dc9b4hik4lmswx9r0pfb.png) 
该算法对于正面人脸工作良好，但是无法检测侧面人脸。

#### 【Snapchat 怎么在我脸上安了个狗鼻子？

现在让我们往回看，了解 Snapchat 如何使用该算法将其滤镜映射到您的面部。

对于计算机来说，在你的脸上放一个狗鼻子，它需要进行更复杂的计算来定位你的面部特征。这是通过使用一个 **[活动形状模型](https://link.springer.com/chapter/10.1007/978-3-642-54851-2_1)** 来完成的。依靠这种模型的计算机利用人类的手动标记，在成千上万张图像上显示边界和面部特征。
[![](img/3a979e6d47190c08a2519e0ad2a9ebb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NuvN-m3u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p7f48agfq7srbnxy6bif.JPG) 
这些面部数据随后被应用到手机摄像头拍摄的图像中，将其缩放到已经检测到你面部的区域。

[![](img/53c5c779d407d10f413d9b98282da1fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GExSbPjb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bq0jooirfe6emdm882cn.png) 
这个模型并不完美，但它有一个上唇的模板。它会在您的图像中寻找图案，并相应地进行调整以匹配它。

它还会考虑面部其他点的位置来创建一个 3D 遮罩，当来自相机的数据一帧一帧地出现时，它可以用于缩放、旋转和移动。

[![](img/acf8b7d222e6471ec51b963bdd334976.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4N0cgPIW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d5mplpxtxevmppmxh4vd.png) 
这个 3D 面具可以在你的面部特征移动时重新变形以适应它们(例如，眼睛或下巴)，添加配件，并在你张开嘴时设置彩虹变得栩栩如生。
[![](img/2f785858b7309c6354c168c78f1f4af3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Byy1nMj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cmx4ayywj5km0z2ccifn.JPG) 

#### 现在你是增强现实专家了。

现在你知道 Snapchat 是如何工作的了，你可以把这个话题作为你参加鸡尾酒会的开场白，或者在下一次家庭晚宴上给你另一半的父母留下深刻印象。

我希望这是有趣的，我喜欢阅读它，就像我喜欢做研究一样。开心抓拍。

* * *