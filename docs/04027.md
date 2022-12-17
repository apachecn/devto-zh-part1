# golang/go 1 . 9 . 1 . Linux-amd64 . tar . gz

> 原文：<https://dev.to/psnebc/golanggo191linux-amd64targz-df4>

```
➜  ~ wget https://storage.googleapis.com/golang/go1.9.1.linux-amd64.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo tar -xvf go1.9.1.linux-amd64.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo mv go /usr/local 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ export GOROOT=/usr/local/go 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ export PATH=$PATH:/usr/local/go/bin 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ go
Go is a tool for managing Go source code.

Usage:

        go command [arguments]

The commands are:

        build       compile packages and dependencies
        clean       remove object files
        doc         show documentation for package or symbol
        env print Go environment information
        bug         start a bug report
        fix         run go tool fix on packages
        fmt run gofmt on package sources
        generate    generate Go files by processing source get         download and install packages and dependencies
        install compile and install packages and dependencies
        list        list packages
        run         compile and run Go program
        test        test packages
        tool        run specified go tool
        version     print Go version
        vet         run go tool vet on packages

Use "go help [command]" for more information about a command.

Additional help topics:

        c           calling between Go and C
        buildmode   description of build modes
        filetype    file types
        gopath      GOPATH environment variable
        environment environment variables
        importpath  import path syntax
        packages    description of package lists
        testflag    description of testing flags
        testfunc    description of testing functions

Use "go help [topic]" for more information about that topic. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ go version
go version go1.9.1 linux/amd64 
```

Enter fullscreen mode Exit fullscreen mode

*   [https://golang.org/doc/install?download = go 1 . 9 . 1 . Linux-amd64 . tar . gz](https://golang.org/doc/install?download=go1.9.1.linux-amd64.tar.gz)
*   wget[https://storage . Google APIs . com/golang/go 1 . 9 . 1 . Linux-amd64 . tar . gz](https://storage.googleapis.com/golang/go1.9.1.linux-amd64.tar.gz)