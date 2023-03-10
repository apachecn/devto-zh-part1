# 将文件移动到 blob 存储中

> 原文：<https://dev.to/funkysi1701/moving-files-into-blob-storage-5136>

我们正在将我们公司的网站迁移到 Azure 平台上。挑战之一是将图像文件从网站项目移到 blob 存储中。本周我已经移动了 15 万个。

我一直强调的一点是，你的源代码不应该包含数据。如果每次部署时都这样，您需要考虑这些映像的位置，并确保不会覆盖或丢失任何映像。不言而喻，几兆字节的部署比几百兆字节的部署要快得多。

Azure blob 存储还为您提供了跨多个数据中心分布存储的优势，这对于服务器上的传统文件来说是不可能的。

现在我们已经确定这是一个好主意，让我们看看如何移动大量数据。在我的例子中，所有文件名都存储在一个 SQL 数据库中，所以行动计划是简单地遍历数据库中的文件，从当前存储(本地或其他云存储)下载，上传到 Azure，然后整理。由于图像的数量，我将更新数据库，并在文件处理完成时进行标记，这样我可以在几天内完成移动。

这是我的代码

。要点表{ margin-bottom:0；}

首先，我在我网站的根目录下创建一个 tmp 文件夹，如果它不存在的话，用来临时存储我的图片。

然后，我使用一个实体框架模型来查询没有被移动的数据库，并使用 take()方法来限制我处理的结果的数量。(我一直是一次传 1000 个)

然后，我对所有这些文件使用 foreach 循环来执行以下操作。

1.  如果存储在数据库中的文件名变量实际上不是一个文件名而是一个文件路径，请注意，您将不得不分割文件名和文件路径，我在这里没有包括代码。
2.  从原始 url 下载文件并保存到临时文件夹中
3.  上传到 Azure
4.  删除临时文件
5.  更新数据库，给出成功或失败

foreach 完成后，我提交数据库更改并删除临时文件夹。我相信肯定有其他方法来完成这种转移，但这是快速而容易的设置，现在我在 Azure 存储中有所有文件的副本，所以我可以测试我的网站的其他问题。

关于如何调度这段代码的最后一个技巧。我从一个 MVC 控制器中调用了上面的代码，然后编写了一个 Azure 函数来按时间表调用这个代码。