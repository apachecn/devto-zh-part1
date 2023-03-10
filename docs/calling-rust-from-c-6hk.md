# 从 C#调用 Rust

> 原文：<https://dev.to/living_syn/calling-rust-from-c-6hk>

# [T1】简介](#intro)

这是一个创建 Rust DLL 并从 C#调用它的指南。我们将涵盖原生返回值以及结构。本指南假设使用 windows 环境，并且假设您已经安装了 rust 并设置了 c#开发环境。

## Rust 项目设置和依赖关系

创建一个 rust 库并把它编译成 DLL 非常简单。首先，导航到项目所在的文件夹，并运行以下命令:

`cargo new cs_call_rst`

这将创建一个名为 cs_call_rust 的新文件夹，并用“src”文件夹和 cargo.toml 文件初始化它。我们可以通过切换到新创建的`cs_call_rust`文件夹并运行:

`cargo build`

运行这个命令后，您会注意到现在有一个名为`target`的新文件夹，它包含一个名为`debug`的文件夹，其中有我们构建的输出。然而，有一个问题，我们没有建立一个 dll，我们建立了一个. rlib 文件。要告诉 rust 编译器创建一个 dll，打开`cargo.toml`，让它看起来像下面这样:

```
[package]
name = "cs_call_rst"
version = "0.1.0"
authors = ["Jeremy Mill <jeremymill@gmail.com>"]

[lib]
name="our_rust"
crate-type = ["dylib"]

[dependencies] 
```

Enter fullscreen mode Exit fullscreen mode

`[package]`部分告诉编译器一些关于我们正在构建的包的元数据，比如我们是谁，这是什么版本。下一部分，`[lib]`是我们告诉编译器创建一个 DLL 的地方，并将其命名为‘our _ rust’。当您再次运行`cargo build`时，您现在应该在输出目录中看到`our_rust.dll`。

## 第一外部防锈功能

现在我们已经设置好了项目，让我们添加第一个 rust 函数，然后从 c#调用它。打开`lib.rs`，增加以下功能:

```
#[no_mangle]
pub extern fn add_numbers(number1: i32, number2: i32) -> i32 {
    println!("Hello from rust!");
    number1 + number2
} 
```

Enter fullscreen mode Exit fullscreen mode

第一行，`#[no_mangle]`告诉编译器保留名称`add_numbers`,以便我们可以从外部代码调用它。接下来，我们定义一个外部可用的公共函数，它接受两个 32 位整数，并返回一个 32 位整数。该方法打印“hello world”并返回相加的数字。

运行`cargo build`来构建我们的 DLL，因为我们将在下一步调用这个函数。

## C#项目设置

我将假设您正在使用 visual studio 进行 c#开发，并且您已经掌握了 c#和设置项目的基本知识。因此，根据这个假设，继续在 visual studio 中创建一个新的 c#控制台应用程序。我给我的取名`rust_dll_poc`。

在编写任何代码之前，我们需要将 DLL 添加到项目中。右键单击我们的项目并选择`add -> existing item -> our_rust.dll`。接下来，在右下角的“属性”窗口(dll 突出显示)，确保将“复制输出目录”从“不复制”更改为“总是复制”。这确保了 dll 被复制到构建目录，这将使调试更加容易。注意，每次对 DLL 进行更改时，都需要重复这一步(或编写脚本)。

接下来，将下面的 using 语句添加到我们的应用程序的顶部:

```
using System.Runtime.InteropServices; 
```

Enter fullscreen mode Exit fullscreen mode

这个库将让我们加载 DLL 并调用它。

接下来添加下面的私有实例变量`Program`类:

