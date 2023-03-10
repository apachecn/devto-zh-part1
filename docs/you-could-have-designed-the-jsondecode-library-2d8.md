# 你可以设计 Json。解码库！

> 原文：<https://dev.to/matthewsj/you-could-have-designed-the-jsondecode-library-2d8>

许多第一次学习 elm 的人发现 elm 的`Json.Decode`库是一个很大的绊脚石。这是从 elm 写入 JSON 值的唯一方法，所以如果您想让您的 elm 程序与外界交流，您可能必须使用它。

但是为什么一定要这样，嗯，*怪异*？

您已经掌握了如何定义记录和标记联合的诀窍，并且您必须学习如何使用`case`和模式匹配来读取复杂的数据结构——`Json.Decode`如何让您使用一组特殊的函数(如`field`和`oneOf`)来构建解码器？为什么我们不能有一个表示 JSON 值的普通 elm 数据类型，并编写普通的 elm 函数来处理它呢？

让我们找出答案。

在这篇文章中，我将试着带领你设计 elm 的`Json.Decode`库。我将假设您对编写 elm 很熟悉，并且您知道 JSON 是什么，但是您对解码是如何工作的一无所知。最后，我希望你能理解`Json.Decode`是如何工作的，并对它为什么被设计成这样有一个直觉。

## JSON 的正常方式

让我们假设代替`Json.Decode.Value`，我们有一个完全正常的类型被定义为:

```
type JsonValue
    = String String
    | Int Int
    | Float Float
    | Bool Bool
    | Null
    | Obj (Dict String JsonValue)
    | Arr (List JsonValue) 
```

Enter fullscreen mode Exit fullscreen mode

JSON 值是一个字符串、一个数字(`Int`或`Float`)、一个布尔值、一个值`null`、一个由与其他 JSON 值相关联的字符串组成的对象或者一个 JSON 值数组。这就是你可能期望 JSON 在 elm 中公开的方式，而且无论如何，这完全是普通的 elm 代码，没有任何花哨的东西。很简单。

假设我们正在编写一个游戏客户端，服务器以 JSON 消息的形式定期向我们发送一组更新，如下所示:

```
{  "operations":  [  {  "action":  "createEnemy",  "name":  "Zombie",  "hitPoints":  4  },  {  "action":  "movePlayer",  "location":  "forest"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在 elm 中使用这些。我们可以直接使用`JsonValue`表示，但这似乎不是个好主意，因为这意味着我们必须在整个程序中处理 JSON 格式的细节。相反，我们应该像这样定义一个更合理的 elm 类型:

```
type Operation
    = CreateEnemy { name : String, hitPoints : Int }
    | MovePlayer { location : String } 
```

Enter fullscreen mode Exit fullscreen mode

并编写一个函数来翻译一个`JsonValue`并产生一个列表到一个列表的`Operation` s:

```
readOperations : JsonValue -> List Operation 
```

Enter fullscreen mode Exit fullscreen mode

哦，我们还需要考虑如果我们得到一个不合法的 JSON 值会发生什么。我们可以返回一个`Maybe (List Operation)`，但是为了调试，我们可能想知道为什么我们得到的 JSON 不是一个操作，所以让我们把它变成一个`Result String (List Operation)`。所以我们要写:

```
readOperations : JsonValue -> Result String (List Operation) 
```

Enter fullscreen mode Exit fullscreen mode

既然我们在讨论`JsonValue` s，这只是一个普通的 elm 函数，我们可以只用`case` s 和普通的 elm 东西来写出来。下面是第一次尝试。(我准备展示整个程序，但是比较大，直接说重要的部分，不要觉得需要研究。)

```
readOperations : JsonValue -> Result String (List Operation)
readOperations jsonValue =
    case jsonValue of
        Obj obj ->
            case Dict.get "operations" obj of
                Just opsList ->
                    readOperationsList opsList

                Nothing ->
                    Err ("Expected an object with field \"operations\", got: " ++ toString jsonValue)

        _ ->
            Err ("Expected an object, got " ++ toString jsonValue)

readOperationsList : JsonValue -> Result String (List Operation)
readOperationsList jsonValue =
    case jsonValue of
        Arr arr ->
            collapseResults (List.map readOperation arr)

        _ ->
            Err ("Expected an array, got " ++ toString jsonValue)

collapseResults : List (Result String a) -> Result String (List a)
collapseResults results =
    let
        collapseResultsAcc accumulatedList results =
            case results of
                [] ->
                    Ok (List.reverse accumulatedList)

                first :: rest ->
                    case first of
                        Ok value ->
                            collapseResultsAcc (value :: accumulatedList) rest

                        Err str ->
                            Err str
    in
        collapseResultsAcc [] results

readOperation : JsonValue -> Result String Operation
readOperation jsonValue =
    case jsonValue of
        Obj fields ->
            case Dict.get "action" fields of
                Just (String "createEnemy") ->
                    readCreateEnemy fields

                Just (String "movePlayer") ->
                    readMovePlayer fields

                Just (String s) ->
                    Err ("Got invalid action: " ++ s)

                Just v ->
                    Err ("Expected a string, got: " ++ toString v)

                Nothing ->
                    Err ("Expected an object with field \"action\", got: " ++ toString jsonValue)

        _ ->
            Err ("Expected an object, got: " ++ toString jsonValue)

readCreateEnemy : Dict String JsonValue -> Result String Operation
readCreateEnemy fields =
    case Dict.get "name" fields of
        Just (String name) ->
            case Dict.get "hitPoints" fields of
                Just (Int hitPoints) ->
                    Ok (CreateEnemy { name = name, hitPoints = hitPoints })

                Just v ->
                    Err ("Expected an integer, got: " ++ toString v)

                Nothing ->
                    Err "Expected an object with field \"hitPoints\""

        Just v ->
            Err ("Expected a string, got: " ++ toString v)

        Nothing ->
            Err "Expected an object with field \"name\""

readMovePlayer : Dict String JsonValue -> Result String Operation
readMovePlayer fields =
    case Dict.get "location" fields of
        Just (String location) ->
            Ok (MovePlayer { location = location })

        Just v ->
            Err ("Expected a string, got: " ++ toString v)

        Nothing ->
            Err "Expected an object with field \"location\"" 
```

Enter fullscreen mode Exit fullscreen mode

圣牛，这个功能*巨大！*不知何故，我需要将近 100 行代码来阅读一段非常简单的 JSON。如果格式更复杂，代码会越来越大。

## 这是怎么回事？

如果你仔细看看这个程序，你就会明白为什么。当我们读 JSON 时，每一步都有可能出错，elm 的类型系统(正确地)迫使我们处理每一个可能的错误。这导致了大量的样板文件。

例如，在`readCreateEnemy`中，第一种情况的第一种情况处理好的路径，而函数的整个其余部分——另外四种情况——机械地处理读取可能出错的所有方式。这不仅让代码写起来很乏味，也让*读*很乏味。

问题是我们编写的简单程序结合了两种不同关注点的细节:

*   假设 JSON 具有我们期望的形状，我们在“好的情况”下应该做什么来把我们的`JsonValue`变成`Operation`。这种担心是特定于我们的特定程序和我们想要将我们期望的 JSON 格式转换成我们特定于程序的`Operation`类型的方式的。
*   如何检测输入 JSON 是否没有预期的形状，如果有，如何构造适当的错误。这种担心并不是我们项目中特定的。似乎我们编写的几乎所有涉及解析 JSON 的程序都会有某种预期的形状，如果输入不符合它，就会发出错误信号。

当然，第二个顾虑是合理的——我们需要处理这样一个事实，即我们的输入 JSON 可能没有我们期望的形状。但是这种担心支配了如此多的代码，如此普通和样板化，以至于很难理解我们的解码器除了发现格式错误之外还做了什么。因此，让我们看看能否找到一种方法，将这两个问题的代码分离出来，这样我们就可以将错误处理代码放入它自己的库中。

## 取出所有的错误处理代码

注意每个函数是如何以一个`case`语句开始的，该语句检查 JSON 对象是否是合适的标记，否则返回一个错误消息。让我们写一些帮助器函数来完成这个任务。让我们从具有直接的 elm 等价物的原始值开始:

```
{-| Reads a JSON string to a String. Returns an error if the input isn't a string.
-}
string : JsonValue -> Result String String
string jsonValue =
    case jsonValue of
        String s ->
            Ok s

        _ ->
            Err ("Expected a string, got " ++ toString jsonValue)

