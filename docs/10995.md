# Phoenix Framework 中每个视图或控制器动作的自定义标题

> 原文：<https://dev.to/tuacker/custom-titles-per-view-or-controller-actions-in-phoenix-framework-3pnm>

Phoenix 提供了`render_existing/3`函数，该函数尝试为所提供的模块呈现模板，或者如果模板不存在，则返回`nil`。我们可以用它来创建自定义标题，甚至是基于每个视图的 JavaScript includes。

在您现有的 phoenix 项目中，打开通常位于`views/layout_view.ex`中的`MyApp.LayoutView`，并添加以下内容。

```
# views/layout_view.ex

@doc """
Returns the string of the title for the provided module and template.
It does so by calling render_existing/3 on the `view_module`. The `view module`
is concatenated with "title." to get the template.

So if the `view_template` is `show.html` then this will call
`title.show.html` on the provided `view_module`.

If `render_existing/3` doesn't produce any results `default` is used instead.
"""
def title(view_module, view_template, assigns, default \\ "Your Default Title") do
  render_existing(view_module, "title." <> view_template, assigns) || default
end 
```

现在打开任何控制器动作的视图模块，例如`session_view.ex`。假设您希望登录操作的标题为“登录 MyApp”。

```
# views/session_view.ex
def render("title.new.html", _), do: "Log in to MyApp" 
```

这里的`new`映射到当前视图模板`new`，它也可以是`show`或任何其他值。

最后，打开你的主布局，通常是`templates/layout/app.html.eex`，并在`<head>`
中添加以下内容

```
# templates/layout/app.html.eex
  <%= title @view_module, @view_template, assigns %> 
```

上面的代码行将调用本文开头定义的`layout_view.ex`中的`title/3`函数，这将在当前视图模块(在我们的例子中是`session_view.ex`)上使用`render_existing/3`作为模板`title.new.html`。

对于任何没有定义`title.<action>.html`的页面，调用将会悄悄地失败并返回默认值。

您甚至可以使用它在选择的页面上包含定制的 JavaScript。只需在你的布局视图中创建另一个函数，重复添加一个`render`函数到你的视图的过程，并更新布局以调用该函数。

我经常在自定义标题、面包屑和元标签中使用这个。这是我的`layout_view.ex`的样子。

```
def title(view_module, view_template, assigns, default \\ "Ads From Source") do
  render_existing(view_module, "title." <> view_template, assigns) || default
end

def breadcrumbs(view_module, view_template, assigns) do
  render_existing(view_module, "crumbs." <> view_template, assigns)
end

def meta_descriptors(view_module, view_template, assigns) do
  render_existing(view_module, "meta." <> view_template, assigns)
end 
```