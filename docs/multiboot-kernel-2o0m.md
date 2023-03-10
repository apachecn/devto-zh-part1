# 多重引导内核

> 原文：<https://dev.to/dzeban/multiboot-kernel-2o0m>

作为一个疯子，在我的业余时间(除了其他事情之外),我正在编写一个操作系统内核。目前没什么，因为我正在研究 x86 系统的引导过程。并且，到目前为止，为了提交我的知识，我将首先解释引导普通内核的简单但非常重要的步骤。

## “仁”

举例来说，我将使用在 NASM 汇编中编写的“Hello world”内核(从 github 获取源代码[):](http://github.com/dzeban/hello-kernel) 

```
 global start                    ; the entry symbol for ELF

    MAGIC_NUMBER equ 0x1BADB002     ; define the magic number constant
    FLAGS        equ 0x0            ; multiboot flags
    CHECKSUM     equ -MAGIC_NUMBER  ; calculate the checksum
                                    ; (magic number + checksum + flags should equal 0)

    section .text:                  ; start of the text (code) section
    align 4                         ; the code must be 4 byte aligned
        dd MAGIC_NUMBER             ; write the magic number to the machine code,
        dd FLAGS                    ; the flags,
        dd CHECKSUM                 ; and the checksum

    start:                          ; the loader label (defined as entry point in linker script)
      mov ebx, 0xb8000 ; VGA area base
      mov ecx, 80*25 ; console size

      ; Clear screen
      mov edx, 0x0020;  space symbol (0x20) on black background
    clear_loop:
      mov [ebx + ecx], edx
      dec ecx
      cmp ecx, -1
      jnz clear_loop

      ; Print red 'A'
      mov eax, ( 4 << 8 | 0x41) ; 'A' symbol (0x41) print in red (0x4)
      mov [ebx], eax

    .loop:
        jmp .loop                   ; loop forever 
```

Enter fullscreen mode Exit fullscreen mode

这个内核与 VGA 缓冲区一起工作——它从旧的 BIOS 信息中清除屏幕，并用红色打印大写字母“A”。之后，它就永远循环下去。

用
编译

```
nasm -f elf32 kernel.S -o kernel.o 
```

Enter fullscreen mode Exit fullscreen mode

`nasm`生成一个不适合执行的目标文件，因为它的地址需要从基址`0x0`重新定位，结合其他段，解析外部符号等等。这是链接程序的一项工作。

为用户空间编译程序时，应用程序`gcc`将使用默认链接器脚本为您调用链接器。但是对于内核空间代码，您必须提供自己的链接脚本，它将告诉您将代码的各个部分放在哪里。我们的内核代码只有`.text`段，没有栈或堆，多引导头被硬编码到`.text`段。所以链接脚本非常简单:

```
ENTRY(start) /* the name of the entry label */

SECTIONS {
    . = 0x00100000; /* the code should be loaded at 1 MB */

    .text ALIGN (0x1000) : /* align at 4 KB */
    {
        *(.text) /* all text sections from all files */
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经在[限制程序内存文章](http://alex.dzyoba.com/programming/restrict-memory.html#Linker)中接触到链接部分。

基本上，我们是在说“从 1mb 开始我们的代码，并将部分`.text`放在 4K 对齐的开头。切入点是`start`”。

这样链接:

```
ld -melf_i386 -T link.ld kernel.o -o kernel 
```

Enter fullscreen mode Exit fullscreen mode

并且直接用 QEMU 运行内核:

```
$ qemu-system-i386 -kernel kernel 
```

Enter fullscreen mode Exit fullscreen mode

你猜对了:

[![QEMU direct kernel](img/b4e268efd929860538948a7edfc44937.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QK4OCAon--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/qemu-kernel.png)

## 多足部分

当计算机通电时，它开始根据它的“复位向量”执行代码。对于现代 x86 处理器，它是`0xFFFFFFF0`。在这个地址，主板设置跳转指令到 BIOS 代码。CPU 处于“实模式”(带分段的 16 位寻址(高达 1mb)，无保护，无分页)。

BIOS 做所有通常的工作，如扫描设备，初始化它，并找到一个可启动的设备。找到可引导设备后，它将控制权传递给该设备上的引导加载程序。

Bootloader 从磁盘加载自身(在多阶段的情况下)找到内核并将其加载到内存中。在黑暗的过去，每个操作系统都有自己的格式和规则，所以有各种不兼容的引导加载程序。但是现在有了一个多重引导规范，它给了你的内核一些保证和便利，作为交换，你要遵守这个规范并提供多重引导头。

对多重引导规范的依赖是一件大事，因为它有助于使生活变得更加容易，这就是为什么:

*   符合多重引导的引导程序将系统[设置为明确定义的状态](http://www.gnu.org/software/grub/manual/multiboot/multiboot.html#Machine-state)，最明显的是:
    *   将 CPU 转换到保护模式，以允许您访问所有内存
    *   启用 A20 线——在实模式下访问额外段的老习惯
    *   全局描述符表和中断描述符表没有定义，所以操作系统必须建立自己的描述符表
*   支持多重引导的操作系统内核:
    *   可以(也应该)是 ELF 格式
    *   必须仅设置 12 个字节才能正确引导

一般来说，引导支持多重引导的内核很简单，尤其是在 ELF 格式的情况下:

*   多重引导引导程序在内核映像的前 8K 字节中搜索多重引导头(通过魔法`0x1BADB002`找到它)
*   如果图像是 ELF 格式，它将根据节表加载节
*   如果映像不是 ELF 格式，它会将内核加载到地址字段或标志字段中提供的地址。

在我们内核的文本部分，我们已经做到了:

```
 MAGIC_NUMBER equ 0x1BADB002     ; define the magic number constant
    FLAGS        equ 0x0            ; multiboot flags
    CHECKSUM     equ -MAGIC_NUMBER  ; calculate the checksum
                                    ; (magic number + checksum + flags should equal 0)

    section .text:                  ; start of the text (code) section
    align 4                         ; the code must be 4 byte aligned
        dd MAGIC_NUMBER             ; write the magic number to the machine code,
        dd FLAGS                    ; the flags,
        dd CHECKSUM                 ; and the checksum 
```

Enter fullscreen mode Exit fullscreen mode

我们没有指定任何标志，因为我们不需要引导加载程序的任何东西，比如内存映射之类的，引导加载程序也不需要我们的任何东西，因为我们是 ELF 格式的。对于其他格式，您必须在其多重引导头中提供加载地址。多重引导头非常简单:

| 抵消 | 类型 | 字段名 | 注释
 |
| Zero | u32 | 魔法 | 必需的
 |
| four | u32 | 旗帜 | 必需的
 |
| eight | u32 | 校验和 | 必需的
 |
| Twelve | u32 | 表头 _ 地址 | 如果标志[16]被设置
 |
| Sixteen | u32 | 加载地址 | 如果标志[16]被设置
 |
| Twenty | u32 | 加载结束地址 | 如果标志[16]被设置
 |
| Twenty-four | u32 | bss _ 结束地址 | 如果标志[16]被设置
 |
| Twenty-eight | u32 | 条目 _ 地址 | 如果标志[16]被设置
 |
| Thirty-two | u32 | 模式类型 | 如果标志[2]被设置
 |
| Thirty-six | u32 | 宽度 | 如果标志[2]被设置
 |
| Forty | u32 | 高度 | 如果标志[2]被设置
 |
| forty-four | u32 | 深度 | 如果标志[2]被设置
 |

## 开机

现在，让我们像严肃的人一样启动我们的内核。

首先，我们在`grub2-mkrescue`的帮助下创建一个 ISO 映像。创建一个这样的层次:

```
isodir/
└── boot
    ├── grub
    │   └── grub.cfg
    └── kernel 
```

Enter fullscreen mode Exit fullscreen mode

其中 grub.cfg 为:

```
menuentry "kernel" {
    multiboot /boot/kernel
} 
```

Enter fullscreen mode Exit fullscreen mode

然后调用`grub2-mkrescue` :

```
grub2-mkrescue -o hello-kernel.iso isodir 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在任何 PC 兼容机上启动它:

```
qemu-system-i386 -cdrom hello-kernel.iso 
```

Enter fullscreen mode Exit fullscreen mode

我们将看到 grub2 菜单，在这里我们可以选择我们的“内核”并看到红色的“A”字母。

是不是很棒？