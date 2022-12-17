# 检查多个服务的名称可用性

> 原文：<https://dev.to/sethmlarson/check-name-availability-for-multiple-services>

大家好！这和我的大多数帖子有点不同，因为它很短。我正在为一个开源项目寻找名字，我发现非常令人沮丧的是，我寻找的名字在一个平台上可用，而在另一个平台上不可用。这导致了大量的时间被投入到寻找一个好名字上，而这些时间本来是可以用来编程的。

为了帮助我完成这个任务，我发明了一个工具，叫做 Avail，它从命令行运行，输出你给它起的名字是否在各种平台上可用，包括 GitHub、Dockerhub、npm、Twitter。网址缩写可用，等等！

你可以像这样通过`pip`安装它:

`python -m pip install avail`

然后像这样使用它:

`avail [NAME]`或者像这样`python -m avail [NAME]`

该工具的[源代码托管在 GitHub](https://github.com/SethMichaelLarson/avail) 上，并获得 Apache-2.0 许可。欢迎对更多服务提出请求和建议！

我希望这个工具能够帮助你，哪怕只是节省几分钟的搜索时间。