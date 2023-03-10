# FP 公共代码评审(#1)

> 原文：<https://dev.to/peteraba/fp-public-review-1-525m>

想象一下:你是一个软件工程师，在一个很棒但很小的公司的小团队里。有一天，你的领导找到你，说公司正在招聘更多的工程师，但负责技术部分的人不得不在中途匆匆回国。不幸的是，在可预见的将来，她是没空的。

最初有大量的申请，但 HR 和一些初级人员已经筛选出了最明显的薄弱环节。不过，还是有一些申请需要你去整理。所有的申请人使用一些在线工具解决了同一套问题，你的工作是检查其中一个问题的所有剩余解决方案。

由于只有有限的时间邀请申请人进行现场面试，他们想听听你对每个申请人表现的专业意见。发出邀请还会有其他因素，所以他们需要你做的不仅仅是选择最佳解决方案。如果您能描述每个解决方案做得好的地方以及如何改进，那将是最好的，但是请确保从 1 到 10 给每个解决方案打分，10 是完美的解决方案。

所有的解决方案都是 F#的，但是公司找的是好的程序员，不一定是 F#专家。如果他们没有使用特定于 F#的快捷键也没关系，尽管那也没关系。您需要查看他们对以下问题的解决方案:(从筛子中复制粘贴[exercisem . io](http://exercism.io)问题)

# 筛题

使用厄拉多塞的筛子找出从 2 到给定的
数的所有素数。

厄拉多塞筛是一种简单而古老的算法，可以找到所有给定极限的素数。它通过迭代地将每个素数的倍数标记为
合成(即不是素数)，从 2 的倍数开始标记为
。

创建您的范围，从 2 开始，一直到并包括给定的限制。(即[2，极限])

该算法包括反复重复以下步骤:

*   取列表中下一个可用的未标记数字(它是质数)
*   标出那个数的所有倍数(它们不是质数)

重复操作，直到处理完范围内的每个数字。

当算法终止时，列表中所有没有被标记的数字都是质数。

维基百科的文章中有一个解释该算法的有用图形:
[https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)

请注意，这是一个非常具体的算法，测试不会检查
您是否已经实现了该算法，只会检查您是否已经得到了
正确的素数列表。

### 来源

在维基百科上筛厄拉多塞[http://en.wikipedia.org/wiki/Sieve_of_Eratosthenes](http://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)

# 方案

以下是您需要评估、审查并向管理层和人力资源部门汇报的解决方案。使用本文的评论部分与他们交流:

## 解决方案 1

```
let primesUpTo2 limit =
    let rec mark st acc prime limit =
        let acc' = acc + prime
        match acc' > limit with
        | true -> st
        | false -> mark (Set.add acc' st) acc' prime limit

    let rec iterate st list limit =
        match list with
        | [] -> st
        | x::tail ->
            match Set.contains x st with
            | true -> iterate st tail limit
            | false ->
                let st' = mark st x x limit
                iterate st' tail limit

    let list = [2..limit]
    let nonPrimes = iterate Set.empty list limit
    (Set.ofList list) - nonPrimes |> Set.toList 
```

## 方案二

```
let primesUpTo3 n =
    let sieveStep n ((primes, composites) as acc) i =
        if Set.contains i composites then acc
        else (i :: primes, {i..i..n} |> Set.ofSeq |> Set.union composites)

    {2..n}
    |> Seq.fold (sieveStep n) ([], Set.empty)
    |> fst
    |> Seq.rev 
```

## 方案三

```
let primesUpTo1 limit =
    let sq = (limit |> float |> (fun n -> n ** 0.5) |> int )

    let filterMultiples possiblePrimes prime =
        if prime > sq then possiblePrimes
        else possiblePrimes |> List.filter (fun x -> x <= prime || x % prime <> 0)

    let possiblePrimes = [ 2 .. limit ]

    List.fold filterMultiples possiblePrimes possiblePrimes 
```

## 方案四

```
let primesUpTo4  n =
    let isMultiple factor x = x % factor = 0
    let rec seiveNextPrime primes = function
        | prime :: tail -> seiveNextPrime (prime :: primes) (List.filter (isMultiple prime >> not) tail)
        | [] -> List.rev primes
    seiveNextPrime [] [2..n] 
```

## 解决方案 5

```
type Candidate  =
    | Unmarked of int
    | Marked of int

let primesUpTo5 limit =
    let mark prime range =
        range 
        |> List.map (
            function
            | Unmarked i when i % prime = 0 -> Marked i
            | c -> c)

    let rec sieve marked =
        function
        | [] -> marked |> Seq.rev
        | ((Unmarked p) as pp) :: tail -> sieve (pp :: marked) (mark p tail)
        | c :: tail -> sieve (c :: marked) tail

    [2..limit]
    |> List.map Unmarked
    |> sieve []
    |> Seq.map (
        function
        | Unmarked i -> i
        | _ -> 0)
    |> Seq.filter ((<) 1) 
```

如果你喜欢这个讨论，让我知道，可能会有更多的未来。