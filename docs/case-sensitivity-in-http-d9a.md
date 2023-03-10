# HTTP 中区分大小写

> 原文：<https://dev.to/onmyway133/case-sensitivity-in-http-d9a>

原帖[https://github.com/onmyway133/blog/issues/115](https://github.com/onmyway133/blog/issues/115)

## URL 区分大小写吗？

根据 [HTML 和 URL](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)

> URL 通常区分大小写(机器名除外)。可能有 URL，或者 URL 的一部分，其中大小写无关紧要，但是识别它们可能并不容易。用户应该始终考虑到 URL 是区分大小写的。

## 表头区分大小写吗？

根据 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.2)

> 每个头字段由不区分大小写的字段名、冒号(":")、可选前导空格、字段值和可选尾随空格组成。

## 方法区分大小写吗？

使用`URLSession`，通常我们会用`request.httpMethod = "GET"`得到`200`，用`request.httpMethod = "get"`得到`400`。一般来说，你应该使用一些流行的包装材料 [Alamofire](https://github.com/Alamofire/Alamofire) 或 [Malibu](https://github.com/hyperoslo/Malibu) ，因为它们足够成熟。把你宝贵的时间花在解决更重要的问题上。

根据 [RFC 7231](https://tools.ietf.org/html/rfc7231#page-24)

> 方法标记区分大小写，因为它可能被用作基于对象的系统的网关，方法名称区分大小写。