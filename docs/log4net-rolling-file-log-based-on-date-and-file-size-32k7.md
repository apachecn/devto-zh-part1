# Log4net 基于日期和文件大小的滚动文件日志

> 原文：<https://dev.to/rbipin/log4net-rolling-file-log-based-on-date-and-file-size-32k7>

[![Log4net rolling file log based on date and file size](img/992f5c4c93011cde496f9c965fcaf0a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kP-dwNtt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.bipinr.com/conteimg/wordpress/2017/08/ApacheSoftwareFoundation.png)

日志记录是任何应用程序的重要组成部分，开发人员花费大量时间编写日志代码。日志记录很重要，因为这有助于我们创建应用程序中所有事件的跟踪，进而有助于调试、识别和分析问题和错误。Log4net 是一个开源框架，用于记录应用程序的输出。应用程序框架是在 Apache 版许可下发布的。Log4net 提供了多种类型的日志技术。

我们今天要讨论的是如何配置 log4net 来满足我想在我的一个项目中实现的需求？要求是实现一个基于日期和文件大小的滚动日志系统。也就是说，我想实现一个文件日志，这将每天创建一个新的日志文件，如果文件大小扩展到一定的大小，它应该创建一个新的文件。

为了实现这一点，我对 Appender 的类型使用了“ *RollingFileAppender* ”。我们来讨论一下配置中的重要参数。

*   *preservelogfilename extension*–该参数指示在滚动过程中是否应该保留日志文件的文件扩展名。
*   *滚动方式*–用于日志的滚动标准，我使用了*复合*来基于日志的日期和大小进行滚动。
*   *Date pattern*–附加到日志文件名的日期模式，我将其配置为 *_MMddyyyy* 。
*   *maxSizeRollBackups*–在滚动期间删除之前要保留的日志文件的最大数量。
*   *Maximum filesize*–日志文件滚动到新文件之前的最大大小。
*   *staticLogFileName*–指示是否总是登录同一个文件，我将此设置为 *true* 。

让组件从配置文件中读取参数。我们需要添加下面的代码。

```
XmlConfigurator.Configure();
or
[assembly: log4net.Config.XmlConfigurator(Watch=false)] 
```

Enter fullscreen mode Exit fullscreen mode

配置中使用的参数如下所示。

```
<log4net>
  <appender name="RollingLogFile" type="log4net.Appender.RollingFileAppender">
    <file value="log.log" />
    <PreserveLogFileNameExtension value="true"/>
    <appendToFile value="false" />
    <rollingStyle value="Composite"/>
    <datePattern value="_MMddyyyy"/>
    <maxSizeRollBackups value="2"/>
    <maximumFileSize value="10KB"/>
    <staticLogFileName value="true" />
    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="%date [%thread] %-5level %logger [%property{NDC}] - %message%newline" />
    </layout>
  </appender>
  <root>
    <appender-ref ref="RollingLogFile" />
  </root>
</log4net> 
```

Enter fullscreen mode Exit fullscreen mode

下面的库

【https://github.com/rbipin/RollingLog_Log4net】中提供了用于测试滚动日志的配置和代码示例代码