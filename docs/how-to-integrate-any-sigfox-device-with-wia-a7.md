# 如何将任何 Sigfox 设备与 Wia 集成

> 原文：<https://dev.to/conalllaverty/how-to-integrate-any-sigfox-device-with-wia-a7>

嘿伙计们！
我们刚刚发布了一种全新的方式，可以轻松地将你的任何 [Sigfox](https://www.sigfox.com) 设备与 [Wia](https://www.wia.io) 云平台集成。下面是你需要遵循的步骤。

另外，如果你想在 iOS 和 Android 上看到你的设备，请在这里预先注册我们的测试版。

### 创建您的 Wia 帐户

如果您还没有，请[点击此处](https://dashboard.wia.io/signup)注册。

### 设置一个空格

登录后，为您的设备设置一个新的空间。我要叫我的`My Sigfox Devices`。当您的共享空间准备就绪时，您应该会看到如下所示的屏幕。

[![screencapture-dashboard-wia-io-spaces-created-1513170238630](img/0939e642f5f0c8da9435ff041881059b.png)T2】](//images.contentful.com/i5evrz7h2uo7/14Hdt1PmZkkgwO2cgaki6M/5773ead70b88b9d869412006d9a5ec0b/screencapture-dashboard-wia-io-spaces-created-1513170238630.png)

### 开始设置积分

点击左上角的空间名称，打开空间下拉菜单，然后点击`Settings`。在侧边栏中选择`Integrations`。

[![screencapture-dashboard-wia-io-spaces-spc FO431hY1-settings-integrations-1513170427867](img/d8a22f9ffbabc594eab7612a0c330f1e.png)T2】](//images.contentful.com/i5evrz7h2uo7/268l3mg23qUAW4qkGC4imy/059055b9823a3307bcf9e5369f5b1138/screencapture-dashboard-wia-io-spaces-spc_FO431hY1-settings-integrations-1513170427867.png)

点击`Setup`开始设置集成。

[![screencapture-dashboard-wia-io-spaces-spc FO431hY1-settings-integrations-sigfox-1513170504931](img/15422e96e7dda5cbeb974c4f547198bb.png)T2】](//images.contentful.com/i5evrz7h2uo7/2KeACAm7ny2iCcUgC4Ekwu/6136f6976e8852e4118cccb6654d0af8/screencapture-dashboard-wia-io-spaces-spc_FO431hY1-settings-integrations-sigfox-1513170504931.png)

### 如何获取 Sigfox API 密钥

*   转到 [Sigfox 仪表盘](https://backend.sigfox.com)
*   转到组>`Your Group Name` > API 访问
*   要生成 API 密钥，请按照下列步骤操作:
    *   点击右上角的`New`
    *   输入 Wia 作为名称
    *   `Add DEVICES_MESSAGES[R]`选项和生成键

[![screencapture-backend-sigfox-api-user-59b91a779058c21b41b8d96c-list-1513171158652](img/29823ddf7096bf23ff29dd408debad66.png)T2】](//images.contentful.com/i5evrz7h2uo7/2fUJXoshJSYY4gEwEYOYUO/c77621f95ef4d816238000fb11bfcd81/screencapture-backend-sigfox-api-user-59b91a779058c21b41b8d96c-list-1513171158652.png)

将登录和密码复制并粘贴到 Wia 的输入字段中，然后点击`Create Integration`。

[![screencapture-dashboard-wia-io-spaces-spc FO431hY1-settings-integrations-sigfox-1513170596442](img/0f002cc98858b5011e26be21eb764250.png)T2】](//images.contentful.com/i5evrz7h2uo7/2nnSURxvEkgawoiOuwwUOu/07cfb4f2096d8cae1d805de2a0e94f59/screencapture-dashboard-wia-io-spaces-spc_FO431hY1-settings-integrations-sigfox-1513170596442.png)

### 如何设置你的 Sigfox 回拨

既然您已经设置了集成。让我们为您的设备设置回拨。

*   转到 [Sigfox 仪表盘](https://backend.sigfox.com)
*   选择设备类型，然后选择要集成的设备类型的名称
*   在左侧，选择回调
    *   注意:如果当前有任何 Wia 回调，请删除它们
*   单击左上角的“新建”以创建新的回调
*   选择自定义回拨，并在下面输入参数:
    *   类型:`DATA` `UPLINK`
    *   频道:`URL`
    *   Url 模式:`https://api.wia.io/v1/events`
    *   使用 HTTP 方法:`POST`
    *   头球
    *   表头:`Authorization`
    *   值:`Bearer [YOUR INBOUND KEY]`
        *   注:参见下面以`ik_`开头的入站关键字
    *   内容类型:`application/json`
    *   正文:从[复制并粘贴文本到](https://gist.github.com/conalllaverty/49ba5a6cdefd853a43662d0349b30338)
    *   单击确定

[![screencapture-backend-sigfox-devicetype-59c178829e93a154e9374013-callbacks-5a2eb8043c87897b18442712-edit-1513171179662](img/20c707ec9601fd4648389a2621f0d38d.png)T2】](//images.contentful.com/i5evrz7h2uo7/5bXhqFyVK8wiS2OCukseQw/e40cefee0625f7990c16b2aa0ef3acc2/screencapture-backend-sigfox-devicetype-59c178829e93a154e9374013-callbacks-5a2eb8043c87897b18442712-edit-1513171179662.png)

开始从该设备类型的任何 Sigfox 设备发送数据，它们将开始出现在 Wia 中。

### 邀请他人加入您的共享空间(可选)

您现在可以与其他用户共享您的共享空间。点击左上角的空间名称，转到`Settings`，然后点击侧边栏中的`Users`。输入您想邀请的人的电子邮件地址。

那都是乡亲们！

附言:如果您在设置方面需要任何帮助，[发微博给我们](https://twitter.com/wiaio)或发电子邮件给 [support@wia.io](//mailto:support@wia.io)

*参考*

[Sigfox](https://www.sigfox.com) 是一家法国物联网(IoT)公司，成立于 2009 年，建立无线网络连接电表、智能手表和洗衣机等低能耗窄带对象，这些对象需要持续开启并发射少量数据。