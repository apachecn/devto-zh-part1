# Slack 中的 SSH 登录通知

> 原文：<https://dev.to/pullrequest/ssh-login-notifications-in-slack-1mac>

知道谁登录了您项目周围的服务器是很方便的。Slack 结合 pam.d 提供了一种很好的方式来实现这一点。

我们假设您使用基于 CentOS 的操作系统进行定位，但这应该可以在任何支持 pam.d 的*nix 操作系统上运行。

##### 1。在 Slack 中添加传入的 webhook 导航至:

```
https://YOUR_DOMAIN.slack.com/apps/manage/custom-integrations 
```

我们建议将景点命名为易于识别的名称；这样以后就不会被删除了。

确保从结果页面中复制 Webhook URL。

##### 2。向您的服务器添加 SSH 脚本

将可执行文件(chmod+x)添加到`/etc/ssh/scripts/sshnotify.sh`

(*注意*确保将`<YOUR SLACKWEBHOOK>`替换为步骤 1 中的 URL，将`#channel`替换为您想要通知的频道)

```
if [ "$PAM_TYPE" != "close_session" ]; then url="<YOUR SLACK WEBHOOK>"
        channel="#channel"
        host="$(hostname)"
        content="\"attachments\": [ { \"mrkdwn_in\": [\"text\", \"fallback\"], \"fallback\": \"SSH login: $PAM_USER connected to \`$host\`\", \"text\": \"SSH login to \`$host\`\", \"fields\": [ { \"title\": \"User\", \"value\": \"$PAM_USER\", \"short\": true }, { \"title\": \"IP Address\", \"value\": \"$PAM_RHOST\", \"short\": true } ], \"color\": \"#F35A00\" } ]"
        curl -X POST --data-urlencode "payload={\"channel\": \"$channel\", \"mrkdwn\": true, \"username\": \"SSH Notifications\", $content, \"icon_emoji\": \":inbox-tray:\"}" "$url" &
fi exit 
```

##### 3。将脚本添加到您的 pam.d 中

```
sudo echo "session optional pam_exec.so seteuid /etc/ssh/scripts/sshnotify.sh" >> /etc/pam.d/sshd 
```

#### 4。验证安装

注销并重新登录到您的邮箱，以验证通知是否到达您选择的频道。