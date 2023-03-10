# 带有布局列表的更智能的面包屑

> 原文：<https://dev.to/eecms/smarter-breadcrumbs-with-layout-lists-5gjk>

[布局列表变量](https://docs.expressionengine.com/latest/templates/layouts.html#dynamic-variables-lists)使得以多种方式重用内容变得容易。

定义内容模板上的面包屑名称、URL 和位置:

```
{layout:set:append name='breadcrumb_urls'}{path='second'}{/layout:set:append}
{layout:set:append name='breadcrumb_titles'}Second crumb{/layout:set:append}
{layout:set:append name='breadcrumb_jsonld_positions'}2{/layout:set:append}

{layout:set:append name='breadcrumb_urls'}{path='active'}{/layout:set:append}
{layout:set:append name='breadcrumb_titles'}Active crumb{/layout:set:append}
{layout:set:append name='breadcrumb_jsonld_positions'}3{/layout:set:append} 
```

Enter fullscreen mode Exit fullscreen mode

在你的版面上以任何你需要的格式输出它。

Bootstrap 的[现代商业](https://startbootstrap.com/template-overviews/modern-business/)模板的 HTML 面包屑:

```
{layout:breadcrumb_urls}
    {if count == 1}
        <ol class="breadcrumb">
            <li class="breadcrumb-item">
                <a href="index">Home</a>
            </li>
            <li class="breadcrumb-item">
                <a href="{value}">{layout:breadcrumb_titles index='{index}'}</a>
            </li>
    {if:elseif count < total_results}
            <li class="breadcrumb-item">
              <a href="{value}">{layout:breadcrumb_titles index='{index}'}</a>
            </li>
    {if:else}
            <li class="breadcrumb-item active">
                {layout:breadcrumb_titles index='{index}'}
            </li>
        </ol>
    {/if}
{/layout:breadcrumb_urls} 
```

Enter fullscreen mode Exit fullscreen mode

一个[结构化标记](http://schema.org/) [面包屑列表](http://schema.org/BreadcrumbList) :

```
<script type="application/ld+json">
    {
        "@context": "http://schema.org",
        "@type": "BreadcrumbList",
        "itemListElement": [
        {
            "@type": "ListItem",
            "position": 1,
            "item": {
                "@id": "https://example.com/index",
                "name": "Home"
            }
        }
        {layout:breadcrumb_urls}
        ,{
            "@type": "ListItem",
            "position": {layout:breadcrumb_jsonld_positions index='{index}'},
            "item": {
                "@id": "{value}",
                "name": "{layout:breadcrumb_titles index='{index}'}"
            }
        }
        {/layout:breadcrumb_urls}
    ]}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

干燥、灵活、易于维护和更新。您的最终结果将是:

```
<ol class="breadcrumb">
    <li class="breadcrumb-item">
        <a href="index">Home</a>
    </li>
    <li class="breadcrumb-item">
        <a href="second">Second crumb</a>
    </li>
    <li class="breadcrumb-item active">
        Active crumb
    </li>
</ol>

<script type="application/ld+json">
    {
        "@context": "http://schema.org",
        "@type": "BreadcrumbList",
        "itemListElement": [
        {
            "@type": "ListItem",
            "position": 1,
            "item": {
                "@id": "https://example.com/index",
                "name": "Home"
            }
        },
        {
            "@type": "ListItem",
            "position": 2,
            "item": {
                "@id": "https://example.com/second",
                "name": "Second crumb"
            }
        },
        {
            "@type": "ListItem",
            "position": 3,
            "item": {
                "@id": "https://example.com/active",
                "name": "Active crumb"
            }
        }]
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode