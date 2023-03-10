# 不要害怕为了可读性而扩展你的代码

> 原文：<https://dev.to/daniel/dont-be-afraid-to-expand-your-code-for-readability>

变量名很便宜。有条件的支票很便宜。既然有更简洁的方式来表达你的代码并增加它的可维护性，为什么还要限制自己呢？

# 使用长变量名

```
var flag = true; // :( 
```

没有很好地描述布尔应该控制什么。不要害怕扩展变量的名称来表达它们真正的作用。JavaScript minifiers 的工作是缩短变量名——这不是你的工作——你的工作是编写可读和可维护的代码。

```
var shouldSaveDocument = true; // :) 
```

# 不要害怕分解条件句

```
// :(
if (doc.hasChanged && !doc.isReadOnly && fs.hasFreeSpace && user.wantsToSave) {
    doc.save();
} 
```

当进行某种前提条件检查时，不要把它们都放在一个`if`中。逻辑运算符变得杂乱无章。很难更改或调试它们，尤其是当它们都在一行时。不要害怕分解条件句并重新测试。

```
var shouldSaveDocument = true;

// retest the flag here so that operations can be added/removed easily and can see them clearly in a diff.
if (shouldSaveDocument && !doc.hasChanged) {
     trace('not saving unchanged document');
     shouldSaveDocument = false;
}

if (shouldSaveDocument && doc.isReadOnly) {
     trace('not saving read-only document');
     shouldSaveDocument = false;
}

if (shouldSaveDocument && !fs.hasFreeSpace) {
     trace('not saving document when there is not any freespace');
     shouldSaveDocument = false;
}

if (shouldSaveDocument && !user.wantsToSave) {
     trace('not saving document as the user does not want to');
     shouldSaveDocument = false;
}

if (shouldSaveDocument) {
    doc.save();
} 
```