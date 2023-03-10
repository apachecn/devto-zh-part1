# 将 GitHub pull-request 合并到本地存储库中的小技巧

> 原文：<https://dev.to/mattn/small-tips-to-merge-github-pull-request-into-your-local-repository-8m3>

**PRO 提示轻松将 GitHub 拉请求合并到本地存储库中。**

你是如何在本地库上签出 GitHub 拉请求的？

```
$ git fetch origin pull/ID/head:BRANCHNAME 
```

Enter fullscreen mode Exit fullscreen mode

*ID 是拉取请求号，BRANCHNAME 是您将创建的本地分行的名称*

像这样？明天你还记得这个格式吗？ [![thinking](img/e2102c68fe0439d9e0f83d617e2355da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--optZ3zpT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://practicaldev-herokuapp-com.freetls.fastly.net/assets/emoji/emoji-one-thinking-0764915e60a80115ee69375d403cb7a8d02ec790391a4a9f181bfbd8ba8256dd.png) 也许我没有。

幸运的是，GitHub 通过在 URL 中添加文件扩展名来提供一些额外的内容。参见[差异和补丁媒体类型](https://developer.github.com/changes/2012-12-10-Diff-and-patch-media-types/)。例如，将`.diff`放在[拉取请求](https://github.com/vim/vim/pull/2070) URL:
的末尾

```
"https://github.com/vim/vim/pull/2070" + ".diff" 
```

Enter fullscreen mode Exit fullscreen mode

[https://github.com/vim/vim/pull/2070.diff](https://github.com/vim/vim/pull/2070.diff)

该请求将被重定向到`unified diff`页面。如果把`.patch`放到[拉取请求](https://github.com/vim/vim/pull/2070)的 URL。

```
"https://github.com/vim/vim/pull/2070" + ".patch" 
```

Enter fullscreen mode Exit fullscreen mode

[https://github.com/vim/vim/pull/2070.patch](https://github.com/vim/vim/pull/2070.patch)

这将被重定向到补丁文件。所以你可以试着应用如下的差异。

```
$ curl -sL https://github.com/vim/vim/pull/2070.diff | patch -p1 
```

Enter fullscreen mode Exit fullscreen mode

另外，如果您想将拉式请求合并到您的本地存储库中，

```
$ git checkout -b pr-2070
$ curl -sL https://github.com/vim/vim/pull/2070.patch | git am 
```

Enter fullscreen mode Exit fullscreen mode

做测试并推进主分支。

```
$ git checkout master
$ git merge pr-2070
$ git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

你能看到吗？一点都不难。