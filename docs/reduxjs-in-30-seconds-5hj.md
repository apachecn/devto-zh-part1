# 30 秒内恢复

> 原文：<https://dev.to/ross/reduxjs-in-30-seconds-5hj>

Redux 是一个 JavaScript 库，旨在简化我们管理有状态数据的方式。Redux 将我们所有的数据保存在一个名为`Store`的 JS 对象中。一个单独的函数`reducer`负责对商店进行修改。我们通过“分派”一个`action`——一个描述我们的数据应该如何变化的 JS 对象来触发缩减器。reducer 函数接收动作作为参数，并相应地进行更改。代码的其他部分(通常是 React 组件)可以订阅存储中的数据。当数据发生变化时，Redux 会将变化通知给订阅者。