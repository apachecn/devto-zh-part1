# 视图和模板中的流畅界面

> 原文：<https://dev.to/robinvanderknaap/fluent-interfaces-in-views-en-templates-2dmo>

最近，我们对于使用 fluent 介面让我们的程式码更具可读性，已经成为网路海盗的粉丝。在本文中，我将向您展示我们如何建立流畅的界面，使视图和模板更易于阅读、更整洁和使用。

### Wat 是 een fluent 接口？

简而言之，fluent 介面可让您有效且清楚地存取物件的公用 API。这通常会大大缩短呼叫程式码的长度、提高可读性，并使其更易于使用。

下面的示例说明如何在 C#中调用具有 fluent 接口的类: