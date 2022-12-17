# 用 Rust(和 WebAssembly)编写 Brainfuck 解释器

> 原文：<https://dev.to/shritesh/writing-a-brainfuck-interpreter-in-rust-and-webassembly-13f>

Rust [最近增加了对 WebAssembly 的支持，我想尝试一下。所以，我用 Rust 写了一个 brainfuck 解释器来编译 WebAssembly。你可以在这里玩](https://github.com/rust-lang/rust/pull/45905)。跟随这篇文章的其余部分来阅读我是如何做到的(以及你如何也能做到)。)

## 脑残

```
This is a hello world program in brainfuck ++++++++[>++++[>++>+++>+++>+<<<<-]>+>+>->>+[<]<-]>>.>---.+++++++..+++.>>.<-.<.+++.------.--------.>>+.>++. 
```

Enter fullscreen mode Exit fullscreen mode

Brainfuck 是一种深奥的编程语言，可以被认为是一种图灵陷阱。该语言只有 8 条指令，每条指令映射到一个特定的字符，所有其他字符都被忽略。执行环境通常有 30，000 个“内存单元”环绕运行。通过操纵指向这些存储单元的指针来执行计算。你可以在它的 [esolangs 条目](https://esolangs.org/wiki/Brainfuck)中读到更多关于 brainfuck 的内容

## 设置

Rust 中的 WebAssembly 支持需要`nightly`工具链和一些其他组件。你好，铁锈发出的[指令！](https://www.hellorust.com/setup/wasm-target/)帮我起步。

安装工具链后，让我们使用 cargo 创建一个新的 Rust 项目。

```
cargo new brainfuck
cd brainfuck 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建一个`run`函数，它接受两个字符串:源代码和程序输入，并返回输出。这将使与 JavaScript 的接口和运行测试变得容易。

我们将使用三个程序来测试我们的实现:hello world，一个类似 unix 的程序和一个 T2。

```
// src/lib.rs
pub fn run(source: &str, input: &str) -> String {
    unimplemented!()
}

#[cfg(test)]
mod test {
    use super::run;

    #[test]
    fn hello_world() {
        assert_eq!(
            run("++++++++[>++++[>++>+++>+++>+<<<<-]>+>+>->>+[<]<-]>>.>---.+++++++..+++.>>.<-.<.+++.------.--------.>>+.>++.", ""), 
            "Hello World!\n");
    }

    #[test]
    fn cat() {
        assert_eq!(run(",[.,]", "hello"), "hello");
    }

    #[test]
    fn quine() {
        // Written by Erik Bosman
        // https://copy.sh/brainfuck/prog/quine505.b
        let program = r">+++++>+++>+++>+++++>+++>+++>+++++>++++++>+>++>+++>++++>++++>+++>+++>+++++>+>+>++++>+++++++>+>+++++>+>+>+++++>++++++>+++>+++>++>+>+>++++>++++++>++++>++++>+++>+++++>+++>+++>++++>++>+>+>+>+>++>++>++>+>+>++>+>+>++++++>++++++>+>+>++++++>++++++>+>+>+>+++++>++++++>+>+++++>+++>+++>++++>++>+>+>++>+>+>++>++>+>+>++>++>+>+>+>+>++>+>+>+>++++>++>++>+>+++++>++++++>+++>+++>+++>+++>+++>+++>++>+>+>+>+>++>+>+>++++>+++>+++>+++>+++++>+>+++++>++++++>+>+>+>++>+++>+++>+++++++>+++>++++>+>++>+>+++++++>++++++>+>+++++>++++++>+++>+++>++>++>++>++>++>++>+>++>++>++>++>++>++>++>++>++>+>++++>++>++>++>++>++>++>++>+++++>++++++>++++>+++>+++++>++++++>++++>+++>+++>++++>+>+>+>+>+++++>+++>+++++>++++++>+++>+++>+++>++>+>+>+>++++>++++[[>>>+<<<-]<]>>>>[<<[-]<[-]+++++++[>+++++++++>++++++<<-]>-.>+>[<.<<+>>>-]>]<<<[>>+>>>>+<<<<<<-]>++[>>>+>>>>++>>++>>+>>+[<<]>-]>>>-->>-->>+>>+++>>>>+[<<]<[[-[>>+<<-]>>]>.[>>]<<[[<+>-]<<]<<]";
        assert_eq!(run(program, ""), program);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在运行`cargo test`失败了，但是在我们的实现完成后它会过去。之后我们将处理 WebAssembly。

## 解析来源

这是八个脑残指令:

*   `>`:向右移动指针
*   `<`:向左移动指针
*   `+`:当前单元格的增量值
*   `-`:当前单元格的递减值
*   `.`:输出当前单元格的字符
*   `,`:在当前单元格中输入字符
*   `[`:如果当前值为 0，跳到下一个匹配的`]`
*   `]`:如果当前值不为 0，跳回到下一个匹配的`[`

除了最后两条，所有的指令都相当简单。为了方便起见，我们将在解析时计算出匹配括号的位置(由下面包装的`usize`表示)。让我们用一个`Enum`来表示指令。

```
enum Instruction {
    MoveRight,
    MoveLeft,
    Increment,
    Decrement,
    Output,
    Input,
    Open(usize),
    Close(usize),
} 
```

Enter fullscreen mode Exit fullscreen mode

我们通过遍历所有字符并只保留指令字符，将字符串解析成一个`Instruction`的`Vec`。

```
fn parse(source: &str) -> Vec<Instruction> {
    let ops: Vec<char> = source
        .chars()
        .filter(|c| match *c {
            '>' | '<' | '+' | '-' | '.' | ',' | '[' | ']' => true,
            _ => false,
        })
        .collect();

    let mut instructions = vec![];
    // TODO: Fill `instructions`
    instructions
} 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然需要找到匹配括号的方法。我们可以使用一些复杂的递归算法或维护一个堆栈，但我的目标是简单。取而代之，我们将在任一方向上扫描字符，并计算括号数，以计算出字符对的索引。当我们找到一个左括号时，计数器加 1，当我们找到一个右括号时，计数器减 1。一旦计数器达到 0，我们就找到了索引。让我们为此写一个结束语。

```
 let find_matching_paren = |open, close, start_index, stop_index| {
        let mut index = start_index;
        let mut open_parens = 1;

        loop {
            if ops[index] == open {
                open_parens += 1;
            } else if ops[index] == close {
                open_parens -= 1;
            }

            if open_parens == 0 {
                return index;
            }

            if index == stop_index {
                panic!("Unmatched parens");
            } else if start_index < stop_index {
                index += 1;
            } else {
                index -= 1;
            }
        }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

这比我想象的要复杂得多，但它完成了任务。

现在我们可以匹配字符并添加到`instructions`。我们循环，将字符与它们各自的`Instruction`匹配，并将其推送到`instructions`。

```
 for i in 0..ops.len() {
        match ops[i] {
            '>' => instructions.push(Instruction::MoveRight),
            '<' => instructions.push(Instruction::MoveLeft),
            '+' => instructions.push(Instruction::Increment),
            '-' => instructions.push(Instruction::Decrement),
            '.' => instructions.push(Instruction::Output),
            ',' => instructions.push(Instruction::Input),
            '[' => instructions.push(Instruction::Open(
                find_matching_paren('[', ']', i + 1, ops.len()),
            )),
            ']' => instructions.push(Instruction::Close(
                find_matching_paren(']', '[', i - 1, 0)
            )),
            _ => {}
        };
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 运行程序

现在我们的解析器已经完成，我们可以开始执行那些指令了。

我们将程序的内存表示为一个 30000 字节的数组。我们还从输入字符串创建了一个迭代器，这样以后读取它就更容易了。

我们保留两个计数器:`instruction_counter`和`memory_counter`作为指向`instructions`和`memory`的指针。创建一个`output`字符串，该字符串将由函数填充并返回。

我们重复匹配`instruction`并在每条指令执行后将`instruction_counter`加 1。

```
use std::char;

const MEMORY_SIZE: usize = 30_000;

pub fn run(source: &str, input: &str) -> String {
    let instructions = parse(source);
    let mut input_iter = input.chars();

    let mut memory = [0u8; MEMORY_SIZE];

    let mut instruction_counter: usize = 0;
    let mut memory_counter: usize = 0;

    let mut output = String::new();

    while let Some(instruction) = instructions.get(instruction_counter) {
        // TODO: execute the instructions

        instruction_counter += 1;
    }

    output
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以对指令进行模式匹配并执行它们。

`MoveRight`和`MoveLeft`改变`memory_counter`并检查回绕，而`Increment`和`Decrement`简单地从指向的存储器中加 1 或减 1。

`Output`将当前值追加到`output`字符串。类似地，`Input`从`input_iter`读入当前单元格，如果是字符串的结尾，则为 0。

`Open`和`Close`检查当前值是否为 0，并将`instruction_counter`修改为其对应的对索引。

```
 match *instruction {
            Instruction::MoveRight => if memory_counter + 1 == MEMORY_SIZE {
                memory_counter = 0;
            } else {
                memory_counter += 1;
            },
            Instruction::MoveLeft => if memory_counter == 0 {
                memory_counter = MEMORY_SIZE - 1;
            } else {
                memory_counter -= 1;
            },
            Instruction::Increment => memory[memory_counter] += 1,
            Instruction::Decrement => memory[memory_counter] -= 1,
            Instruction::Output => {
                output.push(char::from_u32(memory[memory_counter] as u32).unwrap())
            }
            Instruction::Input => memory[memory_counter] = input_iter.next().unwrap_or('\0') as u8,
            Instruction::Open(index) => if memory[memory_counter] == 0 {
                instruction_counter = index;
            },

            Instruction::Close(index) => if memory[memory_counter] != 0 {
                instruction_counter = index;
            },
        } 
```

Enter fullscreen mode Exit fullscreen mode

这比我想象的要容易。我们的测试通过了。我们已经成功地编写了一个工作解释器。

## web 组装

Rust 中围绕 WebAssembly 的工具仍处于初级阶段，但社区每天都在改进。有许多(不安全的)样板文件需要编写。我用过 [Hello Rust 的代码！](https://www.hellorust.com/demos/)为此。

我们需要使用`no_mangle`编译器指令来防止[名称混淆](https://doc.rust-lang.org/book/second-edition/ch19-01-unsafe-rust.html#calling-rust-functions-from-other-languages)。我们还需要从 JavaScript 中手动调用我们的分配器和回收器，这样我们就可以公开它们。我们的函数将接收指向源字符串和输入字符串的指针，因此，我们需要将它们转换为 Rust 字符串。

```
use std::mem;
use std::ffi::{CStr, CString};
use std::os::raw::{c_char, c_void};

#[no_mangle]
pub extern "C" fn alloc(size: usize) -> *mut c_void {
    let mut buf = Vec::with_capacity(size);
    let ptr = buf.as_mut_ptr();
    mem::forget(buf);
    return ptr as *mut c_void;
}

#[no_mangle]
pub extern "C" fn dealloc(ptr: *mut c_void, cap: usize) {
    unsafe {
        let _buf = Vec::from_raw_parts(ptr, 0, cap);
    }
}

#[no_mangle]
pub extern "C" fn dealloc_str(ptr: *mut c_char) {
    unsafe {
        let _ = CString::from_raw(ptr);
    }
}

#[no_mangle]
pub extern "C" fn brainfuck(source: *mut c_char, input: *mut c_char) -> *mut c_char {
    let source_str = unsafe { CStr::from_ptr(source).to_str().unwrap() };
    let input_str = unsafe { CStr::from_ptr(input).to_str().unwrap() };

    let output = run(source_str, input_str);

    CString::new(output).unwrap().into_raw()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以编译 WebAssembly 了。我们还使用`wasm-gc`来移除不需要的符号，因为链接器还没有优化。

```
rustc +nightly --target wasm32-unknown-unknown --crate-type=cdylib  -O src/lib.rs
wasm-gc lib.wasm brainfuck.wasm 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以创建 HTML 页面并使用它。我们将使用 Hello，Rust！演示让我们的生活更轻松。有很多样板文件，但它们很简单。

```
 <script src="bundle.js"></script>
  <script>
    window.Module = {}
    var Brainfuck = {
      run: function (source, input) {
        let src_buf = newString(Module, source);
        let input_buf = newString(Module, input);
        let outptr = Module.brainfuck(src_buf, input_buf);
        let result = copyCStr(Module, outptr);
        Module.dealloc(src_buf);
        Module.dealloc(input_buf);
        Module.dealloc(outptr);
        return result;
      }
    }
    fetchAndInstantiate("./brainfuck.wasm", {})
      .then(mod => {
        Module.alloc = mod.exports.alloc;
        Module.dealloc = mod.exports.dealloc;
        Module.dealloc_str = mod.exports.dealloc_str;
        Module.brainfuck = mod.exports.brainfuck;
        Module.memory = mod.exports.memory;

        alert(Brainfuck.run(",[.,]", "Hello World!"));
      });  </script> 
```

Enter fullscreen mode Exit fullscreen mode

`newString`分配内存并复制 WebAssembly 环境使用的字符串。类似地，`copyCStr`将结果复制回 javascript。所有这些都需要稍后使用`Module.dealloc`手动解除分配。在使用`fetchAndInstantiate`加载 WebAssembly 文件后，这些都以 [bundle.js](https://github.com/badboy/hellorust/blob/master/demos/bundle.js) 期望的格式干净地包装在`Brainfuck.run`中。

我们可以执行程序并使用`Brainfuck.run(source, input)`获得结果字符串。

就是这样。你可以在这里看到最终的 Rust 源代码[，在这里](https://github.com/shritesh/brainfuck-rs-wasm/blob/master/src/lib.rs)看到 HTML 源代码[。这里的主持版本是](https://github.com/shritesh/brainfuck-rs-wasm/blob/master/docs/index.html)。

## 结论

我从编写解释器中获得了很多乐趣。Rust 的`Enum` s、模式匹配和安全性使得这种语言非常强大，但又容易理解。当程序编译完成并通过测试时，我对它很有信心。编译器错误非常有用。

WebAssembly 无疑是 Web 开发中的下一件大事，我很高兴 Rust 站在支持它的最前沿。目前，做任何事情都需要很多样板文件，文档也很少，但我相信这在未来会有所改善。

我希望你喜欢读这篇文章。你可以在推特( [@shritesh](https://twitter.com/shritesh) )上关注我，或者给我发邮件到 [shr@ite.sh](//mailto:shr@ite.sh)