# 将 SQL 数据库备份到 Azure

> 原文：<https://dev.to/funkysi1701/backing-up-sql-databases-to-azure-noe>

我最近读了 Pinal Dave 的一篇博文，内容是关于如何直接备份到 Azure 存储。他描述的过程仅适用于 SQL Server 2014 或更高版本。

我不会详细介绍这种方法，因为 Pinal 比我更好地描述了它，但它的基本要求是设置凭据，然后运行包含 Azure 上存储容器 URL 的备份命令。

不幸的是，我运行的是 SQL Server 2005，所以这个过程不适合我，但它确实让我开始思考有哪些方法可供我使用。

接下来我尝试的是[微软 SQL Server 备份到微软 Azure 工具](https://www.microsoft.com/en-us/download/details.aspx?id=40740&WT.mc_id=Blog_SQL_Announce_DI)。不幸的是，我没有得到这个工具在我的设置正确工作。然而，它听起来像一个灵活的工具，允许压缩和加密您的备份文件。这个工具将你的备份文件重定向到你的 Azure 存储，所以即使我让它正常工作，它也不是一个理想的解决方案，因为我也想要我的备份文件的本地副本。

在这之后，我开始重新审视 powershell。随着我最近在 powershell 上的成功，我知道如何连接到我的 Azure 帐户，所以我所需要的只是从我的服务器上复制一个文件到 Azure。

```
Get-ChildItem *.bak -File -Recurse | Set-AzureStorageBlobContent -Container $DestContainer -Force 
```

Enter fullscreen mode Exit fullscreen mode

该命令获取一个目录中的所有备份文件(recurse 开关还会查看子目录)，然后通过管道将它们传递给 Set-AzureStorageBlobContent 命令。这个命令将它们上传到$DestContainer 中定义的存储容器。我已经添加了强制开关，这样它将替换 Azure 上任何同名的文件。

我最近几天才开始使用这个脚本，但到目前为止，它运行得很好。现在，如果我完全丢失了办公室的所有数据，我可以使用 Azure 上保存的数据从任何其他位置恢复。对我的灾难恢复策略的重大改进。