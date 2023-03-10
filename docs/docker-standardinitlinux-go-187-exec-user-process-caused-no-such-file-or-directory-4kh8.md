# docker-Standard _ init _ Linux . go:187 exec 用户进程没有导致这样的文件或目录

> 原文：<https://dev.to/domnikl/docker-standardinitlinux-go-187-exec-user-process-caused-no-such-file-or-directory-4kh8>

我最近一直在使用 Docker 和 Go，当我在 RaspberryPi 上运行内置于 Go 容器中的工具时，我遇到了一个非常恼人的错误。我发现的所有错误报告都与 Windows 风格的行尾(CRLF)有关，但我使用的是 MacOS，所以这不可能是(至少)我这里的解决方案。

这是我一直在用的 docker file:

```
FROM alpine
WORKDIR /app
ADD washing-machine /app/washing-machine
CMD ["./washing-machine"] 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，在 mac 上构建二进制文件是行不通的，即使使用 [GOOS 和 GOARCH](https://gist.github.com/asukakenji/f15ba7e588ac42795f421b48b8aede63) 也不行，所以我现在在容器中构建二进制文件，使用来自 Docker hub:
的 [golang](https://hub.docker.com/_/golang/) 映像

```
FROM golang:1.9.3-alpine3.7
ADD . /app
WORKDIR /app
RUN cd /app && \
    apk update && \
    apk add git bash && \
    go get github.com/domnikl/ifttt-webhook && \
    go get github.com/domnikl/fritz-box && \
    go build -o washing-machine
CMD ["/app/washing-machine"] 
```

Enter fullscreen mode Exit fullscreen mode

希望这将有助于你避开这个恼人的错误信息。