{-| Reads a JSON int to an Int. Returns an error if the input isn't an integer.
-}
int : JsonValue -> Result String Int
int jsonValue =
    case jsonValue of
        Int i ->
            Ok i

        _ ->
            Err ("Expected an integer, got " ++ toString jsonValue)

-- ... and similar for float and bool 
```

Enter fullscreen mode Exit fullscreen mode

在我们继续之前，有一个观察:我们写了很多类型`JsonValue -> Result String [something]`来表示将 JSON 值读取到某个 elm 类型的东西。当我在类似这样的类型中看到一个常见的模式时，我喜欢给它起一个名字来帮助我思考:

```
{-| Simple type alias for functions that parse a JsonValue into a value of
some arbitrary type t. Since the parse may fail, the function returns a
Result that could indicate a parse error.
-}
type alias JsonDecoder t = JsonValue -> Result String t 
```

Enter fullscreen mode Exit fullscreen mode

从现在起我将使用这种类型。但是当你看到它的时候，记住它只是一个读取一个`JsonValue`并产生一个结果的函数。我们仍然生活在普通的老 elm 函数和数据结构的世界里。

## 数组

现在我们已经摆脱了所有平面 JSON 值的错误处理样板，但是如果不处理数组和对象，我们就无法处理许多有趣的 JSON 值。到目前为止，我们一直在编写的函数只需要做一件事:查看 JSON 结构的一个级别并返回等价的 elm 值或一个错误。但是数组和对象是更多 JSON 值的容器，调用者可能对这些值有自己的看法:我们不仅仅想要一个列表，我们想要一个字符串列表在一个地方，可能一个整数列表在另一个地方。

幸运的是，elm 可以很容易地将函数组合在一起以构建更大的函数，所以让我们的数组解码器使用另一个解码器来解码每个元素。我们可以使用我们之前写的`readOperationsList`的实现，并修改它以获得一个解码器参数:

```
{-| Decodes a JSON array, with each element decoded by the given decoder.
-}
list : JsonDecoder a -> JsonDecoder (List a)
list elementDecoder jsonValue =
    case jsonValue of
        Arr jsonValues ->
            collapseResults (List.map elementDecoder jsonValues)
        _ ->
            Err ("Expected an array, got " ++ toString jsonValue)

{-| Groups a list of Results together into a result that either produces all the
successes in a list if all are Ok, or the first error if there are any.
-}
collapseResults : List (Result String t) -> Result String (List t)
collapseResults = ... -- see above for implementation 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

## 物体

对象本身也有一些问题。

*   首先，JSON 对象有字段，当我们处理一个对象时，我们几乎总是希望得到一个特定的命名字段并处理它。
*   另一方面，当我们读到一个物体

```
{  "action":  "createEnemy",  "name":  "Zombie",  "hitPoints":  4  } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要从中读取多个字段，并将它们提供给其他函数以获得结果——在这种情况下，我们需要读取`name`和`hitPoints`字段，并将它们提供给`CreateEnemy`。

*   最后，我们可能需要通过阅读一个对象的其他部分来弄清楚从该对象期望得到什么。对于我们的操作，我们需要读取`"action"`字段来知道我们是在做`CreateEnemy`还是`MovePlayer`动作，以及期望在对象上看到什么其他字段。

让我们一次解决一个问题。

### 阅读字段

既然我们已经处理了数组，这看起来很简单。我们可以这样做:编写一个函数，它将一个`JsonDecoder`和一个字段名作为参数，并返回一个新的`JsonDecoder`，该函数期望看到一个包含给定字段的对象，并用给定的解码器读取它。下面是它的样子:

```
{-| Decodes the named field of the given JSON object using the given decoder.
Returns an error if the given value isn't a JSON object.
-}
field : String -> JsonDecoder t -> JsonDecoder t
field name decoder jsonValue =
    case jsonValue of
        Obj dict ->
            case Dict.get name dict of
                Just fieldValue ->
                    decoder fieldValue

                Nothing ->
                    Err ("Expected an object with field \"" ++ name ++ "\", got: " ++ toString jsonValue)

        _ ->
            Err ("Expected an object, got: " ++ toString jsonValue) 
