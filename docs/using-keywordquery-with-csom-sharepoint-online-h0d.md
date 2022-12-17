# 在 CSOM SharePoint Online 中使用关键字 Query

> 原文：<https://dev.to/rkosta/using-keywordquery-with-csom-sharepoint-online-h0d>

```
string username = "***********";
string pwd = "*********";
string siteURL = "**********";

ClientContext context = new ClientContext(siteURL);
Web web = context.Web;
SecureString pass = new SecureString();
foreach (char c in pwd.ToCharArray()) pass.AppendChar(c);
context.Credentials = new SharePointOnlineCredentials(username, passWord);
try
{
    KeywordQuery query = new KeywordQuery(context);
    query.QueryText = "ContentType:Factura AND PrecoOWSCURR>10000";

    query.RowLimit = 10;
    query.RowsPerPage = 10;
    SearchExecutor search = new SearchExecutor(context);
    ClientResult<ResultTableCollection> results = search.ExecuteQuery(query);
    context.ExecuteQuery();

    foreach (var resultRow in results.Value[0].ResultRows)
    {
        Console.WriteLine("{0}", resultRow["Title"]);
    }
}
catch (Exception e)
{
    Console.WriteLine(e.ToString());
    Console.ReadKey();
} 
```

在 CSOM SharePoint Online 上使用关键字查询的帖子[首先出现在](https://blogit.create.pt/ricardocosta/2017/10/25/using-keywordquery-with-csom-sharepoint-online/)[博客 IT](https://blogit.create.pt) 上。