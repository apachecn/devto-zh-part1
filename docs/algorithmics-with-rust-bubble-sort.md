# Rust 算法:冒泡排序

> 原文：<https://dev.to/mnivoliez/algorithmics-with-rust-bubble-sort>

*最初发布在我的[博客](https://mathieu-nivoliez.com/posts/2017-04-26-algorithmics-with-rust-bubble-sort/)上。*

大家好，今天我们要讲的是冒泡排序。

## “冒泡排序？”

冒泡排序是一个简单的算法，它将帮助我们理解 Rust 语法，并理解我们在上一篇文章中谈到的算法复杂性。

## “好的，它是做什么的？”

简而言之，这个算法两个两个地比较数组中的所有元素，如果两个元素没有排序，它就交换位置。

它接受一个数组并给出一个数组。

```
fn bubble_sort(vec_to_sort: &Vec<i32>) -> Vec<i32> {
    let mut result = vec_to_sort.clone();
    return result;
} 
```

我们在这里定义函数(fn)“bubble _ sort”。它采用“vec_to_sort ”,这是一个 i32 的向量(一种动态数组)或 32 位整数写，并给出另一个 i32 的向量。

然后我们迭代并两两比较所有元素。

```
fn bubble_sort(vec_to_sort: &Vec<i32>) -> Vec<i32> {
    let mut result = vec_to_sort.clone();
    for i in 0..result.len() {
        for y in 0..result.len() {
            if result[i] < result[y] {
            }
        }
    }
    return result;
} 
```

最后，如果需要，我们交换元素的位置。

```
fn bubble_sort(vec_to_sort: &Vec<i32>) -> Vec<i32> {
    let mut result = vec_to_sort.clone();
    for i in 0..result.len() {
        for y in 0..result.len() {
            if result[i] < result[y] {
                result.swap(i, y);
            }
        }
    }
    return result;
} 
```

你可以在这里测试这个代码[。](https://is.gd/OLZuLj)

数数这些动作，好吗？

1.  我们复制输入数组，不相关
2.  我们迭代数组的 n 个元素:n 个动作。
3.  我们迭代数组的 n 个元素:n 个动作。
4.  我们比较这两个元素。
5.  如果需要，我们交换元素。

在最坏的情况下，我们有 n*n*1+1 个动作。常数是不相关的，所以我们得到的复杂度是 O(n <sup>2</sup> )。

为了更好地理解它的含义，如果我们有一个处理器能够在几秒钟内执行 100 次操作，并且有一个 100 个元素的输入数组，这个算法将需要 1m40 来对这个数组进行排序。

## “好的，算法很好...我的数组是由字符串组成的呢？或者我要不要换个排序？”

好样的。为此，我们必须使函数通用化，并使用[闭包](https://doc.rust-lang.org/book/closures.html)(一种未命名的函数)。

然后我们可以像这样修改代码:

```
fn bubble_sort<T: std::clone::Clone, F>(vec_to_sort: &Vec<T>, compar : F) -> Vec<T> 
where F : Fn(&T,&T) -> bool {
    let mut result = vec_to_sort.clone();
    for i in 0..result.len() {
        for y in 0..result.len() {
            if compar(&result[i], &result[y]) {
                result.swap(i, y);
            }
        }
    }
    return result;
} 
```

这里我们说这个函数是针对 T 和 F 参数化的，其中 F 是一个返回布尔值的函数。

要了解它的工作原理，请看[这里](https://is.gd/sXT7Wd)。

这一个已经完成了，下一个再见。

马修