```

Enter fullscreen mode Exit fullscreen mode

### 组合多个字段

在我们的第一版`decodeOperations`中，函数`readCreateEnemy`负责读取 JSON 对象的`name`和`hitPoints`字段，并使用这些内容构建一个`CreateEnemy`操作。由于我们需要做大量的错误处理，它很容易成为该实现中最糟糕的函数，所以如果我们试图清理错误处理代码，我们必须在这里做一些事情。但是现在还看不出*是什么*。

让我们想想我们需要做什么。首先，`readCreateEnemy`的工作是构建一个`CreateEnemy`，一个与 JSON 无关的值；让调用者提供一个函数来进行组合，而我们现在编写的样板文件在正确的情况下处理调用，这似乎是一个好主意。由于我们的工作是处理错误，该函数应该只获取解码子字段的成功结果，所以我们还需要调用者告诉我们他们想要什么子字段以及如何解码它们的内容。

让我们把它写出来(为了便于讨论，我们假装总是想读取并组合两个字段):

```
potentialMultifieldDecoder :
    (a -> b -> t)
    -> String
    -> JsonDecoder a
    -> String
    -> JsonDecoder b
    -> JsonDecoder t 
```

Enter fullscreen mode Exit fullscreen mode

这似乎是一个我们可以实现的函数，但是令人沮丧的是，我们*刚刚*写了一个解码字段的函数，我们将不得不在这个新函数的主体中再次编写它。这是一种很好的情况，我们可以做更少的工作，同时使我们的库更强大——与其强迫调用者从对象中读取字段，不如让他们解码他们想要的任何东西！他们可以使用我们刚刚编写的`field`函数很容易地从一个对象中解码多个字段，或者他们可以解码和组合他们想要的任何其他内容。

该函数变成:

```
{-| Returns a decoder that returns the result of applying the given
function to the successful result of decoding using both of the given
decoders.
-}
object2 : (a -> b -> t) -> JsonDecoder a -> JsonDecoder b -> JsonDecoder t
object2 f aDecoder bDecoder jsonValue =
    case ( aDecoder jsonValue, bDecoder jsonValue ) of
        ( Ok a, Ok b ) ->
            Ok (f a b)

        ( Err s, _ ) ->
            Err s

        ( _, Err s ) ->
            Err s 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以定义`object3`、`object4`等等。

看看我们写的东西，你觉得这种类型眼熟吗？如果您阅读了大量的 elm 库代码，您可能已经注意到它符合出现在许多库中的`map`模式:例如，

```
Maybe.map2 : (a -> b -> t) -> Maybe a -> Maybe b -> Maybe t
Result.map2 : (a -> b -> t) -> Result x a -> Result x b -> Result x t
Task.map2 : (a -> b -> t) -> Task x a -> Task x b -> Task x t 
```

Enter fullscreen mode Exit fullscreen mode

在所有这些库中,“map”函数做同样的事情。我们有一些类似于`Maybe`的类型，我们可以认为是一种“类似于`a`的类型”——`Maybe a`是一个可能不存在的`a`,`Result x a`是一个`a`，可能是一个类型为`x`的错误值，而`Task x a`是一个异步任务的配方，如果我们执行它，可能会产生一个`a`。这使得`map2`成为一个“排序函数应用程序”,因为我们已经有了一个排序函数`a`和一个排序函数`b`,如果底层的`a`和`b`值存在，就将该函数应用于它们。因为它们只是一种值，所以结果也只是一种值。

