# 有助于。假人网

> 原文：<https://dev.to/rionmonster/contributing-to-net-for-dummies>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

在过去的几个月里，在我仅有的一点空闲时间里，我设法向各种各样的。NET 相关的存储库(如 [coreclr](https://github.com/dotnet/coreclr) 、 [corefx](https://github.com/dotnet/corefx) 、 [docs](https://github.com/aspnet/doc) 等)。)令人难以置信的是，他们被录取了。

现在，对一些人来说，这似乎没什么大不了的。但对我来说，这是一个相当大的问题，所以我想我应该花一些时间来阐述这个过程本身，分享我的经验，并希望消除一些可能阻碍你做出贡献的神话或误解。网也是。

希望你会发现，你不需要成为一个天才或设计一个更好的排序算法来做出有意义的贡献。NET 作为一个整体。

## 消除一些神话和先入为主的观念

我将公开承认，在浏览可用的网站库之前，我受到了严重的威胁，但没有任何真正合理的理由。似乎我已经围绕这些项目建立了很多神话，这些神话可能会阻碍我做出贡献，例如:

*   我需要成为一个编码向导，所有的开放问题都超出了我的能力范围。
*   所有我能处理的简单问题都被拿走了。
*   由于这一切都是公开的，我将被暴露为一个垃圾开发者(见[冒名顶替综合症](https://en.wikipedia.org/wiki/Impostor_syndrome))。
*   我从未做过如此大规模的项目，我很可能会把它搞砸。

这些只是我在踏上旅程之前在心里告诉自己的一些事情。我很快发现:这些都不是真的。

让我们逐一解决，好吗？

## 神话#1:我需要成为一名编码向导，所有公开的问题都是我力所不及的。

[![something something ben adams](img/ed5f82ca6c220ce2d9633436cad26f3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LKuIsx7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rion.io/conteimg/2017/04/ben-adams-question-makr.jpg)

*这里所有的*都是关键词。

在所有的网络仓库中，都有大量“待价而沽”的问题。其中一些是严峻的挑战，需要投入大量的时间、精力、天赋和思想。这些壮举通常不会单独解决，并经历来自双方的反馈和批评。NET 团队和社区。

对于每一个向导级的问题，还有无数其他的内务相关的问题，大多数开发人员都可以解决。最好的部分是，他们通常只需要一点点的努力。

让我们看一下我对. NET 相关库的第一个贡献。

这不是一个公开的问题，而是一天我在阅读文档时偶然发现的。我注意到示例中的几个变量具有以下格式，这看起来很奇怪:

```
 // static public doesn't look right, does it?
static public Type Foo { get; set; } 
```

这直接违背了[。NET 框架编码指南](https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/coding-style.md)，它声明“可见性修饰符应该总是第一个出现的修饰符”。这意味着我只需要在存储库中进行筛选，找到这种差异的任何其他实例，并将前面的示例更改为:

```
 language-csharp 
```

//啊！好多了！
公共静态类型 Foo { get 设置；}

```
 That's it! 

I didn't have to invent a new algorithm, reduce any allocations, or anything. It's housekeeping, but it's something that needs to be done and does make a difference in the long run.

The entire process took place over a lunch break. I forked the repository, make the changes, submitted the PR, and it was merged all within a matter of hours. What a great feeling!

##Myth #2: All of the easy issues that I could handle are taken.

![low hanging fruit](https://rion.io/conteimg/2017/04/the-easy-ones.jpg)

The low-hanging fruit is often the quickest to be picked, and GitHub issues are no exception. 

A while back, it may have been tough to find an "easy" issue to get your feet wet before it was snatched up. This isn't really the case anymore as the .NET folks have created a simple way to find your first issue to work on: **the 'easy' label**.

> The 'easy' label is generally reserved for first-time contributors to the framework and it allows them to get their feet wet.

If there's an issue that you want to work on, simply jump in and ask to be assigned to it, and then you can get started. The .NET team members are incredibly helpful at answering questions, and generally don't mind walking you through the process. 

So if you _want_ to contribute, consider going to one of the popular .NET-related repositories and searching for the following under the Issues area: 
```

是:问题是:开放标签:容易标签:待价而沽

```
 This will filter all of the available issues that are currently opened and designated as "beginner friendly" (i.e. they are both up for grabs and should be fairly easy):

![current open easy and up for grabs tags](https://rion.io/conteimg/2017/04/this-could-be-you.PNG)

The general rule of thumb has been to reserve many of these types of issues for first time contributors, so this is probably a great starting point if you want to dive in.

There are more and more added each day, but they do tend to go fast. So you may want to keep an eye out for something that piques your interest, and stake your claim when you get the chance.

##Myth #3: Since this is all in the open, I will be exposed as a crap developer.

![family guy phoney](https://rion.io/conteimg/2017/04/big-fat-phony-developer-1.jpg)

If you have done anything related to open-source, you can probably skip this section as you already know what it feels like to have your code viewed by potentially hundreds of thousands of people a day.

If you don't, then you might be a little nervous. You might not be incredibly familiar with the process, the code base, or even things like git. Or you just might be afraid that people will judge and criticize your code to the point of embarrassment. 

Well, don't be. 

I know that's easy to say, but everyone that you will be working with throughout the process is there to help you (i.e. you are not going to be alone during this process).

Each of the major repositories has [more documentation that you could imagine](https://github.com/dotnet/coreclr/tree/master/Documentation) a wide range of topics such as:

 - [Suggested Contribution Workflow](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/contributing-workflow.md#suggested-workflow)
 - [Coding Style Guidelines](https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/coding-style.md)
 - [Formatting Your Commit Messages](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/contributing-workflow.md#commit-messages)
 - [How the Project CI Works](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/contributing-workflow.md#pr---ci-process)
 - [Building Instructions](https://github.com/dotnet/coreclr/tree/master/Documentation/building) 
 - [Performance Testing Guidelines](https://github.com/dotnet/corefx/blob/master/Documentation/project-docs/performance-tests.md)
 - Much, much, more.

In addition to these topics, the teams have been working hard on creating docs specifically tailored for new and first time contributors:

 - [New Contributor Documentation](https://github.com/dotnet/corefx/wiki/New-contributor-Docs#main-page-section-content)

Overall, the documentation will vary depending on which repository you are in and they are probably better documented that most projects you may have worked on, so that should ease some of the tension.

Once you actually submit a PR, a team member will review your commits and provide any feedback, which I've found to be a fairly pleasant experience. You might have a few nits here and there with minor changes or the reviewer may offer alternative approaches or recommendations to solving the problem (this commonly occurs in areas involving performance). 

In my personal experience, the team was always extremely quick to react, and I felt that we were always on the same page and working _together_ as opposed to him simply critiquing my code.

##Myth #4: I've never worked on a project of this scale, I'll probably screw it up.

![something big](https://rion.io/conteimg/2017/04/uh-oh.PNG)

Let's get this out of the way: some of these projects are huge, and likely _way_ larger than most things that you have probably encountered before. As such, you are going to need to work with them a bit differently.

When I first forked projects like the coreclr and corefx, I will admit that they were a bit intimidating. Due to their sheer size and scale, they aren't terribly Visual Studio friendly, at least from a building and testing perspective. Thankfully, there is [documentation available for how to go about building these tests within each of the repositories](https://github.com/dotnet/coreclr/blob/master/Documentation/building/windows-instructions.md). 

Basically, you'll need to become with running the available building and test scripts from the command line. After building the projects, you should be able to open them up and edit them using Visual Studio, but keep that command prompt open as you'll use it frequently.

Testing and debugging are also going to be done predominately from the command-line as Visual Studio's Test Explorer will melt if it tried to run all of these tests. Thankfully, [there's extensive documentation for that as well](https://github.com/dotnet/corefx/blob/master/Documentation/project-docs/developer-guide.md#build-and-run-tests).

Getting acclimated to this environment can probably be one of the largest learning curves in this entire process, but it's something that you'll only need to do once. Another thing worth noting is that due to the scale of these projects, building can be _slow_, so make sure you have something to do while you wait.

##How to Contribute

Hopefully, the dispelling of some of these myths have built your confidence up enough to consider making a contribution. If so, here's what you'll need to do:

1\. **Find an issue that you want to work on.** Consider either searching one of the popular dotnet repositories using the [easy/up-for-grabs labels](is:issue is:open label:easy label:up-for-grabs ) as mentioned earlier, or try out an aggregate site like [Up For Grabs](http://up-for-grabs.net/#/). 
2\. **Sign a Contribution License Agreement.** In order to work and actively contribute to a .NET Foundation project, you'll need to agree to the terms of the CLA. This will also require you to set up two-factor authentication for your GitHub account.
2\. **Fork the Repository.** In order to work on a particular issue, you'll need to be working on your own fork of the project, which you'll eventually merge back in once your changes are complete.
3\. **Building and Running.** Building some of these projects isn't likely going to be the same as running your average project within Visual Studio. Thankfully, there's a wide range of [documentation for building and running](https://github.com/dotnet/coreclr/tree/master/Documentation/building) these  projects, which can get you pointed in the right direction.
4\. **Make Your Changes.** After you have the solution running, make your changes to apply the fix. Don't be afraid to ask any questions on GitHub if they arise throughout this process.
5\. **Write Tests (As Needed).** Some changes will require that a set of unit tests are written for any new or altered functionality. Any team member that is assigned to your issue will likely let you know about this, and about where to go about writing the tests themselves.
6\. **Submit Your Pull Request.** Now that all of your changes are made and they appear to be working, you can submit your pull request. It will undergo a review process and a team member will provide any feedback as needed.
7\. **LGTM!** "Looks good to me!" This will usually be one of the last things that you see on your PR, and it's an indication that you are about to officially become a contributor to .NET. Congrats! 

##In Closing

My experiences with contributing to .NET have been nothing but stellar. It was great to have a chance to collaborate with members of the .NET team and the community towards improving the ecosystem, even in the smallest of ways. Simply knowing how a minor performance improvement that saves milliseconds here or there could save so much time on such a large scale is really rewarding.

I know that this experience certainly struck a chord with me, and in the weeks following my initial merged pull request, I've probably rattled off 10+ additional ones, and I look forward to contributing more in the future.

I hope that this post helped ease your mind if you thought that you wouldn't have anything to contribute, or that it would simply be beyond your skill-set or talent. There's something out there for everyone, so go out there and get that First-Time Contributor badge and help make .NET a better place. 
```