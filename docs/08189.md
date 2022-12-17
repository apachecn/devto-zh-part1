# 如何让 GDB 找到你的消息来源

> 原文：<https://dev.to/dzeban/how-to-point-gdb-to-your-sources-5ho0>

所以，你有一个你或其他人开发的二进制文件，令人惊讶的是，它有一些错误。或者你只是好奇它是如何工作的。调试器是帮助解决这些问题的一个很好的工具。

当你想在汇编级调试时，这种情况真的很少，通常，你想看源代码。但是，您经常在构建主机之外的主机上调试程序，并看到这个令人沮丧的消息:

```
$ gdb -q python3.7
Reading symbols from python3.7...done.
(gdb) l
6 ./Programs/python.c: No such file or directory. 
```

Enter fullscreen mode Exit fullscreen mode

哎哟。每个人都在这里。我经常看到这种情况，而它对于明智的调试是如此重要，所以我认为了解细节并理解 GDB 如何在调试会话中显示源代码是非常重要的。

## 调试信息

这一切都从*调试信息*开始——由编译器生成的二进制文件中的特殊部分，由调试器和其他方便的工具使用。

在 GCC 中有一个著名的`-g`标志。大多数具有某种构建系统的项目要么默认使用调试信息进行构建，要么为它设置一些标志。

在 CPython 的情况下，您必须执行以下操作:

```
$ ./configure --with-pydebug
$ make -j 
```

Enter fullscreen mode Exit fullscreen mode

`--with-pydebug`将在 GCC 调用中插入`-g`。

这个`-g`选项将生成*调试段* -插入程序二进制文件的二进制段。这些部分通常是 DWARF 格式。对于 ELF 二进制文件，这些调试部分的名称类似于`.debug_*`，例如`.debug_info`或`.debug_loc`。这些调试部分使得调试的魔力成为可能——基本上，它是汇编级指令到源代码的映射。

为了发现你的程序是否有调试符号，你可以用`objdump` :
列出二进制文件的各个部分

```
$ objdump -h ./python

python: file format elf64-x86-64

Sections:
Idx Name Size VMA LMA File off Algn
  0 .interp 0000001c 0000000000400238 0000000000400238 00000238 2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  1 .note.ABI-tag 00000020 0000000000400254 0000000000400254 00000254 2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
...
 25 .bss 00031f70 00000000008d9e00 00000000008d9e00 002d9dfe 2**5
                  ALLOC
 26 .comment 00000058 0000000000000000 0000000000000000 002d9dfe 2**0
                  CONTENTS, READONLY
 27 .debug_aranges 000017f0 0000000000000000 0000000000000000 002d9e56 2**0
                  CONTENTS, READONLY, DEBUGGING
 28 .debug_info 00377bac 0000000000000000 0000000000000000 002db646 2**0
                  CONTENTS, READONLY, DEBUGGING
 29 .debug_abbrev 0001fcd7 0000000000000000 0000000000000000 006531f2 2**0
                  CONTENTS, READONLY, DEBUGGING
 30 .debug_line 0008b441 0000000000000000 0000000000000000 00672ec9 2**0
                  CONTENTS, READONLY, DEBUGGING
 31 .debug_str 00031f18 0000000000000000 0000000000000000 006fe30a 2**0
                  CONTENTS, READONLY, DEBUGGING
 32 .debug_loc 0034190c 0000000000000000 0000000000000000 00730222 2**0
                  CONTENTS, READONLY, DEBUGGING
 33 .debug_ranges 00062e10 0000000000000000 0000000000000000 00a71b2e 2**0
                  CONTENTS, READONLY, DEBUGGING 
```

Enter fullscreen mode Exit fullscreen mode

或者`readelf` :

