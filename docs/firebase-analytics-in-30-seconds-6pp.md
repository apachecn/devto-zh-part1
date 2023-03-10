# 30 秒内完成 Firebase 分析

> 原文：<https://dev.to/swyx/firebase-analytics-in-30-seconds-6pp>

[Stacy Devino](https://twitter.com/doesitpew) 今天做了一个关于 Firebase Analytics 的精彩演讲([幻灯片在此](https://docs.google.com/presentation/d/1akpEegWCXHkMjU-GBS-3yZ1wHvO7YATU_zHP0kuqDhU/edit#slide=id.p)， [github 在此](https://github.com/childofthehorn/AndroidFirebaseAnalyticsWorkshop)， [youtube 介绍在此](https://www.youtube.com/watch?v=8iZpH7O6zXo))。有很多顿悟的时刻:

1.  整合 Firebase SDK ( [Android](https://firebase.google.com/docs/android/setup) 和 [iOS](https://firebase.google.com/docs/ios/setup) 和 [web](https://firebase.google.com/docs/web/setup) )就几行代码！

有**个预先配置的**个事件(发送标准的东西，比如登录尝试):

```
Bundle bundle = new Bundle();
bundle.putString(FirebaseAnalytics.Param.ITEM_NAME, name);
bundle.putString(FirebaseAnalytics.Param.CONTENT_TYPE, "image");
mFirebaseAnalytics.logEvent(
    FirebaseAnalytics.Event.SELECT_CONTENT, bundle); 
```

Enter fullscreen mode Exit fullscreen mode

还有**自定义**事件(想发什么就发什么)

```
Bundle bundle = new Bundle();
bundle.putString("image_name", name);
mFirebaseAnalytics.logEvent("profile_image_select", bundle); 
```

Enter fullscreen mode Exit fullscreen mode

1.  用户分组(这样您就可以研究不同用户组的不同行为)同样只有几行代码:

```
mFirebaseAnalytics = FirebaseAnalytics.getInstance(this);
mFirebaseAnalytics.setUserProperty("preferred_pet", 
    petSelector);
mFirebaseAnalytics.setUserId("userIdString"); 
```

Enter fullscreen mode Exit fullscreen mode

1.  一旦你的应用程序发布，前往[仪表盘](https://console.firebase.google.com)查看见解！

2.  在仪表盘中， [Streamview](https://support.google.com/firebase/answer/7229836?hl=en) 特别棒，因为您可以按顺序查看用户操作的“点击流”,并可视化他们在您的应用上正在做(或未能做)什么！

3.  上面的一切都是完全免费的，你可以无限制的使用。为了进行真正大规模的数据处理，你必须将数据从 [Firebase Analytics 导出到 Google BigQuery](https://cloud.google.com/solutions/mobile/mobile-firebase-analytics-big-query) 。在那里你可以用普通的 SQL 查询你所有的历史用户数据！

BigQuery 是一个免费增值产品，所以你必须有一张信用卡，但对于大多数用例来说，财务风险很低:“[实际上，大多数人不会在第一年花光免费的 GCP 美元。20k 用户和正常使用<【5 美元/月](https://youtu.be/Ki_F6VCOtXU?t=86)。

在我担任产品经理期间，我曾使用过一些分析产品，这看起来非常容易安装和理解，我期待着在我所有的 [Firebase 托管的](https://firebase.google.com/docs/hosting/)和[无服务器功能](https://firebase.google.com/docs/functions/)项目中部署它！