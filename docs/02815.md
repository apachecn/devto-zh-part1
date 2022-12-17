# 将 WebAssembly 用于 NodeJS 本机插件

> 原文：<https://dev.to/gfx/using-webassembly-for-a-nodejs-native-addon-dpf>

最近我发布了一个 npm [@gfx/zopfli](https://www.npmjs.com/package/@gfx/zopfli) ，这是一个绑定到 [google/zopfli](https://github.com/google/zopfli) 的 JavaScript，作为 [node-zopfli](https://www.npmjs.com/package/node-zopfli) 的嵌入式替代。

我有一个关于这个包的故事:它是用 Emscripten 构建到 WebAssembly 中的。

[https://github.com/gfx/universal-zopfli-js](https://github.com/gfx/universal-zopfli-js)

WebAssembly 是 web 浏览器的可移植可执行格式，但它不仅适用于 web，也适用于 nodejs 本地插件。也就是说，这个包包含 wasm 中预先构建的可执行二进制文件。安装它不需要编译器。不需要 node-pre-gyp 魔法(事实上 node-zopfli 在 node-pre-gyp[pierreinglebert/node-zop fli # 87](https://github.com/pierreinglebert/node-zopfli/issues/87)上有问题)。甚至 Windows 用户只需通过`npm install @gfx/zofpli`(或`yarnpkg add @gfx/zopfli`)就可以使用。

您可能会担心 wasm 的性能，但我认为它已经可以投入生产了，尽管它比原生代码慢。

下面是 NodejS 8.9.1 上 repo 的一个基准测试结果(代码见[benchmark/random-bytes . js](https://github.com/gfx/universal-zopfli-js/blob/master/benchmark/random-bytes.js)):

> #### 有效载荷大小:1
> 
> 通用-zopfli x 106 操作/秒 0.79%(采样 80 次运行)
> 节点-zopfli x 201 操作/秒 1.99%(采样 82 次运行)
> 
> #### 有效载荷尺寸:1024
> 
> universal-zopfli x 1.37 ops/sec 12.99%(11 次运行采样)
> node-zopfli x 4.62 ops/sec 3.34%(27 次运行采样)
> 
> #### 有效载荷大小:1038336
> 
> 通用-zopfli x 0.26 操作/秒 6.91% (6 次运行采样)
> 节点-zopfli x 0.39 操作/秒 1.35% (6 次运行采样)

universal-zopfli(即在 wasm 中)的速度(ops/sec)大约是 node-zopfli(即在原生代码中)的 30% - 70%。如果你在[compression-web pack-plugin](https://github.com/webpack-contrib/compression-webpack-plugin)中使用`@gfx/zopfli`作为 Zlib 的替代品，这对于生产来说已经足够好了。而且幸运的是，目前 NodeJS/V8 中 WebAssembly 实现的状态只是一个 MVP！性能上应该是越来越好了。

用 Emscripten 开发 nodejs 原生插件目前比较困难，但我相信大部分不使用系统调用的 nodejs 原生插件，包括 NodeJS 标准库如 zlib，都可以用 wasm 替代，因为它的可移植性。