```
$ readelf -S ./python
There are 38 section headers, starting at offset 0xb41840:

Section Headers:
  [Nr] Name Type Address Offset
       Size EntSize Flags Link Info Align
  [0] NULL 0000000000000000 00000000
       0000000000000000 0000000000000000 0 0 0
  [1] .interp PROGBITS 0000000000400238 00000238
       000000000000001c 0000000000000000 A 0 0 1

...

  [26] .bss NOBITS 00000000008d9e00 002d9dfe
       0000000000031f70 0000000000000000 WA 0 0 32
  [27] .comment PROGBITS 0000000000000000 002d9dfe
       0000000000000058 0000000000000001 MS 0 0 1
  [28] .debug_aranges PROGBITS 0000000000000000 002d9e56
       00000000000017f0 0000000000000000 0 0 1
  [29] .debug_info PROGBITS 0000000000000000 002db646
       0000000000377bac 0000000000000000 0 0 1
  [30] .debug_abbrev PROGBITS 0000000000000000 006531f2
       000000000001fcd7 0000000000000000 0 0 1
  [31] .debug_line PROGBITS 0000000000000000 00672ec9
       000000000008b441 0000000000000000 0 0 1
  [32] .debug_str PROGBITS 0000000000000000 006fe30a
       0000000000031f18 0000000000000001 MS 0 0 1
  [33] .debug_loc PROGBITS 0000000000000000 00730222
       000000000034190c 0000000000000000 0 0 1
  [34] .debug_ranges PROGBITS 0000000000000000 00a71b2e
       0000000000062e10 0000000000000000 0 0 1
  [35] .shstrtab STRTAB 0000000000000000 00b416d5
       0000000000000165 0000000000000000 0 0 1
  [36] .symtab SYMTAB 0000000000000000 00ad4940
       000000000003f978 0000000000000018 37 8762 8
  [37] .strtab STRTAB 0000000000000000 00b142b8
       000000000002d41d 0000000000000000 0 0 1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), l (large)
  I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
  O (extra OS processing required) o (OS specific), p (processor specific) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在新编译的 Python 中看到的——它有`.debug_*`部分，因此它有调试信息。

调试信息是芯片调试信息条目的集合。每个骰子都有一个标签，指定它是哪种骰子以及描述这个骰子的属性——比如变量名和行号。

## GDB 如何找到源代码

为了找到来源，GDB 解析`.debug_info`部分，找到所有标签为`DW_TAG_compile_unit`的芯片。带有这个标签的骰子有两个主要属性`DW_AT_comp_dir`(编译目录)和`DW_AT_name` -源文件的路径。它们共同为特定的编译单元(目标文件)提供了源文件的完整路径。

要解析调试信息，您可以再次使用`objdump` :

```
$ objdump -g ./python | vim - 
```

Enter fullscreen mode Exit fullscreen mode

在那里您可以看到解析后的调试信息:

```
Contents of the .debug_info section:

  Compilation Unit @ offset 0x0:
   Length: 0x222d (32-bit)
   Version: 4
   Abbrev Offset: 0x0
   Pointer Size: 8
 <0><b>: Abbrev Number: 1 (DW_TAG_compile_unit)
    <c> DW_AT_producer : (indirect string, offset: 0xb6b): GNU C99 6.3.1 20161221 (Red Hat 6.3.1-1) -mtune=generic -march=x86-64 -g -Og -std=c99
    <10> DW_AT_language : 12 (ANSI C99)
    <11> DW_AT_name : (indirect string, offset: 0x10ec): ./Programs/python.c
    <15> DW_AT_comp_dir : (indirect string, offset: 0x7a): /home/avd/dev/cpython
    <19> DW_AT_low_pc : 0x41d2f6
    <21> DW_AT_high_pc : 0x1b3
    <29> DW_AT_stmt_list : 0x0 
```

Enter fullscreen mode Exit fullscreen mode

它是这样写的——对于从`DW_AT_low_pc` = `0x41d2f6`到`DW_AT_low_pc + DW_AT_high_pc` = `0x41d2f6` + `0x1b3` = `0x41d4a9`的地址范围，源代码文件是位于`/home/avd/dev/cpython`的`./Programs/python.c`。非常简单。

这就是 GDB 试图向你们展示源代码时发生的事情:

*   解析`.debug_info`,为当前目标文件(地址范围)找到具有`DW_AT_name`属性的`DW_AT_comp_dir`
*   在`DW_AT_comp_dir/DW_AT_name`打开文件
*   向您显示文件的内容

## 如何告诉 GDB 消息来源在哪里

因此，要解决我们与`./Programs/python.c: No such file or directory.`的问题，我们必须在目标主机上获取我们的源(copy 或`git clone`)并执行以下操作之一:

### 1。重建源路径

您可以在目标主机上重建源文件的路径，这样 GDB 就可以在需要的地方找到源文件。很蠢，但会有用的。

在我的情况下，我可以只做`git clone https://github.com/python/cpython.git /home/avd/dev/cpython`并检查所需的提交。