在我们的例子中，`JsonDecoder a`表示一种`a`——一种从 JSON 值中读取的`a`。它非常符合这个模式！在这种情况下，很明显`object2`实际上只是`map2`，为了与其他 elm 库保持一致，我们应该这样称呼它。同样，很明显，我们有很好的理由添加一个`map`(即`map1`)来转换单个参数。我们不仅可以用它来读取对象的字段！

```
map2 : (a -> b -> t) -> JsonDecoder a -> JsonDecoder b -> JsonDecoder t
map2 = object2

{-| Returns a decoder that returns the result of applying the given
function to the successful result of decoding using the given decoder.
-}
map : (a -> t) -> JsonDecoder a -> JsonDecoder t
map f decoder jsonValue =
    decoder jsonValue
        |> Result.map f 
```

Enter fullscreen mode Exit fullscreen mode

### 边解码边做决定

至此，我们已经构建了一个库，它很好地为我们在原始程序中所做的几乎所有事情排除了错误处理。我们剩下的一个问题是我们最初的`readOperation`函数，它查看`action`字段并决定是调用`readCreateEnemy`还是`readMovePlayer`。如果我们认为`readOperation`是由“真实函数”和“错误样板”组成的，那么真实函数是将`action`字段作为字符串读取，然后对其执行案例调度以调用`readCreateEnemy`或`readMovePlayer`的部分，而错误样板是处理场景的函数末尾的四个案例系列:

1.  当我们正在读取的 JSON 对象指定了一个不是`"createEnemy"`或`"movePlayer"`的`action`字段时，
2.  当它指定一个不是字符串的`action`字段时，
3.  当它没有`action`字段时，以及
4.  当它根本不是一个物体时。

第一个非常特定于读取`Operation` s，但是其他的完全通用:事实上，`field "action" string`已经处理了所有三个。所以让我们想办法重复利用它。我们需要用户从上面指定“实函数”，所以我们就直接把它当成(实)函数吧。这个函数应该接受一个成功解码的字符串，但是它应该返回什么呢？在代码中，我们试图从它返回的`readCreateEnemy`和`readMovePlayer`中移除样板文件，这两者都可以被认为是`JsonDecoder` s。因此我们的样板文件可以接受一个`JsonDecoder a`和一个函数`a -> JsonDecoder b`，并且应该返回一个`JsonDecoder b`。这是所有类型:

```
(a -> JsonDecoder b) -> JsonDecoder a -> JsonDecoder b 
```

Enter fullscreen mode Exit fullscreen mode

那个类型看起来也很眼熟！它出现在很多名为`andThen`的 elm 包中:例如，就在`core`中，我们有

```
Maybe.andThen : (a -> Maybe b) -> Maybe a -> Maybe b
Result.andThen : (a -> Result x b) -> Result x a -> Result x b
Task.andThen : (a -> Task x b) -> Task x a -> Task x b 
```

Enter fullscreen mode Exit fullscreen mode

等等等等。

在所有这些情况下，`andThen`基本上都做了我们希望它做的事情。记住`map`代表将一个函数`a -> b`应用于一个“排序`a`”；我们可能不会得不到我们的`a`，但是如果我们得到了，我们肯定能把它转换成`b`。`andThen`用于我们有一个排序`a`的情况，一旦我们实际上得到了一个`a`，我们想用它来弄清楚如何进行排序`b`。例如，你可以使用

```
Maybe.map (\x -> x + 1) maybeNumber 
```

Enter fullscreen mode Exit fullscreen mode

如果存在的话，在`maybeNumber`上加 1，和

```
Maybe.andThen (\x -> if x >= 5 then (Just x) else Nothing) maybeNumber 
```

Enter fullscreen mode Exit fullscreen mode

如果小于 5，将`maybeNumber`变为`Nothing`。

这种模式正是我们想要对解码器做的，所以让我们调用我们的函数`andThen`。很容易实现:

