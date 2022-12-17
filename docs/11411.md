# 节点程序包相关性的版本范围

> 原文：<https://dev.to/adamkdean/version-ranges-for-node-package-dependencies-1mi7>

我从 npmjs.org 拿走了这个，但至少在这里我会再找到它。

`version`必须与`version`完全匹配

`>version`必须大于`version`

`>=version`等

`<version`

`<=version`

`~version`大致相当于

`^version`【兼容版本】

`1.2.x``1.2.0``1.2.1`等。，但不是`1.3.0`

`http://...`见

`*`下面的 URLs 作为依赖项’匹配任何版本

`""`(只是一个空字符串)与*

`version1 - version2`相同与`>=version1 <=version2`。如果满足`range1`或`range2`，则

`range1 || range2`通过。

`git...`见下面

`user/repo`见下面【GitHub URLs】见下面

`tag`一个被标记并作为标记发布的特定版本`path/path/path`见下面本地路径

例如，这些都是有效的:

```
{ "dependencies" :
    { "foo" : "1.0.0 - 2.9999.9999"
    , "bar" : ">=1.0.2 <2.1.2"
    , "baz" : ">1.0.2 <=2.3.4"
    , "boo" : "2.0.1"
    , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
    , "asd" : "http://asdf.com/asdf.tar.gz"
    , "til" : "~1.2"
    , "elf" : "~1.2.3"
    , "two" : "2.x"
    , "thr" : "3.3.x"
    , "lat" : "latest"
    , "dyl" : "file:../dyl"
    }
} 
```

Git urls 的形式可以是:

```
git://github.com/user/project.git#commit-ish
git+ssh://user@hostname:project.git#commit-ish
git+ssh://user@hostname/project.git#commit-ish
git+http://user@hostname/project/blah.git#commit-ish
git+https://user@hostname/project/blah.git#commit-ish 
```

更多阅读:[https://www.npmjs.org/doc/files/package.json.html](https://www.npmjs.org/doc/files/package.json.html)