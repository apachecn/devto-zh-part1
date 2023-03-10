# 重构 Django 中过度增长的通知类

> 原文：<https://dev.to/gabivoicu/refactoring-an-overgrown-notifications-class-in-django-4m4h>

拉杰什·阿巴拉在 [Unsplash](https://unsplash.com/photos/IBWIy0tZ2Vw) 上的照片

在使用相同的代码库一段时间后，开发人员开始注意到某些部分可以改进的方法，包括性能和代码质量。代码也经常随着时间的推移而发展，以跟上不断变化的需求。通常，这些变化可以被大大简化。在 [NextTier](https://nexttier.com/) 的 Django API 中的 notifications 类的情况下，两者都是正确的。

API 处理学生与其大学申请任务和辅导员的交互。学生可以找到要申请的大学和奖学金，参加考试以帮助指导他们走向职业生涯，并通过为每个大学量身定制的任务来跟踪他们的大学申请过程。辅导员可以直接通过消息和验证他们的仪表板来跟踪学生的进步，也可以通过通知系统来间接跟踪。

通知发送类在过去两年中经历了许多变化，需要做越来越多的工作来跟上不断发展的需求。由于我们现在对通知系统的需求有了更好的了解，并且开始感觉到性能下降，是时候对通知发送类 SendNotificationMixin 进行无情的分析了。

#### 初始状态

这是重构之前通知发送类的样子: