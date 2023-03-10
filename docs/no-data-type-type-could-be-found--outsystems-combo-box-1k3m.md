# 找不到数据类型“TYPE”——out systems 组合框

> 原文：<https://dev.to/rkosta/no-data-type-type-could-be-found--outsystems-combo-box-1k3m>

我试图设置一个绑定到静态实体的组合框，当我发布网站时，我得到了这个错误:

```
Internal Error 
No data type 'Status' could be found. 
Exception Details:
[1] Internal Error: No data type 'Status' could be found.
at OutSystems.HubEdition.Compiler.WebWidgets.ComboBox.dumpEntityDeclaration(TextWriter res)
 at OutSystems.HubEdition.Compiler.WebWidgets.ComboBox.DumpDataBindingCode(TextWriter writer)
 at OutSystems.HubEdition.Compiler.AbstractServerWebWidget.DumpOnDataBinding(TextWriter writer, NodeViewState viewState)
 at OutSystems.HubEdition.Compiler.AbstractServerWebWidget.DumpHandler(TextWriter writer, NodeViewState viewState)
 at OutSystems.HubEdition.Compiler.WebWidgets.ComboBox.DumpHandler(TextWriter res, NodeViewState viewState)
 at OutSystems.HubEdition.Compiler.Nodes.WebScreen.Dump(TextWriter writer)
 at OutSystems.HubEdition.Compiler.Flows.WebFlow.Dump()
 at OutSystems.HubEdition.Compiler.WebFlows.WebHandler.Dump(IEnumerable`1 webFlows)
 at OutSystems.HubEdition.Compiler.ESpace.Dump()
 at OutSystems.HubEdition.Compiler.Compiler.InnerCompile(CompilationContext context)
 at OutSystems.HubEdition.Compiler.Compiler.Compile(CompilationContext context)
 at OutSystems.HubEdition.Compiler.Utils.CompilerUtils.WithOverridenSettings(IDictionary`2 settingsOverride, Action body)
 at OutSystems.HubEdition.Compiler.Compiler.<>c__DisplayClass3.<Compile>b__1()
 at System.Threading.ExecutionContext.RunInternal(ExecutionContext executionContext, ContextCallback callback, Object state, Boolean preserveSyncCtx)
 at System.Threading.ExecutionContext.Run(ExecutionContext executionContext, ContextCallback callback, Object state, Boolean preserveSyncCtx)
 at System.Threading.ExecutionContext.Run(ExecutionContext executionContext, ContextCallback callback, Object state)
 at System.Threading.ThreadHelper.ThreadStart() 
```

Enter fullscreen mode Exit fullscreen mode

但是 TrueChange 没有向我显示任何错误或警告。只有当我试图发布应用程序时，才会出现这个错误。

问题是我定义的用来保存用户输入值的变量是静态实体类型，而不是静态实体标识符类型。

[![](img/0ef310eaa52d0f2f6a5832c63d496560.png)T2】](https://i1.wp.com/blogit-create.com/wp-content/uploads/2017/05/OutSystemsComboBox01.png)

变量“状态”是这样定义的:

[![](img/326e5aff6830dc4d64a2e7b12e0881ac.png)T2】](https://i1.wp.com/blogit-create.com/wp-content/uploads/2017/05/OutSystemsComboBox02.png)

并且必须这样定义:

[![](img/4672ed691a33eae75d456cc6cb4f4ec2.png)T2】](https://i1.wp.com/blogit-create.com/wp-content/uploads/2017/05/OutSystemsComboBox03.png)

它必须是状态标识符，而不是状态(静态实体)。

帖子[找不到数据类型“TYPE”——out systems 组合框](https://blogit.create.pt/ricardocosta/2017/05/02/no-data-type-type-could-be-found-outsystems-combo-box/)首先出现在[博客 IT](https://blogit.create.pt) 上。