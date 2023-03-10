# 可组合回调

> 原文：<https://dev.to/avaq/composable-callbacks-1l7i>

### 一诺实现在六十字以下

你以前听过:*回调不作曲*。恕我不能苟同。在本文中，我们将构建一个抽象，它具有与承诺相似的组合和流控制功能，但是只使用接受回调的函数——组合的对立面。然后我们将使用我们新发现的抽象来解决[异步问题](https://github.com/plaid/async-problem)。

让我们先思考一下我们是如何定义函数的。常规加法函数可以这样定义:

```
//    add :: (Number, Number) -> Number
const add = (a, b) => a + b 
```

但是我们也可以稍微定义一下，一个函数取一个参数，返回一个函数取另一个参数，然后返回两个参数相加的结果:

```
//    add :: Number -> Number -> Number
const add = a => b => a + b 
```

你们许多人会认为后者是前者的“咖喱”变种。你可以在最合适的指南的第四章[中阅读关于奉承的内容。](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch4.html)

以这种方式定义函数可以打开一些使用函数的新方法。例如，我们可以通过将`add`应用于`5`来轻松定义一个新的`add5`函数，用于映射一个数组，例如:

```
[1, 2, 3, 4, 5] .map (add (5))
//> [6, 7, 8, 9, 10] 
```

我们将以可定制的方式定义我们所有的函数，这是实现回调组合的第一步。

让我们举一个使用回调的异步程序的基本例子:

```
fs.readFile ('input.txt', 'utf8', (e, input) => {
  if (e) console.error (e)
  else fs.readFile (`${input}-file.txt`, 'utf8', (e, result) => {
    if (e) console.error (e)
    else console.log (result)
  })
}) 
```

当我们这样做的时候，它会直接把我们送到回调地狱。让我们看看在创建了一个 curried 版本的`readFile`之后我们能做些什么。我们还将通过去掉错误参数来简化回调。我们将在本文快结束时回到这个问题。

```
//    readFile :: String -> String -> (String -> Undefined) -> Undefined
const readFile = encoding => filename => callback => {
  fs.readFile (filename, encoding, (e, contents) => {
    if (e) console.error (e)
    else callback (contents)
  })
} 
```

*现在你可能想知道这些`::`-注释在每个函数上面做什么。它们是一种叫做 Hindley Milner 的简洁类型语言中的类型定义。“HM”语言在描述特定的 curried 函数时非常简洁。如果你花一点时间来理解它是如何工作的，它将帮助你更清楚地看到我们的函数发生了什么。你可以在最合适的指南第七章[中读到更多。](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html)*

你可能也注意到了，我稍微打乱了论点的顺序。这对于局部应用来说是更优化的。这个对`readFile`的新定义允许我们部分地应用它，并且*还没有通过回调*。

```
//    readText :: String -> (String -> Undefined) -> Undefined
const readText = readFile ('utf8')

//    step1 :: (String -> Undefined) -> Undefined
const step1 = readText ('input.txt')

//    step2 :: String -> (String -> Undefined) -> Undefined
const step2 = input => readText (`${input}-file.txt`)

//    step3 :: String -> Undefined
const step3 = console.log 
```

让我们看看我们在这里创建了什么:

1.  `readText`:对`readFile`的部分应用，带编码。我们可以重复使用它，而不必到处经过 T2。
2.  `step1`:部分应用`readText`。现在剩下的唯一参数是实际的回调。所以`step1`变成了一个接受回调的函数，`input.txt`的内容将被传递给这个回调。
3.  `step2`:一个函数，接受一些输入，并使用它来读取一个文件名包含所述输入的文件。尽管它实际上并不读取任何文件，它只是再次部分地应用`readText`并返回等待回调的函数。
4.  `step3`:只是为了说明起见，`console.log`的别名。它曾经嵌套在步骤 2 的回调函数中。

现在，如果我们研究一下这些函数的特征，我们会发现它们可以很好地相互融合。`step3`可以作为`step2`的回调，`step2`的整体可以作为`step1`的自变量。这样做需要大量的嵌套，但是我们可以定义一个助手函数来“展平”嵌套。姑且称之为`then`；)

```
//    then :: (a -> (b -> Undefined) -> Undefined)
//         -> (     (a -> Undefined) -> Undefined)
//         ->       (b -> Undefined) -> Undefined
const then = transform => run => callback => run (value => transform (value) (callback)) 
```

我们的`then`函数有三个参数:

1.  一个转换函数，它接收一个值并产生一个等待回调的函数。我们的`step2`其实符合这个描述。
2.  一个仍在等待回调的函数。我们的`step1`符合这一点。
3.  回调。我们的`step3`符合这个。

这个函数很酷的一点是，当我们用它的前两个参数部分地应用它时，我们得到了一个类型，这个类型可以再次用作`then`的第二个参数。这将允许我们将多个“步骤”*一个接一个地放在*旁边，而不是将*嵌套在*中。

您可能已经从签名中注意到，`(a -> Undefined) -> Undefined`有三个实例。如果我们给这种特殊的类型一个特殊的名字，并在我们的类型中使用它，事情会变得更加清楚。让我们为回调函数创建一个简单的别名(`Future`)。这种类型的构造函数没有实现:它只是返回输入(因为它是一个别名)。但是这将有助于使我们的代码更加清晰。让我们用更明确命名的类型来重新定义我们的`then`函数。

```
//    Future :: ((a -> Undefined) -> Undefined) -> Future a
const Future = x => x

//    then :: (a -> Future b) -> Future a -> Future b
const then = transform => future => Future (callback => {
  future (value => transform (value) (callback))
}) 
```

这个新的`then`函数与前一个完全相同，但是它突然变得更加清楚它在做什么:它接受一个创建未来的函数，它接受一个未来，最后返回一个新的未来。用这些术语来讲，`step1`是一个字符串的未来，`step2` *返回*一个字符串的未来，取一个字符串后。

配备了我们的`then`函数和类型别名，我们可以重写我们的回调地狱程序。

```
//    Future :: ((a -> Undefined) -> Undefined) -> Future a
const Future = x => x

//    then :: (a -> Future b) -> Future a -> Future b
const then = transform => future => Future (callback => {
  future (value => transform (value) (callback))
})

//    readFile :: String -> String -> Future String
const readFile = encoding => filename => Future (callback => {
  fs.readFile (filename, encoding, (e, contents) => {
    if (e) console.error (e)
    else callback (contents)
  })
})

//    readText :: String -> Future String
const readText = readFile ('utf8')

//    step1 :: Future String
const step1 = readText ('input.txt')

//    step2 :: String -> Future String
const step2 = input => readText (`${input}-file.txt`)

//    program :: Future String
const program = then (step2) (step1)

program (console.log) 
```

*我们的`then`函数实际上在做数学上精确的平面映射。看看如果我们在类型签名中用`Array`代替`Future`会发生什么。平面可映射类型背后的抽象接口被称为“单子”(因为数学家抢先了一步)。*

我们*可以*使用程序作为`then`的参数来组成一个更大的程序，这意味着**我们已经实现了创建可组合回调**的目标。

让我们回到这一点，因为我们已经失去了手动处理错误的能力。我们可以简单地通过让我们的函数接受两个回调而不是一个来把它加回去。

```
//    Future :: (((a -> Undefined) -> Undefined)
//           -> ((b -> Undefined) -> Undefined))
//           -> Future a b
const Future = x => x

//    then :: (b -> Future a c) -> Future a b -> Future a c
const then = transform => future => Future (reject => resolve => {
  future (reject) (value => transform (value) (reject) (resolve))
})

//    readFile :: String -> String -> Future Error String
const readFile = encoding => filename => Future (reject => resolve => {
  fs.readFile (filename, encoding, (e, contents) => {
    if (e) reject (e)
    else resolve (contents)
  })
})

//    readText :: String -> Future Error String
const readText = readFile ('utf8')

//    step1 :: Future Error String
const step1 = readText ('input.txt')

//    step2 :: String -> Future Error String
const step2 = input => readText (`${input}-file.txt`)

//    program :: Future Error String
const program = then (step2) (step1)

program (console.error) (console.log) 
```

上一个例子中的`then`函数为我们提供了与 Promises 函数相似的异步函数组合和流控制优势，这个函数可以用不到 60 个字符编写:

```
const then = f => m => l => r => m (l) (x => f (x) (l) (r)) 
```

它甚至消除了承诺带来的许多问题。但是它确实留下了一些需要改进的地方，比如良好的性能和堆栈安全性。但是对于我们的目的来说，这样做很好:解决异步问题，并证明回调和同步代码一样是可组合的。

*最初版本的[长笛](https://github.com/fluture-js/Fluture)就是这样实现的，除了`then`被称为`chain`。*

#### 解决异步问题

[异步问题](https://github.com/plaid/async-problem)是一个小的挑战，用来识别一个抽象允许用户如何将一个异步算法分解成小的、可管理的子问题。总结这篇帖子，让我们深入一下，用回调来解决。

```
//    pipe :: Array (Any -> Any) -> Any -> Any
const pipe = fs => x => fs.reduce ((y, f) => f (y), x)

//    lmap :: (a -> b) -> Array a -> Array b
const lmap = f => xs => xs.map (f)

//    append :: a -> Array a -> Array a
const append = x => xs => [...xs, x]

//    pure :: b -> Future a b
const pure = x => l => r => r (x)

//    then :: (b -> Future a c) -> Future a b -> Future a c
const then = f => m => l => r => m (l) (x => f (x) (l) (r))

//    fmap :: (b -> c) -> Future a b -> Future a c
const fmap = f => then (x => pure (f (x)))

//    all :: Array (Future a b) -> Future a (Array b)
//        -- Note: This implementation resolves things in sequence for brevity.
const all = ms => ms.reduce
  ((mxs, mx) => then (x => fmap (append (x)) (mxs)) (mx), pure ([]))

const filesystem = require ('fs')
const path = require ('path')

//    readFile :: String -> String -> Future Error String
const readFile = encoding => filename => l => r => {
  filesystem.readFile (filename, encoding, (e, contents) => {
    if (e) l (e)
    else r (contents)
  })
}

//    readText :: String -> Future Error String
const readText = readFile ('utf8')

//    lines :: String -> Array String
const lines = s => s.split ('\n')

//    unlines :: Array String -> String
const unlines = ss => ss.join ('\n')

//concatFiles :: (String -> String) -> Future Error String
const concatFiles = path =>
  pipe ([ path
        , readText
        , fmap (lines)
        , fmap (lmap (path))
        , fmap (lmap (readText))
        , then (all)
        , fmap (unlines) ])
       ('index.txt')

const main = () => {
  concatFiles (x => path.resolve (process.argv[2], x))
              (e => { process.stderr.write (e.message); process.exit (1) })
              (x => { process.stdout.write (x); process.exit (0) })
}

main() 
```