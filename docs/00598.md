# 蒂尔:詹金斯用卷发把球传给斯莱克

> 原文：<https://dev.to/bradymholt/til-post-to-slack-from-jenkins-with-curl-adg>

我最近需要从 Jenkins 版本发布到 Slack，但是因为 Jenkins 的 Slack 插件没有提供细粒度的配置，所以我在后期构建脚本中使用了 curl 脚本。

首先，我在 Slack 中创建了一个新的 [Jenkins CI 配置](https://my.slack.com/services/new/jenkins-ci)，并记下了它给我的令牌(下面的`$SLACK_API_TOKEN`)。然后，这是我用的 curl 脚本:

```
if ["$GIT_BRANCH" = "origin/master"]
then
curl -X POST -H "Content-Type: application/json" \
 -d '{"text":"'"$JOB_NAME"' - #'"$BUILD_NUMBER"' Failed on '"$GIT_BRANCH"' branch - '"$BUILD_URL"'"}' \
 "https://[domain].slack.com/services/hooks/jenkins-ci?token=$SLACK_API_TOKEN"
fi 
```

Enter fullscreen mode Exit fullscreen mode