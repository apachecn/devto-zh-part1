# NativeScript 和 Vue.js

> 原文:# t0]https://dev . to/fut oasis/native script-and-vuej-ckn

# NativeScript 和 Vue.js

[![image](img/305ba8dd6b893c2217c023a706ef8500.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tHMFxglb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/in9g1xwqqyxj2auggxrx.png)

[NativeScript](https://www.nativescript.org/) 支持 iOS 和 Android 的跨平台。由前端平台解决方案组成。

# 设置

参考是[这篇博文](https://www.nativescript.org/blog/vue-and-nativescript-in-one-minute)。

```
> npm install -g nativescript 
```

Enter fullscreen mode Exit fullscreen mode

添加的命令是 tns。

```
> which tns
/Users/futoase/.nodenv/shims/tns 
```

Enter fullscreen mode Exit fullscreen mode

创建示例产品(目录)。

```
> tns create sample-project 
```

Enter fullscreen mode Exit fullscreen mode

安装[原生脚本视图插件](https://www.npmjs.com/package/nativescript-vue)

```
> cd sample-project/
> npm install --save nativescript-vue 
```

Enter fullscreen mode Exit fullscreen mode

编辑 app.js ( [来自样本的参考](https://www.nativescript.org/blog/vue-and-nativescript-in-one-minute) )

```
const Vue = require('nativescript-vue/dist/index');

new Vue({
  template: `
    <page>
      <stack-layout>
        <label text="Hello Vue!" style="background-color:#41b883;color:#ffffff;padding:10;text-align:center"></label>
      </stack-layout>
    </page>
  `,
}).$start(); 
```

Enter fullscreen mode Exit fullscreen mode

## 运行在仿真器上

### iOS

```
> tns run ios 
```

Enter fullscreen mode Exit fullscreen mode

[![image](img/b1ef4762a84393d32aef3a80ec660cd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HuRt1dt_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/flvnonn8351bq1g0ca3b.png)

### 安卓

编译错误

*   原因是代码错误。
    *   [这个代码点。](https://github.com/telerik/mobile-cli-lib/blob/06d4f680523cf79ecc7df47feeb61cf6acc7a2a7/sys-info-base.ts#L22)

```
 try {
                // different java has different format for `java -version` command
                const output = (await this.$childProcess.spawnFromEvent("java", ["-version"], "exit")).stderr;
                this.javaVerCache = /(?:openjdk|java) version \"((?:\d+\.)+(?:\d+))/i.exec(output)[1];
            } catch (e) {
                this.javaVerCache = null;
            } 
```

Enter fullscreen mode Exit fullscreen mode

未决问题。[问题#3148](https://github.com/NativeScript/nativescript-cli/issues/3148)

[![error](img/2f6f1c1bb19603a9102eb58c50790b59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AsgXXcvU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xb94lfb019apdfc5xsa8.png)

因为 Java 9 从 stdout 输出版本号，但应该是从 Stdout 中获取版本号的代码...

# 引用

*   [https://www . native script . org/blog/vue-and-native script-in-one-minute](https://www.nativescript.org/blog/vue-and-nativescript-in-one-minute)
*   [https://developer . telerik . com/products/native script/native-IOs-Android-vue-native script/](https://developer.telerik.com/products/nativescript/native-ios-android-vue-nativescript/)
*   [https://github . com/native script/native script-CLI/issues/3152](https://github.com/NativeScript/nativescript-cli/issues/3152)