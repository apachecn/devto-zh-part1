# 宣布推出 Rust 1.23

> 原文：<https://dev.to/defman/announcing-rust-123-96i>

要更新，运行`rustup update stable`。

*   `rustc`在某些情况下避免不必要的内存拷贝`->`内存使用量减少 5-10%
*   `rustdoc`的通用标记
*   [http://doc . crates . io](http://doc.crates.io)`->`T3】https://doc.rust-lang.org/cargo
*   `std::ascii::AsciiExt`被弃用，因为 trait 的方法是直接为`u8`、`char`、`[u8]`和`str`定义的
*   原子类型实现`::From`它们的非原子类型，例如:`let x = AtomicBool::From(true)`
*   [`()`现在实现了`FromIterator<()>`](https://github.com/rust-lang/rust/pull/45379)
*   [RwLock 取消了发送限制](https://github.com/rust-lang/rust/pull/45682)
*   [`cargo check`可以检查你的单元测试](https://github.com/rust-lang/cargo/pull/4592)
*   [`cargo uninstall`现在可以在一个命令中卸载多个软件包](https://github.com/rust-lang/cargo/pull/4561)

[https://blog.rust-lang.org/2018/01/04/Rust-1.23.html](https://blog.rust-lang.org/2018/01/04/Rust-1.23.html)