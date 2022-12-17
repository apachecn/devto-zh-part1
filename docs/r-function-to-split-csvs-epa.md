# 用于分割 CSV 的 r 函数

> 原文：<https://dev.to/ladvien/r-function-to-split-csvs-epa>

这是一个 R 函数，用来将数据集分割成特定大小的集合，然后将它们写成 CSV 格式。通常，我们的办公室需要一种快速的方法来拆分文件以便上传，因为我们的 HMIS 软件不能很好地处理大量的上传。

例如:

```
splitDataAndWriteFiles(df,  500,  "My_Data") 
```

Enter fullscreen mode Exit fullscreen mode

将产生 X 个名为“My_data_X.csv”的文件

```
options(java.parameters  =  "-Xmx14336m")  ## memory set to 14 GB  library("XLConnect")  # Function to split files.  splitDataAndWriteFiles  <-  function(df,  chunkSize,  nameOfFiles)  {  success  <-  FALSE  count  <-  0  while  (!success)  {  # If you want 20 samples, put any range of 20 values within the range of number of rows  s  <-  paste(((count*chunkSize)+1),  "_",  ((count+1)*chunkSize))  print(s)  chunk  <-  subset(df[((count*chunkSize)+1):((count+1)*chunkSize),])  #chunk <- sample(df[5:20,])  ## this would contain first 20 rows  fileName  <-  paste(nameOfFiles,  "_",  as.character(count),  ".csv")  # Write out all the Active HUD Assessments.  write.csv(chunk,  file  =  fileName,  na  =  "",  row.names  =  FALSE,  fileEncoding  =  "utf8")  count  <-  count  +  1  success  <-  (count  *  chunkSize)  >  nrow(df)  }  return(success)  }  fileToSplit  <-  read.csv("UPLOAD -- Sal Men-- TCES Move -- TSA Bed Data Template.csv")  splitDataAndWriteFiles(fileToSplit,  5000,  "Sal_Men_NBN") 
```

Enter fullscreen mode Exit fullscreen mode