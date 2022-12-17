# 通过三个简单的步骤设置 Git 服务器

> 原文：<https://dev.to/andrew/setting-up-a-git-server-in-three-simple-steps>

这是我发现的一件小事，很容易做到，但可能很多人不知道:如何在任何安装了`sshd`和`git`的*nix 机器上托管你自己的 Git 遥控器。

## 先决条件

*   安装了`git`的开发机。
*   安装了`git`和`sshd`的服务器。

## 指令

#### 第一步

在您的开发机器上创建一个 git 存储库，如果您已经有一个想要上传的 repo，那么您可以跳过这一步。

```
16:01 andrew@hermes Development$ mkdir sample && cd sample
16:02 andrew@hermes sample$ git init
Initialized empty Git repository in /Users/andrew/Development/sample/.git/
16:02 andrew@hermes sample$ echo "# Sample Project" > readme.md
16:02 andrew@hermes sample$ git add .
16:02 andrew@hermes sample$ git commit
[master (root-commit) 4612ba6] Initial commit.
 Committer: Andrew <andrew@hermes.local>
 1 file changed, 1 insertion(+)
 create mode 100644 readme.md
16:02 andrew@hermes sample$ 
```

Enter fullscreen mode Exit fullscreen mode

#### 第二步

登录到您的服务器并创建 git remote。

```
16:04 andrew@hermes sample$ ssh andrew@tesseract.local
16:05 andrew@tesseract ~$ mkdir git && cd git
16:05 andrew@tesseract git$ mkdir sample && cd sample
16:05 andrew@tesseract sample$ git init --bare
Initialised empty Git repository in /raid/home/andrew/git/sample/
16:05 andrew@tesseract sample$ logout 16:06 andrew@hermes sample$ 
```

Enter fullscreen mode Exit fullscreen mode

#### 第三步

将新的遥控器添加到 git 项目中，然后推送。

```
16:12 andrew@hermes sample$ git remote add origin andrew@tesseract.local:git/sample
16:12 andrew@hermes sample$ git push --set-upstream origin master
Counting objects: 3, done.
Writing objects: 100% (3/3), 228 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To tesseract.local:git/sample
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
16:12 andrew@hermes sample$ 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

就是这样。拥有 git remote 所需要的只是远程主机上的一个空存储库和 ssh。你现在甚至可以删除你的本地回购，并再次检查出来。

```
16:12 andrew@hermes sample$ cd ..
16:14 andrew@hermes Development$ rm -rf sample
16:14 andrew@hermes Development$ git clone andrew@tesseract.local:git/sample
Cloning into 'sample'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (3/3), done.
16:14 andrew@hermes Development$ cat sample/readme.md 
# Sample Project
16:14 andrew@hermes Development$ 
```

Enter fullscreen mode Exit fullscreen mode