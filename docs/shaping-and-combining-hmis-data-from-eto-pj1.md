# 从欧洲贸易组织收集和整合 HMIS 数据

> 原文：<https://dev.to/ladvien/shaping-and-combining-hmis-data-from-eto-pj1>

为了继续探索 R 和 SQL 在 HMIS 数据方面的用处，我决定开始发布 HMIS 问题以及 R 和 SQL 解决方案。

问题:我们的 HMIS 实现有三个避难所向一个分区输入数据。这就像把三种不同颜色的沙子混合到一个桶里——更容易放进去，然后再挑选出来。这也是一个问题，因为 HUD 要求紧急解决方案赠款(ESG)接受者提供年度标准化报告，称为 CAPER，关于由 [HMIS 数据字典](https://www.hudexchange.info/resources/documents/HMIS-Data-Dictionary.pdf)定义的数据。这些数据元素被称为通用数据元素。对于这种混合桶数据，错误责任成为一个大问题。CAPER 只允许多达 25%的数据丢失，这使得数据质量变得非常重要。至于数据修复，应该由造成错误的机构来完成。这使得交流数据问题势在必行。

不幸的是，当来自几个机构的数据混合在一起时，创建一个错误报告是有问题的——至少对于我们的连续护理使用的 HMIS 软件来说是这样。我们的 HMIS 办公室制作的数据质量报告将所有错误汇总在一起。这导致了机构间的社交游手好闲，反过来，很少有数据修复。

解决方案:该解决方案似乎将数据重新分类，重新分配给各个机构的数据。这将允许数据质量报告分配修复责任。目前，我们的 COC 使用社会解决方案 ETO 软件来管理我们的 HMIS。移动数据的过程包括以下步骤:

1.  确定所有需要迁移的数据。对我们来说，这是人口统计，平视显示器评估，和卧床数据。
2.  导出这些数据集。
3.  将数据集分类到各个机构。
4.  使用预定义的模板导入数据。

本文主要关注第三步。数据已经导出，但是如何排序？

下面是一个脚本编写的平视显示器评估和平面文件

1.  过滤到相应的程序
2.  过滤 HUD 评估以*仅*保护入口
3.  修复 COC 代码(例如，“TX 601”->“TX-601”)
4.  将数据重新分配给适当的站点(机构数据)和程序。
5.  将数据分割成不超过 500 行的集合，使导入过程更容易
6.  将数据写入文件。

这是相当粗糙的，但它的工作。

```
library("sqldf")  library("readxl")  # Script Settings --------------------------------------------------------  # Name of output file, not including CSV extension.  outputFileName  <-  paste("HUD Assessments to Upload - v01 -")  # Paths to input files.  inputFileNameforHUDAssessments  <-  paste("TCES HUD Assesment Batch Report of Active Participants on 12-31-2016 - v09.xlsx")  inputFileNameforKeysToMatch  <-  paste("PEIDs of TCES Active Participants.xlsx")  # Names of target Site and Program  siteName  <-  "The Salvation Army Mabee Center"  programName  <-  "TSA Emergency Shelter Check In"  # ----------------------------------------  # Function to split files.  splitDataAndWriteFiles  <-  function(df,  chunkSize,  filename)  {  success  <-  FALSE  count  <-  0  while  (!success)  {  # If you want 20 samples, put any range of 20 values within the range of number of rows  s  <-  paste(((count*chunkSize)+1),  "  ",  ((count+1)*chunkSize))  print(s)  chunk  <-  subset(df[((count*chunkSize)+1):((count+1)*chunkSize),])  #chunk <- sample(df[5:20,])  ## this would contain first 20 rows  fileName  <-  paste(outputFileName,  "_",  as.character(count),  ".csv")  # Write out all the Active HUD Assessments.  write.csv(chunk,  file  =  fileName,  na  =  "",  row.names  =  FALSE)  count  <-  count  +  1  success  <-  (count  *  chunkSize)  >  nrow(df)  }  return(success)  }  # Load all HUD Data  hudAssRaw  <-  read_excel(inputFileNameforHUDAssessments,  na  =  "")  hudAssRaw  <-  subset(hudAssRaw,  slect  =  -NA)  # Re-title columns for easier handling.  colnames(hudAssRaw)[1]  <-  "peid"  colnames(hudAssRaw)[11]  <-  "Relation_to_HH"  colnames(hudAssRaw)[12]  <-  "COC_Code"  # Replaces COC code for head's of household  hudAssRaw$COC_Code[with(hudAssRaw,  Relation_to_HH  ==  "Self (head of household)")]  <-  "TX-601"  hudAssRaw  <-  subset(hudAssRaw,  slect  =  -NA)  # Subset Project Entry data.  hudAssRaw  <-  sqldf("SELECT * FROM hudAssRaw WHERE hudAssRaw.'At what point is this data being collected?_2270' = 'Project Entry'")  # Replaces COC code for head's of household  hudAssRaw$'Program Name'  <-  programName  hudAssRaw$'Site Name'  <-  siteName  colnames(hudAssRaw)[13]  <-  "A-3 What is the client's relationship t_2272"  colnames(hudAssRaw)[14]  <-  "A-5 HUD-assigned CoC code for the clien_2273"  # Set the dates back to YYYY-MM-DD  #hudAssRaw$`Response Date` <- as.Date(hudAssRaw$`Response Date`, "%Y%m%d")  #hudAssRaw$`DOB` <- as.Date(hudAssRaw$`DOB`, "%Y%m%d")  #hudAssRaw$`A-57 Approximate date homelessness star_6115` <- as.Date(hudAssRaw$`A-57 Approximate date homelessness star_6115`, "%Y%m%d")  #hudAssRaw$`A-58 Approximate date homelessness star_6116` <- as.Date(hudAssRaw$`A-58 Approximate date homelessness star_6116`, "%Y%m%d")  hudAssRaw  <-  subset(hudAssRaw,  slect  =  -NA)  # Get target site Participant IDs  targetSiteParticipantIDs  <-  read_excel("TSA ESCI Target PSID.xlsx")  assessmentsWithTargetPID  <-  sqldf("SELECT * FROM targetSiteParticipantIDs
                                  INNER JOIN hudAssRaw   
                                  ON hudAssRaw.'Case Number'=targetSiteParticipantIDs.'Case Number'")  # Free up space.  rm(hudAssRaw)  rm(targetSiteParticipantIDs)  assessmentsWithTargetPID  <-  subset(assessmentsWithTargetPID,  slect  =  -NA)  colnames(assessmentsWithTargetPID)[1]  <-  "pid"  colnames(assessmentsWithTargetPID)[12]  <-  "rid"  # INNER JOIN on self to get -only- the first HUD Assessment  # Thanks SO! http://stackoverflow.com/questions/7745609/sql-select-only-rows-with-max-value-on-a-column  assessmentsWithTargetPID  <-  sqldf("SELECT *
              FROM assessmentsWithTargetPID a
               INNER JOIN (
                  SELECT pid, MIN(rid) rid
                  FROM assessmentsWithTargetPID
                  GROUP BY pid
                ) b ON a.pid = b.pid AND a.rid = b.rid
              ")  # Remove PEID  assessmentsWithTargetPID  <-  subset(assessmentsWithTargetPID,  select  =  -c(peid,peid.1))  write.csv(assessmentsWithTargetPID,  file  =  "First HUD Entry Assessments for ESCI.csv",  na  =  "",  row.names  =  FALSE)  # Split the data into chunks and write to files.  splitDataAndWriteFiles(activeEntryAssessments,  500) 
```

Enter fullscreen mode Exit fullscreen mode