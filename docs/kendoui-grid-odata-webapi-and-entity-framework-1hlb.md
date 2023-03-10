# KendoUI 网格、OData、WebAPI 和实体框架

> 原文：<https://dev.to/vigilnt/kendoui-grid-odata-webapi-and-entity-framework-1hlb>

在我最近参与的一个项目中，需要在 Umbraco 的后台直接呈现数据库表数据，所有的 CRUD 操作都必须得到支持。这些表可能有 1000 到 100 万行，网站和数据库都将托管在 Azure 中，分别位于 WebApp 和 SQL 数据库中。用户应该能够创建复杂的过滤器，并按照自己的意愿对数据进行排序，以及单独和批量更新数据。

从一开始，我们就需要克服一些挑战，以创建一个可用的系统，它是用户友好的、快速的、可靠的，并且当多个用户更新数据时不会出现冲突。

解决方案是一个新的 Umbraco 包，我们称之为*搜索管理器*(因为数据库表最终提供了 Azure 搜索索引)，它使用了 [Telerik 的剑道 UI 网格](http://demos.telerik.com/kendo-ui/grid/index)、WebAPI 和实体框架。下面介绍了达到最终结果的过程。

## 第一步

我们创建了一个简单的 Umbraco 包，它实现了一个 Kendo UI 网格，该网格从一个 WebAPI 方法中获取数据，该方法使用 Petapoco 从一个表中返回所有行。

这允许我们使用 Kendo UI 网格在内存中保存完整的表数据。它运行得很好，但是有一些主要问题:

*   一个大约有 40k 行的表实际上是一个 40 MBs 的 json 下载，以便填充网格
*   用户可能正在使用过时的数据，因为这些数据是加载到内存中的，并且可能已经对源进行了更改

## 第二步

我们开始使用剑道网格的服务器分页特性。这大大降低了下载量，但我们仍然有问题:

*   即使我们只处理客户端结果的一个子集，我们仍然可以使用 petapoco 在服务器端获得完整的表数据。

我们需要一种简单的方法来将客户端查询(如过滤器、分页、订单等)转换为 SQL 查询。

## 第三步

我们将网格生成的查询类型更改为 OData。使用 [OData WebAPI nuget 包](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OData)我们能够将 OData 查询映射到 C#对象，然后直接将查询应用到任何 IQueryable 数据。

Petapoco 不提供任何 IQueryable 数据支持，所以我们把我们的 ORM 改成了 Entity Framework，它提供了，而且 *voilá* ！

## 最后

一切就绪后，我们就能够在网格中过滤出结果的子集，从动态生成的 SQL 查询中准确地获得需要显示的内容。

*   它很快，因为我们只得到我们所需要的:

由 Kendo UI 网格生成的 OData 查询如下所示:

```
http://localhost:14231/umbraco/backoffice/SearchManager/PricesApi/Read?%24inlinecount=allpages&%24top=20&%24skip=40&%24orderby=ProviderName+desc&%24filter=(NatureProcCode+eq+%27exames%27+and+AvgAmtClaimed+gt+10) 
```

它被转换为以下 sql 查询:

```
exec sp_executesql N'SELECT 
    [Project1].[Approved] AS [Approved], 
    [Project1].[DiffWeightedRate] AS [DiffWeightedRate], 
    [Project1].[SubmitPrvId] AS [SubmitPrvId], 
    [Project1].[PracticeSeq] AS [PracticeSeq], 
    [Project1].[NatureProcCode] AS [NatureProcCode], 
    [Project1].[GroupProcCode] AS [GroupProcCode], 
    [Project1].[Network] AS [Network], 
    [Project1].[AmtClaimedWeighted] AS [AmtClaimedWeighted], 
    [Project1].[ManualPrice] AS [ManualPrice], 
    [Project1].[IsImported] AS [IsImported], 
    [Project1].[DiffCalculatedRate] AS [DiffCalculatedRate], 
    [Project1].[AmtContract] AS [AmtContract], 
    [Project1].[AvgAmtClaimed] AS [AvgAmtClaimed], 
    [Project1].[ProviderName] AS [ProviderName], 
    [Project1].[ClinicName] AS [ClinicName]
    FROM ( SELECT 
        [Extent1].[Approved] AS [Approved], 
        [Extent1].[DiffWeightedRate] AS [DiffWeightedRate], 
        [Extent1].[SubmitPrvId] AS [SubmitPrvId], 
        [Extent1].[PracticeSeq] AS [PracticeSeq], 
        [Extent1].[NatureProcCode] AS [NatureProcCode], 
        [Extent1].[GroupProcCode] AS [GroupProcCode], 
        [Extent1].[Network] AS [Network], 
        [Extent1].[AmtClaimedWeighted] AS [AmtClaimedWeighted], 
        [Extent1].[ManualPrice] AS [ManualPrice], 
        [Extent1].[IsImported] AS [IsImported], 
        [Extent1].[DiffCalculatedRate] AS [DiffCalculatedRate], 
        [Extent1].[AmtContract] AS [AmtContract], 
        [Extent1].[AvgAmtClaimed] AS [AvgAmtClaimed], 
        [Extent1].[ProviderName] AS [ProviderName], 
        [Extent1].[ClinicName] AS [ClinicName]
        FROM (SELECT 
    [PriceGrid].[Approved] AS [Approved], 
    [PriceGrid].[DiffWeightedRate] AS [DiffWeightedRate], 
    [PriceGrid].[SubmitPrvId] AS [SubmitPrvId], 
    [PriceGrid].[PracticeSeq] AS [PracticeSeq], 
    [PriceGrid].[NatureProcCode] AS [NatureProcCode], 
    [PriceGrid].[GroupProcCode] AS [GroupProcCode], 
    [PriceGrid].[Network] AS [Network], 
    [PriceGrid].[AmtClaimedWeighted] AS [AmtClaimedWeighted], 
    [PriceGrid].[ManualPrice] AS [ManualPrice], 
    [PriceGrid].[IsImported] AS [IsImported], 
    [PriceGrid].[DiffCalculatedRate] AS [DiffCalculatedRate], 
    [PriceGrid].[AmtContract] AS [AmtContract], 
    [PriceGrid].[AvgAmtClaimed] AS [AvgAmtClaimed], 
    [PriceGrid].[ProviderName] AS [ProviderName], 
    [PriceGrid].[ClinicName] AS [ClinicName]
    FROM [dbo].[PriceGrid] AS [PriceGrid]) AS [Extent1]
        WHERE ([Extent1].[NatureProcCode] = @p__linq__0) AND ([Extent1].[AvgAmtClaimed] > @p__linq__1)
    )  AS [Project1]
    ORDER BY [Project1].[ProviderName] DESC, [Project1].[AmtClaimedWeighted] ASC, [Project1].[AmtContract] ASC, [Project1].[Approved] ASC, [Project1].[AvgAmtClaimed] ASC, [Project1].[ClinicName] ASC, [Project1].[DiffCalculatedRate] ASC, [Project1].[DiffWeightedRate] ASC, [Project1].[GroupProcCode] ASC, [Project1].[IsImported] ASC, [Project1].[ManualPrice] ASC, [Project1].[NatureProcCode] ASC, [Project1].[Network] ASC, [Project1].[PracticeSeq] ASC, [Project1].[SubmitPrvId] ASC
    OFFSET @p__linq__2 ROWS FETCH NEXT @p__linq__3 ROWS ONLY  option (Recompile)',N'@p__linq__0 varchar(8000),@p__linq__1 decimal(2,0),@p__linq__2 int,@p__linq__3 int',@p__linq__0='exames',@p__linq__1=10,@p__linq__2=40,@p__linq__3=20 
```

*   它对用户友好且可靠，因为用户可以像使用 excel 文件一样过滤数据
*   它避免了冲突，因为我们每次只获得结果的一个子集，并且每个更改都在单个事务中提交

## 代码示例:

```
grid = $("#grid").kendoGrid({
                dataSource: new kendo.data.DataSource({
                    transport: transport,
                    schema: schema,
                    serverFiltering: true,
                    serverPaging: true,
                    serverSorting: true,
                    type: "odata",
                    pageSize: 20
                }),
                dataBound: onDataBound,
                pageable: true,
                filterable: true,
                sortable: true,
                scrollable: false,
                navigatable: true,
                resizable: true,
                batch: true,
                columnMenu: true,
                editable: true,
                toolbar: [
                    { template: '<a class="k-button k-button-icontext k-grid-save-batch" href="\\#""><span class="k-icon k-i-update"></span>Guardar Alterações</a>' },
                    { name: "cancel", text: "Cancelar" },
                ],
                columns: [...]
            }); 
```

```
[HttpGet]
        public IHttpActionResult Read(ODataQueryOptions<PriceGrid> opts)
        {
            Mapper.CreateMap<PriceGrid, PriceGridBO>();

            using (var context = new EntitiesContext())
            {
                using (var qh = new HintScope(context, QueryHintType.Recompile))
                {
                    List<PriceGrid> results = opts
                        .ApplyTo(context.PriceGrids)
                        .Cast<PriceGrid>()
                        .ToList();

                    List<PriceGridBO> boEntities = results.Select(x => Mapper.Map<PriceGrid, PriceGridBO>(x)).ToList();

                    PriceResponse response = new PriceResponse()
                    {
                        Entities = boEntities,
                        Total = Request.ODataProperties().TotalCount.HasValue ? Request.ODataProperties().TotalCount.Value : 0
                    };

                    return Ok(response);
                }
            }
        } 
```

帖子 [KendoUI Grid、OData、WebAPI 和实体框架](https://blogit.create.pt/andresantos/2017/07/05/kendoui-grid-odata-webapi-and-entity-framework/)最早出现在[博客 IT](https://blogit.create.pt) 上。