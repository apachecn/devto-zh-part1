# 如何在 Windows 上构建 plv8

> 原文：<https://dev.to/kjunichi/how-to-build-plv8-on-windows-4d>

这篇文章过时了。现在 plv8 正式支持 Windows！

# TL；DR；

*   [https://github . com/plv 8/plv 8/pull/239](https://github.com/plv8/plv8/pull/239)

# 让我们试试

您需要以下内容:

*   vs2015/vs2017
*   Postgres9.6
*   git(和 patch.exe)
*   cmake
*   nuget.exe

```
$ git clone https://github.com/kjunichi/plv8.git
$ cd plv8
$ git checkout support-msvc 
```

Enter fullscreen mode Exit fullscreen mode

你必须修补 PostgreSQL 的头文件。

```
copy "c:\Program Files\PostgreSQL\9.6\include\server\port\atomics\generic-msvc.h" .
set path=%path%;c:\Program Files\Git\usr\bin patch --binary -p1 generic-msvc.h < generic-msvc.h 
```

Enter fullscreen mode Exit fullscreen mode

然后，将打了补丁的 generic-msvc.h 复制到 PostgreSQL 的头文件(c:\ Program Files \ PostgreSQL \ 9.6 \ include \ server \ port \ atomics \ generic-msvc . h)。

```
$ bootstrap.bat
$ cmake . -G "Visual Studio 14 2015 Win64" -DCMAKE_INSTALL_PREFIX="C:\Program Files\PostgreSQL\9.6" -DPOSTGRESQL_VERSION=9.6
$ cmake --build . --config Release --target Package 
```

Enter fullscreen mode Exit fullscreen mode

当建设成功时，你会得到这个。

```
plv8-2.1.0-postgresql-9.6-x64.zip 
```

Enter fullscreen mode Exit fullscreen mode

将其解压缩，并复制到 PostgreSQL 目录中。