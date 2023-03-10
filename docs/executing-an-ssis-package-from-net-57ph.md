# 从执行 SSIS 包。网

> 原文：<https://dev.to/collinbarrett/executing-an-ssis-package-from-net-57ph>

[![SQL Server Integration Services](img/753ad048e5acec322ca3cd28526db5d1.png)T2】](https://collinmbarrett.com/execute-ssis-csharp-dotnet/)

我一直在开发的零售定价应用程序要求允许用户触发在 SQL Server Integration Services 中实现的流程。我们在 Kendo UI 前端添加了一个按钮，但是连接这个按钮来触发安装在 SQL Server 实例上的 SSIS 包对我来说是全新的。我们是这样做的。

## 直接执行 SSIS 包

我和一位同事花了一天左右的时间，试图直接执行 SSIS 方案。之所以花了这么长时间，是因为必须与 DBA 进行沟通，以获得正确配置的服务帐户、访问权限和包位置/名称。然而，最终我们找到了一个可行的解决方案，但有一点需要注意。

### 依赖关系

我们首先需要确保我们包含了几个依赖项。

```
using System.Data.SqlClient;using Microsoft.SqlServer.Management.IntegrationServices; 
```

### 执行

下面的代码片段显示了我们想到的最终工作解决方案，它用单个参数触发一个包。参数配置中 ObjectType 的值指的是以下哪种类型的参数:system (50)，package (30)，project (20)。我们使用的连接字符串是专门为此目的创建的新 SQL Server 服务帐户。

```
private void ExecuteSsisPackage(){using (var ssisSqlConnection = new SqlConnection(ConfigurationManager.ConnectionStrings["SsisConn"].ConnectionString)){var ssisServer = new IntegrationServices(ssisSqlConnection);var ssisPackage = ssisServer.Catalogs["SSISDB"].Folders["MyProject"].Projects["MyProject"].Packages["MyPackage.dtsx"];var executionParameter = new Collection&lt;PackageInfo.ExecutionValueParameterSet&gt;{new PackageInfo.ExecutionValueParameterSet{ObjectType = 20,ParameterName = "MyPackageParameter1Name",ParameterValue = "MyPackageParameter1Value"}};ssisPackage.Execute(false, null, executionParameter);}} 
```

### 告诫

在反复测试各种错误以获得上面的解决方案之后，我们面临最后一个错误:

<figure>[![SSIS Execution Error Using SQL Server Authentication](img/1cec7421c052aad639f7f190d54d73f6.png)](https://collinmbarrett.com/media/SsisSqlServerAuthenticationError_collinmbarrett.jpg)

<figcaption id="caption-attachment-4400">【SSIS】使用 SQL Server 认证执行错误</figcaption>

</figure>

哦，我们创建的新 SQL Server 服务帐户不能直接触发包。我希望我们能早点发现这一点。在我们的环境中，使用 Windows 帐户不是一个选项，所以我们被迫放弃这段代码，转而采用 b 计划。

## 通过 SQL 作业执行

与 SSIS 包不同，SQL 作业实际上可以由 SQL Server 用户帐户触发。因此，我们配置了一个 SQL 作业来为我们执行。SQL Server 附带了一个专门为此目的而设计的系统存储过程。因此，下面的代码片段相当简单，一旦我们确保作业配置了正确的参数值，并且 SQL Server 服务帐户具有执行该系统存储过程的权限，它就像冠军一样工作。

```
private void ExecuteSsisPackage(){using (var ssisSqlConnection = new SqlConnection(ConfigurationManager.ConnectionStrings["SsisConn"].ConnectionString)){using (var execSsisStoredProcedure = new SqlCommand("dbo.sp\_start\_job", ssisSqlConnection)){execSsisStoredProcedure.CommandType = CommandType.StoredProcedure;execSsisStoredProcedure.Parameters.AddWithValue("@job\_name", "MyPackageSqlJob");ssisSqlConnection.Open();execSsisStoredProcedure.ExecuteNonQuery();}}} 
```

注意，为了让这个方法工作，我们需要正确设置系统存储过程所在的目录(MSDB)。我们在 web.config 中的连接字符串中这样做了，如下所示，但是在上面的代码片段中也可能有一种设置它的方法。

```
&lt;connectionStrings&gt;&lt;add name="SsisConn" connectionString="Data Source=myserver;Initial Catalog=MSDB;MultipleActiveResultSets=True;Persist Security Info=True;User ID=MySsisUser;Password=MySsisPassword" providerName="System.Data.SqlClient" /&gt;&lt;/connectionStrings&gt; 
```

*via [微软 TechNet](https://social.technet.microsoft.com/wiki/contents/articles/21978.execute-ssis-2012-package-with-parameters-via-net.aspx)T3】*