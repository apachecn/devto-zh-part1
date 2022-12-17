# 将文件从一个容器复制到另一个容器

> 原文：<https://dev.to/adamkdean/copying-files-from-one-container-to-another-28eg>

这只对 OS X 有效，因为我使用了`pbcopy`和`pbpaste`。如果你认为合适，你可以换掉这些。此外，我在这里使用 superdocker，如果你喜欢，可以将它换成 docker，这没有什么区别。

我需要做的是将一些 SSL 证书从一个容器复制到另一个容器。我想为未来找到最简单的方法。因此，我想出了这个有点乱的解决方案。不过，我喜欢它。在这种情况下，源容器称为“注册表”，目标容器是一个名为“publisherd-data”的数据卷容器。

```
superdocker exec -ti $(superdocker ps | grep registry | awk '{print $1}') \
    cat /go/src/github.com/docker/distribution/certs/domain.crt | \
    pbcopy && \
    superdocker run --rm -ti \
        --volumes-from publisherd-data \
        ubuntu bash -c "echo '$(pbpaste)' > /etc/nginx/certs/registry.domain.com.crt"

superdocker exec -ti $(superdocker ps | grep registry | awk '{print $1}') \
    cat /go/src/github.com/docker/distribution/certs/domain.key | \
    pbcopy && \
    superdocker run --rm -ti \
        --volumes-from publisherd-data \
        ubuntu bash -c "echo '$(pbpaste)' > /etc/nginx/certs/registry.domain.com.key" 
```

Enter fullscreen mode Exit fullscreen mode

为了分解它，我们首先通过在源容器上运行 docker exec 来获取证书，这是通过快速执行 docker ps | grep | awk 找到的。

```
superdocker exec -ti $(superdocker ps | grep registry | awk '{print $1}') \
    cat /go/src/github.com/docker/distribution/certs/domain.crt 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将它通过管道传输到 OS X 的 clibpoard 实用程序 pbcopy。

```
superdocker exec -ti $(superdocker ps | grep registry | awk '{print $1}') \
    cat /go/src/github.com/docker/distribution/certs/domain.crt | \
    pbcopy 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们运行一个 ethereal 容器，将它连接到目标数据卷容器。然后，我们只需将剪贴板清空到目标文件中。

```
superdocker exec -ti $(superdocker ps | grep registry | awk '{print $1}') \
    cat /go/src/github.com/docker/distribution/certs/domain.crt | \
    pbcopy && \
    superdocker run --rm -ti \
        --volumes-from publisherd-data \
        ubuntu bash -c "echo '$(pbpaste)' > /etc/nginx/certs/registry.domain.com.crt" 
```

Enter fullscreen mode Exit fullscreen mode

这是一个比喻性的说法，但它确实起了作用，却没有拯救你的任何地方。不过，您可能还想在事后清除剪贴板。

```
echo '' | pbcopy 
```

Enter fullscreen mode Exit fullscreen mode