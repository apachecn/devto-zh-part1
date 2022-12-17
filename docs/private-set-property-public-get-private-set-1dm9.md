# 私有集合属性-公共获取，私有集合

> 原文：<https://dev.to/adamkdean/private-set-property-public-get-private-set-1dm9>

只是遇到一些我以前不需要的东西，一个从外部只读，但从内部可写的属性..我向你介绍私人财产！

```
public SocketException LastErrorException { get; private set;  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以从类内部设置它，但不能从外部设置。简单！