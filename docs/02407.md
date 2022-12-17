# 要求('。/opencv.js ')对于使用 opencv.js 是不够的

> 原文：<https://dev.to/kjunichi/requireopencvjs-is-not-enough-for-using-opencvjs-8ff>

# 开始

我注意到我可以使用官方 OpenCV 和 JavaScript(WASM)。

*   [https://docs.opencv.org/3.3.1/d4/da1/tutorial_js_setup.html](https://docs.opencv.org/3.3.1/d4/da1/tutorial_js_setup.html)

# 问题

当我写下面的代码时:

```
const cv = require('./opencv.js');

let mat = new cv.Mat();
console.log(mat.size());
mat.delete(); 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我只有这样的错误消息:

```
TypeError: cv.Mat is not a constructor
    at Object.<anonymous> (/Volumes/EXTHDD3/local/opencv/build_wasm/bin/dame.js:3:11)
    at Module._compile (module.js:641:30)
    at Object.Module._extensions..js (module.js:652:10)
    at Module.load (module.js:560:32)
    at tryModuleLoad (module.js:503:12)
    at Function.Module._load (module.js:495:3)
    at Function.Module.runMain (module.js:682:10)
    at startup (bootstrap_node.js:191:16)
    at bootstrap_node.js:613:3 
```

Enter fullscreen mode Exit fullscreen mode

# 方案 1

我想我需要一些等待，我重写了下面的代码:

```
const cv = require('./opencv.js');

setTimeout(()=>{
        (()=>{
                let mat = new cv.Mat();
                console.log(mat.size());
                mat.delete();
        })();
},10000); 
```

Enter fullscreen mode Exit fullscreen mode

这一次，没有错误消息。

```
{ width: 0, height: 0 } 
```

Enter fullscreen mode Exit fullscreen mode

# 方案二

解决方案 1 不好看，依赖于 CPU 的速度。

我发现 Emscipten 在准备调用 main 函数的时候会调用 onRuntimeInitialized。

```
const cv = require("./opencv.js");
cv['onRuntimeInitialized']=()=>{
          let mat = new cv.Mat();
          console.log(mat.size());
          mat.delete();
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
{ width: 0, height: 0 } 
```

Enter fullscreen mode Exit fullscreen mode