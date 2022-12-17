# 看，端点对象

> 原文：<https://dev.to/burdettelamar/behold-the-endpoint-object-820>

在测试 web 应用程序时，*页面对象*设计模式已经变得非常有名。它的工作是典型的数据隐藏:每个这样的对象封装一个 HTML 页面。

当我开始开发我的第一个测试 REST API 的框架时，我问自己如何应用这个封装原则。显而易见的答案是:*端点对象*。

正如 page 对象封装 HTML 页面一样，endpoint 对象也封装 REST API 端点。每个端点都有自己的封装类。

确切地说，现在一个端点没有名字，但是它有一个 HTTP 方法和一个 URL。我用它们来构造端点类名。

示例(来自我的[框架](https://github.com/BurdetteLamar/RubyTest/blob/master/examples/github_api/TesterTour.md#tester-tour)，用于测试 GitHub 自己的 REST API):

| 方法和 URL | 端点类名 | 影响 |
| `GET /labels` | `GetLabels` | 获取所有标签。 |
| `POST /labels` | `PostLabels` | 创建一个标签。 |
| `GET /labels/:name` | `GetLabelsName` | 获取命名标签。 |
| `PATCH /labels/:name` | `PatchLabelsName` | 更新命名标签。 |
| `DELETE /labels/:name` | `DeleteLabelsName` | 删除命名标签。 |

测试框架应该为测试人员简化事情，对吗？为此，这些对象中的方法接受并返回实际的 Ruby `Label`对象，而不是原始的 JSON。这些方法透明地处理 JSON 和这些对象之间的转换。

每个端点都有四个这样的方法。以`PatchLabels`为例:

*   `PatchLabels.call(client, label)`创建一个标签，并将创建的标签作为一个`Label`对象返回。
*   `PatchLabels.call_and_return_payload(client, label)`做了同样的事情，但是返回了`Label`对象和原始 JSON 负载(以防调用者想要检查它)。
*   `PatchLabels.verdict_call_and_verify_success(client, log, label)`创建一个标签，并将创建的标签作为一个`Label`对象返回，同时记录相关的判断。
*   `PatchLabels.verdict_aberrant(client, log)`访问具有各种导致错误的异常的端点，记录相关的判断，并且不返回任何内容。

瞧，端点对象！