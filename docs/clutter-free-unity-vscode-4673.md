# 无杂波 Unity/VSCode

> 原文：<https://dev.to/adamkdean/clutter-free-unity-vscode-4673>

所以，现在是 2016 年。距离我上一篇帖子已经过去两个多月了。我最近太忙了，工作让我感觉很累，孩子也快出生了，我一直忙于现实生活，没有太多机会做其他事情。我已经决定我需要享受编程。我需要用我的能力做好事。所以我要做游戏。

我以前涉足过游戏开发，但它总是不受控制，这不是一件好事。能够享受某些东西的第一步是享受你的环境，对于我们开发者来说，这意味着我们的开发环境。我用的是 OS X，所以有时候和 90%的互联网有所不同。Unity 搭载了 MonoDevelop，这很公平，完成了它的工作，但是它很糟糕。幸运的是，VSCode 可以在 OS X 上运行，尽管我现在是一名皈依者，但自从 16 年前开始在 VB6 上使用 Visual Studio 以来，我一直很喜欢它。

一个叫 [@reapazor](http://twitter.com/reapazor) 的家伙为 Unity 写了一个 [VSCode 集成脚本，运行得非常好，但是我的 OCD 对出现在树形视图中的所有杂七杂八的文件感到抓狂。就是不好看。注意:我必须从资产商店安装 VSCode 才能正常工作。](https://github.com/dotBunny/VSCode)

[![Cluttered treeview](img/f9628a32f2f125d10f2e1bff113a128c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--obLtDHq---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/SCn3crd.png)

这可以通过在您的用户`settings.json`文件中删除一些文件/目录来解决(您可以使用`cmd` + `,`来访问)。如果您需要访问这些文件，只需将它们从例外列表中删除即可。

**更新**我刚刚意识到(25/01/16 14:39)vs code 插件实际上有一个选项可以帮你做到这一点，在首选项中，“写工作区设置”。我想当时我并没有意识到这一点，但这也是可行的。您所需要做的就是在`.vscode/settings.json`文件后面添加两行，以清除一些您可能不想看到的其他文件:

```
"**/*.csproj":true,
"**/*.sln":true 
```

Enter fullscreen mode Exit fullscreen mode

完整的列表在这篇文章的末尾。这会导致血压降低:

[![Lower blood pressure with Unity + VSCode](img/4faac8f1932c8d9dc535e62cf4d062b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bms_23O---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/0Dtf4ep.png)

嗯，希望今年我会发一些有趣的帖子。我将使用 C#进行游戏开发，在工作中使用 ES6，而且随着孩子的出生，我肯定会有很多与计算机无关的发现。

为美好的 2016 年干杯，这是你完整的`settings.json`档案:

```
{
    "files.exclude":
    {
        "**/.DS_Store":true,
        "**/.git":true,
        "**/.gitignore":true,
        "**/.gitmodules":true,
        "**/*.booproj":true,
        "**/*.pidb":true,
        "**/*.suo":true,
        "**/*.user":true,
        "**/*.userprefs":true,
        "**/*.unityproj":true,
        "**/*.dll":true,
        "**/*.exe":true,
        "**/*.pdf":true,
        "**/*.mid":true,
        "**/*.midi":true,
        "**/*.wav":true,
        "**/*.gif":true,
        "**/*.ico":true,
        "**/*.jpg":true,
        "**/*.jpeg":true,
        "**/*.png":true,
        "**/*.psd":true,
        "**/*.tga":true,
        "**/*.tif":true,
        "**/*.tiff":true,
        "**/*.3ds":true,
        "**/*.3DS":true,
        "**/*.fbx":true,
        "**/*.FBX":true,
        "**/*.lxo":true,
        "**/*.LXO":true,
        "**/*.ma":true,
        "**/*.MA":true,
        "**/*.obj":true,
        "**/*.OBJ":true,
        "**/*.asset":true,
        "**/*.cubemap":true,
        "**/*.flare":true,
        "**/*.mat":true,
        "**/*.meta":true,
        "**/*.prefab":true,
        "**/*.unity":true,
        "build/":true,
        "Build/":true,
        "Library/":true,
        "library/":true,
        "obj/":true,
        "Obj/":true,
        "ProjectSettings/":true,
        "temp/":true,
        "Temp/":true,
        "**/*.csproj":true,
        "**/*.sln":true
    }
} 
```

Enter fullscreen mode Exit fullscreen mode