# 从系统创建 EML 文件。邮件消息

> 原文：<https://dev.to/robinvanderknaap/create-eml-file-from-system-net-mail-mailmessage-4gef>

> 在我从事的一个项目中，我需要从系统中存在的标准 MailMessage 类创建一个 EML 文件。邮件命名空间。我看到一篇文章,描述了如何在 MailMessage 类上扩展保存功能。使用同样的技术，我创建了一个扩展方法，可以将邮件消息类保存为 EML。

在您的解决方案中使用这种扩展方法，您可以创建一个 EML 文件，如下所示: