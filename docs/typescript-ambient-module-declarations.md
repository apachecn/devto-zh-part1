# Typescript 环境模块声明

> 原文：<https://dev.to/jmourtada/typescript-ambient-module-declarations>

最初发布于 [www.mourtada.se](https://www.mourtada.se/typescript-ambient-module-declarations/)

在 ES2015 中，JavaScript 使用 export 和 import 关键字获得了模块的概念。Typescript 支持这一点，而且只要你用 TypeScript 编写模块，它就能很好地工作。但是要使用不是用 TypeScript 编写的外部库或代码，您需要有一个类型声明。对于所有主要的库，这些都已经存在于确定类型的库中，可以通过[类型搜索](https://microsoft.github.io/TypeSearch/)进行查询。但是有时候你必须自己写一个。

我在理解如何使用这些声明时遇到了一些问题，并且发现文档有点难以理解，这促使我写了这篇文章。

## 环境模块

来自 TypeScript 文档:

> 我们称不定义实现的声明为“环境”。通常，这些是在. d.ts 文件中定义的。如果你熟悉 C/C++，你可以认为这些是. h 文件

因此，要为已经用 JavaScript 编写的代码编写类型声明，我们必须编写一个环境模块。如上所述，这些几乎总是在以扩展名`.d.ts`结尾的文件中定义。

我们如何定义环境模块？有两种方法。

### 全局声明

当使用`declare module`创建一个环境模块时，`.d.ts`文件的命名并不重要，重要的是该文件包含在编译中。

```
declare module "my-module" {
    export const a: number;
    export function b(paramA: number): void;
} 
```

Enter fullscreen mode Exit fullscreen mode

当上面的文件包含在编译类型脚本中时，它会注册有一个名为`my-module`的模块，然后可以导入这个模块

```
import { a, b } from "my-module" 
```

Enter fullscreen mode Exit fullscreen mode

在编译中包含声明文件有不同的方法

1.  在`typeRoots`编译器选项中指定路径，在【typeRoots 下的所有全局声明文件将被自动包含。这可以通过`types`属性编译选项来控制，您可以显式地控制哪些定义应该被自动包含
2.  在`tsconfig`中指定 files 属性，以便包含声明文件
3.  使用三斜线指令`/// <reference path="..." />`
4.  借助于`tsconfig`中的路径编译器选项

来自 TypeScript 文档

> 请记住，只有在使用带有全局声明的文件时，自动包含才是重要的(与声明为模块的文件相反)。例如，如果您使用 import“foo”语句，TypeScript 可能仍然会在 node _ modules & node _ modules/@ types 文件夹中查找 foo 包。

### 声明为模块的文件

使用顶级导出和导入模块声明，`.d.ts`不需要包含在编译中。这里重要的是，这个文件被命名为`index.d.ts`，并且驻留在一个以模块命名的文件夹中，在本例中这个模块是`my-module`

```
// index.d.ts
export const a: number;
export function b(paramA: number): void;

// In a file importing the library
import { a, b } from "my-module" 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，TypeScript 会尝试查找`my-module`。它将尝试许多不同的步骤来查找代码(ts 文件)和声明(. d.ts 文件)。其中一个步骤是在`node_modules/@types`中寻找声明文件。它将查找一个与导入的模块同名的文件夹，其 index.d.ts 文件看起来像上面的文件。

有时，您不想将声明文件发布到具有自定义类型声明的 definitelytyped 和 have 文件夹，因此通知 TypeScript 在 node_modules/@types 之外的其他文件夹中查找声明。这可以借助`compilerOption` `paths`来完成。

```
{
    "baseUrl": ".",
    "paths": {
        "*": [
            "custom-typings/*"
        ]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`tsconfig.json`中的这个配置，TypeScript 将在`custom-typings`文件夹中查找代码和声明文件。

要验证 TypeScript 试图在哪里解决问题，您可以使用`traceresolution`标志
运行编译器

```
tsc --traceresolution 
```

Enter fullscreen mode Exit fullscreen mode