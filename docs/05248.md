# 使用 Docker 实现简单而强大的反向代理和负载平衡

> 原文：<https://dev.to/stephenafamo/easy-and-powerful-reverse-proxy-and-load-balancing-with-docker>

我建立了很多网站，我在 Docker 容器中运行它们。所发生的是，我得到了这样的 URLs】。

这是个问题，因为，

1.  它们很难记住
2.  不允许子域。
3.  对密码管理员不利

我最初的解决方案是用 Nginx 添加一个反向代理，这样我就有了指向我的容器的虚拟主机。然而，这意味着我一遍又一遍地编写相同的 Nginx 配置，只是使用了不同的端口。

因此，正如任何正常的开发人员都会做的那样，我花了几个小时来一劳永逸地自动化这个过程。

[![let's do this](img/a47d75c3d70f497317da2d50a1becca6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--La_V8E55--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://m.popkey.co/bb9661/7MDOG.gif)

## 解

我最终创建了一个 docker 映像来生成我的配置文件，并成为一个合适的反向代理。
你可以在这里[找到它](https://github.com/stephenafamo/docker-nginx-auto-proxy)

### 特性

1.  只需几行配置就可以设置反向代理
2.  可以自动从[获取 SSL 证书让我们加密](https://letsencrypt.org/)
3.  可用于在几台服务器之间建立负载平衡
4.  允许对负载平衡配置进行微调。

随着我发现更多的用例，我会添加更多的特性。如果有什么事，请告诉我

关于如何使用的适当细节在[库](https://github.com/stephenafamo/docker-nginx-auto-proxy)的`README.md`中。
喜欢就上 GitHub 星。