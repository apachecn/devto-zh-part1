# HTTP 基本身份验证用户 id 和密码中应该允许哪些字符

> 原文：<https://dev.to/ethanarrowood/what-characters-should-be-allowed-in-http-basic-authentication-userid-and-password-48f0>

基于[这个](https://tools.ietf.org/html/rfc2617#page-6) RFC2617 规范，HTTP 基本认证`userid`可以包含除符号`:`之外的任何`TEXT`。`password`可以包含任何`TEXT`。这些是基本身份验证用户名和密码的唯一规则吗？