### 2。更改 GDB 源路径

您可以在调试会话中使用`directory <dir>`命令:
将 GDB 指向新的源路径

```
(gdb) list
6 ./Programs/python.c: No such file or directory.
(gdb) directory /usr/src/python
Source directories searched: /usr/src/python:$cdir:$cwd
(gdb) list
6 #ifdef __FreeBSD__
7 #include <fenv.h>
8 #endif
9   
10 #ifdef MS_WINDOWS
11 int
12 wmain(int argc, wchar_t **argv)
13 {
14 return Py_Main(argc, argv);
15 } 
```

Enter fullscreen mode Exit fullscreen mode

### 3。设置 GDB 替换规则

如果您有一个复杂的层次结构，有时添加另一个源路径是不够的。在这种情况下，您可以使用`set
substitute-path` GDB 命令为源路径添加替换规则。

```
(gdb) list
6 ./Programs/python.c: No such file or directory.
(gdb) set substitute-path /home/avd/dev/cpython /usr/src/python
(gdb) list
6 #ifdef __FreeBSD__
7 #include <fenv.h>
8 #endif
9   
10 #ifdef MS_WINDOWS
11 int
12 wmain(int argc, wchar_t **argv)
13 {
14 return Py_Main(argc, argv);
15 } 
```

Enter fullscreen mode Exit fullscreen mode

### 4。将二进制文件移动到源代码

您可以通过将二进制文件移动到带有源代码的目录来欺骗 GDB 源代码路径。

```
mv python /home/user/sources/cpython 
```

Enter fullscreen mode Exit fullscreen mode

这将起作用，因为 GDB 将尝试在当前目录(`$cwd`)中寻找资源作为最后的手段。

### 5。用`-fdebug-prefix-map`编译

您可以在构建阶段用`-fdebug-prefix-map=old_path=new_path`选项替换源路径。下面是如何在 CPython 项目中做到这一点:

```
$ make distclean # start clean
$ ./configure CFLAGS="-fdebug-prefix-map=$(pwd)=/usr/src/python" --with-pydebug
$ make -j 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了新的来源目录:

```
$ objdump -g ./python
...
 <0><b>: Abbrev Number: 1 (DW_TAG_compile_unit)
    <c> DW_AT_producer : (indirect string, offset: 0xb65): GNU C99 6.3.1 20161221 (Red Hat 6.3.1-1) -mtune=generic -march=x86-64 -g -Og -std=c99
    <10> DW_AT_language : 12 (ANSI C99)
    <11> DW_AT_name : (indirect string, offset: 0x10ff): ./Programs/python.c
    <15> DW_AT_comp_dir : (indirect string, offset: 0x558): /usr/src/python
    <19> DW_AT_low_pc : 0x41d336
    <21> DW_AT_high_pc : 0x1b3
    <29> DW_AT_stmt_list : 0x0
... 
```

Enter fullscreen mode Exit fullscreen mode

这是最健壮的方法，因为您可以将它设置为类似于`/usr/src/<project>`的东西，从一个包中安装源代码，然后像老板一样调试。

## 结论

GDB 使用以 DWARF 格式存储的调试信息来查找源代码级别的信息。DWARF 是一种非常简单的格式——基本上，它是一棵描述程序的目标文件以及变量和函数的骰子树(调试信息条目)。

有多种方法可以帮助 GDB 找到源代码，其中最简单的是`directory`和`set substitute-path`命令，尽管`-fdebug-prefix-map`非常有用。

现在，当你有了源代码级别的信息，就去探索吧！

## 资源

*   [DWARF 调试格式介绍](http://www.dwarfstd.org/doc/Debugging%20using%20DWARF-2012.pdf)
*   [源路径上的 GDB 文档](https://sourceware.org/gdb/onlinedocs/gdb/Source-Path.html)