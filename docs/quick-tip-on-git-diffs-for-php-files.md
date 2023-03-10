# PHP 文件 Git 差异的快速技巧

> 原文：<https://dev.to/phprio/quick-tip-on-git-diffs-for-php-files>

每当 PHP 开发人员在命令行上运行`git diff`时，就会有一只小猫死去:

> [![Badly-hunked PHP diff](img/11a26fc5c53e9baf8bfe297a538f18ae.png "Badly-hunked PHP diff")](https://res.cloudinary.com/practicaldev/image/fetch/s--452IPm89--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AyE143yhomWgCeEdzer6Gmw.png) 
> *见鬼不！这段代码在一个方法中，而不是在类中:(*

那些片段被称为块头——因为 diff 的每一部分都被称为块。不幸的是，git 太笨了，无法根据 PHP 语法判断出 PHP 文件应该是不同的，所以您需要通过对 git 属性使用[规则来帮它一把。](https://git-scm.com/docs/gitattributes#__code_diff_code)

不幸的是(2)，git 属性不像 config 选项那样容易在用户范围内配置(没有`git global`命令)，因此这里有一个提示:

1.  如果文件夹不存在，则创建该文件夹:`~/.config/git`(或`$XDG_CONFIG_HOME`中的任何内容)
2.  在`~/.config/git/attributes`中写入这个:`*.php diff=php`
3.  保存并区分开来

> *这也可以通过在`.gitattributes`文件中添加相同的文本来为每个项目进行配置。*

> [![Well-hunked PHP diff](img/0ffab8308289c3b45243dc84dbe24609.png "Well-hunked PHP diff")](https://res.cloudinary.com/practicaldev/image/fetch/s--Oibp4xGC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AL5EKHPXHoTbO4j6CY0sxBQ.png) 
> *原来如此！PHP 文件上的一个不错的标题*

很奇怪为什么 Git 不能通过文件扩展名理解开箱即用的语法，因为有预先设置来理解几种语法(如 TeX、Fortran、HTML 等)。真不敢相信

* * *

#### 参考文献

*   [http://stack overflow . com/questions/28026767/where-should-I-place-my-global-git attributes-file](http://stackoverflow.com/questions/28026767/where-should-i-place-my-global-gitattributes-file)