```
{-| Returns a decoder that runs the given decoder against its input. Then,
if the decode is successful, it applies the given function and re-parses
the input JSON against the resulting second decoder. This allows a decoder
that reads part of the JSON object before deciding how to parse the rest
of the object.
-}
andThen : (a -> JsonDecoder b) -> JsonDecoder a -> JsonDecoder b
andThen toB aDecoder jsonValue =
    case aDecoder jsonValue of
        Ok a ->
            (toB a) jsonValue

        Err s ->
            Err s 
```

Enter fullscreen mode Exit fullscreen mode

最后一个细节:我们的样板文件处理一般的问题，但是使用`andThen`我们可能会发现我们自己编写的逻辑想要直接返回带有特定值的成功或失败，而不需要运行任何更多的子解码器。在这些情况下，我们不能只返回`Ok`或`Error`值，因为`andThen`希望我们返回一个`JsonDecoder`——记住，这是一个将`JsonValue`传递给`Result`的函数，而不是一个`Result`本身。但是我们可以退而求其次:

```
{-| Returns a decoder that always succeeds with the given value.
-}
succeed : a -> JsonDecoder a
succeed a jsonValue =
    Ok a

{-| Returns a decoder that always fails with the given error message.
-}
fail : String -> JsonDecoder a
fail str jsonValue =
    Err str 
```

Enter fullscreen mode Exit fullscreen mode

## 解析操作，取两个

现在我们已经写好了从`readOperation`中提取所有样板文件所需的一切。让我们看看当我们把所有的样板文件拿出来的时候会是什么样子

```
readOperations : JsonDecoder (List Operation)
readOperations =
    JsonDecoder.list <|
        JsonDecoder.field "action" JsonDecoder.string
            |> JsonDecoder.andThen
                (\action ->
                    case action of
                        "createEnemy" ->
                            JsonDecoder.map2
                                (\name hitPoints ->
                                    CreateEnemy
                                        { name = name
                                        , hitPoints = hitPoints
                                        }
                                )
                                (JsonDecoder.field "name" JsonDecoder.string)
                                (JsonDecoder.field "hitPoints" JsonDecoder.int)

                        "movePlayer" ->
                            JsonDecoder.map
                                (\location -> MovePlayer { location = location })
                                (JsonDecoder.field "location" JsonDecoder.string)

                        _ ->
                            JsonDecoder.fail ("Got invalid action: " ++ action)
                ) 
```

Enter fullscreen mode Exit fullscreen mode

哇！这个版本只有我们第一次尝试的四分之一大小，而且可读性更好。如果你稍微注意一下，它看起来几乎就像是对 JSON 格式的简单描述:它是一个带有名为`action`的字段的对象列表。如果`action`是`"createEnemy"`，那么它应该有`name`和`hitPoints`字段，如果是`"movePlayer"`，那么它应该有一个`location`字段，其他的都是非法的。我更愿意维持这个版本的解析，而不是我们开始时的版本。

我们在这里所做的事情值得反思。我们所做的唯一一件事是试图抽象出处理案例中涉及的样板文件，在这些案例中，我们试图解码一个 JSON 值并发现它不具有我们期望的形状。这里不涉及任何魔法，所有东西都是简短、相对简单的代码，然而我们能够将一堆不可读的东西转换成基本上有意义的东西，在这个过程中，我们得到了一个很好的库，可以用于其他项目。

## 祝贺你！你设计了`Json.Decode`！

坏消息是，`Json.Decode`库没有我们在这里使用的`JsonValue`类型。它是用原生 Javascript 实现的，不允许您访问它所使用的底层数据结构。

好消息是，我们在这里编写的所有其他函数实际上都是直接取自`Json.Decode` API:用`Decoder`替换`JsonDecoder`，一切都一样！我希望我已经说服了你，即使`Json.Decode`*给了你一个`JsonValue`的等价物，你还是会想使用 API 函数。现在去读 API 的其余部分:那里的一切都只是我们在这里已经解决的想法的延伸。我希望现在你已经从基本原则上理解了为什么这个库是这样工作的，你会发现它不那么神秘了，并且更容易使用。*