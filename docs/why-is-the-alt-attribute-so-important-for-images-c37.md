# 为什么“alt”属性对图像如此重要？

> 原文：<https://dev.to/belhassen07/why-is-the-alt-attribute-so-important-for-images-c37>

也许这看起来很基本，但我记得当我写一个图像标签时，我常常忽略这个属性。出于以下原因，您不应该这样做:

# 图像不存在时显示【真基本】

有时你从另一个网站复制一个图片的链接，然后把它放在源属性(src)中，就像例子
中的这样

```
<img src="https://scontent.ftun3-1.fna.fbcdn.net/v/t1.0-

9/23844430_1595292810569743_8608995995175264766_n.jpg?

oh=4ec53eb36555985367848ef693bd824f&oe=5ABD4CDC" > 
```

Enter fullscreen mode Exit fullscreen mode

这是我的脸书简介的封面图片，我在我的网站上使用了它，然后我想从 Facebook 上删除它，所以我的网站也会删除它，它会给我这样的信息:

[![image not found](img/d5ce1f509e72dce5bc7edaaf88abdbe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jdm8HBqL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3drxlbd8t5ubut0x7t9e.PNG) 
用户甚至不知道丢失的是什么，所以我们需要像这样添加“alt”属性

```
<img src="https://scontent.ftun3-1.fna.fbcdn.net/v/t1.0-

9/23844430_1595292810569743_8608995995175264766_n.jpg?

oh=4ec53eb36555985367848ef693bd824f&oe=5ABD4CDC" 

alt="minimal design"> 
```

Enter fullscreen mode Exit fullscreen mode

这给出了:

[![minimal design](img/6984a5397a1a29968eac2ad1ecbdeffb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3QKY_4ei--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3fybhlhwlvjjyj83c50.PNG)

所以它不能救你的命，但至少用户知道隐藏了什么。

# 无障碍事宜

“网页可访问性意味着残疾人可以使用网络”
所以，我们在制作网站时应该在可访问性方面下功夫，在这个世界上提供平等真是太棒了。

alt 属性必须出现在图像标签中，并且它应该是描述性的，以便残障人士知道屏幕阅读器有什么。脸书使用人工智能来识别图像包含的内容，并将描述自动放入 alt 属性的值中。

例如，这是一年前我和两个朋友的照片:

[![with my friends](img/43307b8d540815800a5fef412328a68d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qeXsEWq1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scontent.ftun3-1.fna.fbcdn.net/v/t1.0-9/16473183_979309875534963_475963488166732354_n.jpg%3Foh%3Df12eabea3c13ec07fe4f152cb522ab8c%26oe%3D5AD202B3)

在 alt 属性中，我发现:“图像可能包含:3 个人，眼镜”。是的，眼镜是我的。这是图像上传后自动生成的，这很棒，每个人都可以知道里面有什么，如果你自己提供 alt，你这个人类，会更棒。

# 为了搜索引擎优化着想

搜索引擎喜欢 alt，这意味着他们想知道关于你的图像的信息，所以他们查看 alt 属性值；所以，如果你关心搜索引擎优化，添加 alt。在这个 youtube 播放列表课程中，你可以学到更多关于 SEO 的基本知识

## 所以是的，alt 很重要。