```
[DllImport("our_rust.dll")]
private static extern Int32 add_numbers(Int32 number1, Int32 number2); 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们声明我们正在导入一个外部函数，名为 add_numbers，它的签名，以及我们从哪里导入它。您可能知道 c#通常将`int`视为 32 位有符号整数，然而，当处理外来函数时，明确您在两端期望的确切数据类型要安全得多，因此我们明确声明，我们期望返回 32 位有符号整数，并且输入应该是 32 位有符号整数。

现在，让我们调用函数。将以下代码添加到`main` :

```
static void Main(string[] args)
{
    var addedNumbers = add_numbers(10, 5);
    Console.WriteLine(addedNumbers);
    Console.ReadLine();
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到以下输出:

```
Hello from rust!
15 
```

Enter fullscreen mode Exit fullscreen mode

注意！:如果您在尝试运行上述代码时看到一个`System.BadImageFormatException`，那么您(很可能)在我们的 rust dll 和 c#应用程序的构建目标中有一个不匹配。C#和 visual studio 默认为 x86 构建，rust-init 默认为 64 位架构安装 64 位编译器。你可以按照[这里](https://msdn.microsoft.com/en-us/library/ms185328.aspx)列出的步骤构建 64 位版本的 c#应用程序

## 返回简单结构

好，太棒了，我们现在知道如何返回基本值了。但是结构呢？我们将从一个不需要内存分配的基本结构开始。首先，让我们通过添加下面的代码来定义我们的结构和一个在`lib.rs`中返回它的实例的方法:

```
#[repr(C)]
pub struct SampleStruct {    
    pub field_one: i16,
    pub field_two: i32,
}

#[no_mangle]
pub extern fn get_simple_struct() -> SampleStruct {
    SampleStruct {
        field_one: 1,
        field_two: 2
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要在 c#中定义与 rust struct 匹配的相应 struct，导入新函数，并调用它！将以下内容添加到我们的`program.cs`文件中:

编辑:正如 Kalyanov Dmitry 指出的，我没有添加 Struct 布局注释。这个注释确保了 C#编译器不会重新排列我们的结构并破坏我们的返回值

```
namespace rust_dll_poc
{
    [StructLayout(LayoutKind.Sequential)]
    public struct SampleStruct
    {
        public Int16 field_one;
        public Int32 field_two;
    }

    class Program
    {
        [DllImport("our_rust.dll")]
        private static extern SampleStruct get_simple_struct();
        ... 
```

Enter fullscreen mode Exit fullscreen mode

然后我们在`Main` :
内部调用它

```
static void Main(string[] args)
{
    var simple_struct = get_simple_struct();
    Console.WriteLine(simple_struct.field_one);
    Console.WriteLine(simple_struct.field_two);
    .... 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到以下输出(您记得将更新后的 DLL 移动到项目目录中，对吗？)

```
1
2 
```

Enter fullscreen mode Exit fullscreen mode

## 琴弦呢？

在我看来，字符串是编程中最微妙复杂的东西。在两种不同的语言之间工作时尤其如此，在处理托管和非托管代码之间的接口时更是如此。我们的策略是将静态字符串存储到堆中，并在一个结构体中返回一个`char *`到内存地址。我们将把这个地址存储在 rust 中的一个静态变量中，以便于释放。我们还将定义一个函数`free_string`,当被 c#调用时，它将通知 rust 我们已经处理完了字符串，并且可以释放内存。这里值得注意的是，这非常简单，而且肯定不是线程安全的。这应该如何“实际”实现在很大程度上取决于您正在编写的代码。

让我们首先向所需的标准库添加一个 using 语句:

```
//external crates
use std::os::raw::c_char;
use std::ffi::CString; 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们将创建一个可变的静态变量，它将保存我们放入堆中的字符串的地址:

```
static mut STRING_POINTER: *mut c_char = 0 as *mut c_char; 
```

Enter fullscreen mode Exit fullscreen mode

重要的是要知道，每当我们访问这个静态变量时，我们都会将这个块标记为不安全的。关于原因的更多信息可以在这里找到[。](https://doc.rust-lang.org/book/first-edition/const-and-static.html)

接下来，我们将编辑我们的结构，使其具有一个 c_char 字段:

```
#[repr(C)]
pub struct SampleStruct {    
    pub field_one: i16,
    pub field_two: i32,
    pub string_field: *mut c_char,
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们创建两个助手方法，一个将字符串存储到堆上并转移所有权(私有)，另一个释放内存(公共)。关于这些方法的信息以及真正重要的安全注意事项可以在[这里](https://doc.rust-lang.org/std/ffi/struct.CString.html#method.from_raw)T2【找到

```
fn store_string_on_heap(string_to_store: &'static str) -> *mut c_char {
    //create a new raw pointer
    let pntr = CString::new(string_to_store).unwrap().into_raw();
    //store it in our static variable (REQUIRES UNSAFE)
    unsafe {
        STRING_POINTER = pntr;
    }
    //return the c_char
    return pntr;
}

#[no_mangle]
pub extern fn free_string() {
    unsafe {
        let _ = CString::from_raw(STRING_POINTER);
        STRING_POINTER = 0 as *mut c_char;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们更新`get_simple_struct`来包含我们的代码:

```
#[no_mangle]
pub extern fn get_simple_struct() -> SampleStruct {
    let test_string: &'static str = "Hi, I'm a string in rust";
    SampleStruct {
        field_one: 1,
        field_two: 2,
        string_field: store_string_on_heap(test_string),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们的 rust 代码都准备好了！接下来让我们编辑 C#结构。我们需要为字符串字段使用 IntPtr 类型。我们应该能够使用“MarshalAs”数据属性来自动将该字段转换为字符串，但是我还没有能够使它工作。

```
[StructLayout(LayoutKind.Sequential)]
public struct SampleStruct
{
    public Int16 field_one;
    public Int32 field_two;
    public IntPtr string_field;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在另一个控制台下面的 main 中添加下面一行。WriteLines，我们应该可以看到我们的文本:

```
Console.WriteLine(Marshal.PtrToStringAnsi(simple_struct.string_field)); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要告诉 rust 释放内存是可以的，所以我们需要导入`free_string`方法，就像我们对其他方法所做的那样，并将其命名为` free _ string()；

输出应该是这样的:

 ``1
2
Hi, I'm a string in rust`` 

 ``我希望这些对你有用！完整的 c#可以在这里找到[，完整的 rust 代码可以在这里](https://gist.github.com/LivingInSyn/3f80d2bef2d7b9c7aa8e1efa277f80db)找到[。祝你好运，并快乐编码！](https://gist.github.com/LivingInSyn/81395282fdeeb8257b60ff279e2a39b4)``