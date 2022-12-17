# 采样大量数据

> 原文：<https://dev.to/ladvien/sampling-large-data-730>

R 函数允许对数据帧进行采样。这在编写将用于大型数据帧的脚本时很有帮助，但是，编写脚本是一个迭代过程。抽样允许总体上减少测试迭代的时间，而不会失去实际结果的有效性。

```
 options(java.parameters  =  "-Xmx14336m")  ## memory set to 14 GB  library("sqldf")  library("XLConnect")  library("tcltk")  df  <-  readWorksheetFromFile("Data_X.xlsx",  sheet  =  1,  startRow  =  1)  sampleVector  <-  sample(1:nrow(df),  30000)  df2  <-  df[sampleVector,]  write.csv(df2,  file="Sample of Data_X (30000).csv",  na="") 
```

Enter fullscreen mode Exit fullscreen mode