# Git 和交互式补丁添加

> 原文：<https://dev.to/etcwilde/git-and-the-interactive-patch-add>

有时候，在工作的时候，我们会看到一个文件的一部分被修复了，这个文件在我们的工作区域附近，但是和我们正在做的事情没有直接的关系。我们是现在进行修复，然后在添加文件时将它与 wash 一起丢弃，还是等待并希望记住修复它，以便可以单独提交它？

答案是，都不是。我们现在进行修复，但是我们可以使用`git add --interactive file.txt`或`git add --patch file.txt`选择我们想要在提交中包含的“大块”代码，而不是仅仅添加整个文件。这两种方法都会产生一个交互式控制台，显示正在更改的大块或分组行，您可以选择将其暂存或忽略。

## 去添加补丁

它是做什么的？它允许您以交互方式从文件中选择要提交的代码块，同时忽略文件中的其他更改。

在调用`git add --patch`时，它会显示文件中的变化并给出提示。你可以输入 9 个不同命令中的一个，这取决于你在第一阶段想做什么，但也有其他命令；下面是您可以给 git add - patch 的所有命令的列表

*   让这个大块头登台
*   不要上演这个大块头
*   q -退出；不要展示这个大块或任何其他大块
*   上演这出大戏，以及之后所有的大戏
*   不要存放这个块，或者文件中任何后面的块
*   选择一个帅哥去
*   / -使用正则表达式搜索合适的对象
*   留下这个未决定的大块，看下一个未决定的大块
*   J -留下这个大块头，看看下一个大块头
*   k -留下这个未决定的大块，看前面未决定的大块
*   K -留下这一大块未定，看到前一大块
*   将当前块分割成更小的块
*   e -手动编辑当前块
*   ？-打印命令列表

## 谈得够多了，请举例说明

好，让我们想象一个非常简单的设置。假设我正在向下面的 C 文件中的函数添加文档(称之为`file.c`)。

```
#include <stdio.h> 
int printHelloWorld() {
  print("hello world");
  return 0;
}

int main(int argc, char *argv[]) {
  printHelloWorld();
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我到达`printHelloWorld`函数并添加文档。

```
/**
 * printHelloWorld
 * writes "Hello world" to the console
 *
 * params: none
 * return: 0 if the function executed correctly
 */
int printHelloWorld() {
  print("hello world");
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

...但是我也注意到有人写了`print`而不是`printf`。这是一个 bug(不可否认，如果他们有正确的预提交挂钩或测试，早在它到达公共存储库之前就被发现了，但请相信我)。

我修复它，并以
结束

```
/**
 * printHelloWorld
 * writes "Hello world" to the console
 *
 * params: none
 * return: 0 if the function executed correctly
 */
int printHelloWorld() {
  printf("hello world");
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我继续添加我应该添加的文档的剩余部分，并以最后的文件
结束

```
#include <stdio.h> 
/**
 * printHelloWorld
 * writes "Hello world" to the console
 *
 * params: none
 * return: 0 if the function executed correctly
 */
int printHelloWorld() {
  printf("hello world");
  return 0;
}

/**
 * main
 * runs the program
 *
 * params: none
 * return: 0 if program executed without error
 */
int main(int argc, char *argv[]) {
  printHelloWorld();
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

文档已经完成，但我也修复了一个 bug，它不应该包含在所做的更改中。

我们运行`git add --patch file.c`

```
diff --git a/file.c b/file.c
index 7afa2a7..4293372 100644
-------- a/test.c
+++ b/test.c
@@ -1,10 +1,24 @@
 #include <stdio.h>

+/**
+ * printHelloWorld
+ * writes "Hello world" to the console
+ *
+ * params: none
+ * return: 0 if the function executed correctly
+ */
 int printHelloWorld() {
-  print("hello world");
+  printf("hello world");
   return 0;
 }

+/**
+ * main
+ * runs the program
+ *
+ * params: none
+ * return: 0 if program executed without error
+ */
 int main(int argc, char *argv[]) {
   printHelloWorld();
   return 0;
Stage this hunk [y,n,q,a,d,/,s,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

我们选择将大块分成小块，选择`s`。

```
Split into 3 hunks.
@@ -1,3 +1,10 @@
 #include <stdio.h>

+/**
+ * printHelloWorld
+ * writes "Hello world" to the console
+ *
+ * params: none
+ * return: 0 if the function executed correctly
+ */
 int printHelloWorld() {
Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想首先提交文档，我们可以按下`y`，暂存这一大块并转到下一大块。如果我们想先提交 bugfix，我们应该按下`n`，而不是暂存这个大块，但仍然会转到下一个。我选择先修复 bug(注意，我们总是可以在事后通过简单的重新排序来重新排序提交)。我们选择`n`。

```
@@ -3,5 +10,5 @@
 int printHelloWorld() {
-  print("hello world"); +  printf("hello world");
   return 0;
 }

Stage this hunk [y,n,q,a,d,/,K,j,J,g,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

这是我们唯一想要的，所以我们选择`y`。这将把我们带到最后一块，也就是文档，所以我们只需选择`q`退出。

如果我们现在运行`git status`,我们将看到 file.c 既已暂存也未暂存。运行`git diff --cached`，显示我们的 bugfix 已经准备好发布，而`git diff`显示我们的文档还没有发布。只需运行一个快速的`git commit`来提交错误修复。然后，由于剩下的只是文档，我们可以做一个普通的`git add file.c`，然后是标准的`git commit`。

我们将以一个回购协议结束，其中包含以下提交:

```
* aa7b7ec (HEAD -> master) adding documentation to functions
* ecc0aa7 bugfix: fixing print to printf
* e106991 Writing the program 
```

Enter fullscreen mode Exit fullscreen mode

## 关闭起来

使用`git add --patch`,我们可以交互地从一个文件中只选择我们想要的改变。因此，我们可以修复我们看到的更改，因为我们看到它们，但我们不需要将它们包括在提交中，这允许我们在逻辑上很好地分离更改，同时能够在工作中进行错误修复和打字错误更正。