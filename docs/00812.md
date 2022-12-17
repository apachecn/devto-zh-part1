# 如何调试和解决 Kubernetes 的“权限被拒绝(403)”问题

> 原文：<https://dev.to/corehello/how-to-debug-and-solve-permission-denied403-issue-for-kubernetes-14ef>

当使用 kubernetes 时，授权问题是一件令人厌烦的事情，因为我们通常不关心授权部分，我们只想要特性工作，比如 HPA。

因此，对于这一点，我强烈建议您启用 audit chain，让您知道哪个用户在集群中的 403 上，因为 kubernetes 经常使用某个代理用户而不是您认为应该使用的用户来执行最终操作。

要启用审计链，请参考[审计配置](https://kubernetes.io/docs/tasks/debug-application-cluster/audit)。

启用审计后，在调试权限被拒绝问题时，可以通过`tail -f`检查审计日志。您将在审计日志中看到类似这样的内容:

```
2017-03-21T03:57:09.106841886-04:00 AUDIT: id="c939d2a7-1c37-4ef1-b2f7-4ba9b1e43b53" ip="127.0.0.1" method="GET" user="admin" groups="\"system:masters\",\"system:authenticated\"" as="<self>" asgroups="<lookup>" namespace="default" uri="/api/v1/namespaces/default/pods"
2017-03-21T03:57:09.108403639-04:00 AUDIT: id="c939d2a7-1c37-4ef1-b2f7-4ba9b1e43b53" response="200" 
```

Enter fullscreen mode Exit fullscreen mode

可以找到这 403 个，尝试在适当的范围内授予(`kubectl create clusterrolebinding bindname --cluster-role=role_name --user=user_name [--group=group_name]`)适当角色的用户或组或集群角色(`kubectl get cluster role`)。

无论如何，你必须熟悉库伯内特，并对库伯内特 RBAC 有所了解，否则你仍然不知道我在说什么。

谢了。