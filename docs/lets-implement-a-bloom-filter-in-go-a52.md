# 让我们在 Go 中实现一个布隆过滤器

> 原文：<https://dev.to/theodesp/lets-implement-a-bloom-filter-in-go-a52>

[![img](img/e3ca0b3caa36c54f29960e693b4874b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7WLUjIx9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w3g2shxwg47c7mbi8rjh.jpeg)

[**阅读原文**](https://codeburst.io/lets-implement-a-bloom-filter-in-go-b2da8a4b849f)

今天我决定玩一些数据结构，我想知道我下一步应该学什么。我记得不久前，当我阅读一些关于 [Cassandra internals](https://www.google.ie/search?ei=X_U0WsGpIuzfgAaBsqPoCQ&q=cassandra+bloom+filter&oq=Cassandra+blool&gs_l=psy-ab.1.0.0i13k1l10.5647.6751.0.8083.8.7.1.0.0.0.80.468.7.7.0....0...1c.1.64.psy-ab..0.8.478...0j0i67k1j0i20i263k1.0.w0BJKKaKl0A) 的文章时，它使用 bloom filters 来检查特定的表是否可能有请求分区数据。我想实现一个并与你分享我的思考过程可能会很酷。我会尽可能简单地解释事情。

## 进入花期

那么什么是布鲁姆过滤器呢？详细的解释超出了本文的范围，但这里有一个来自维基百科的摘要:

> Bloom Filter:一种节省空间的概率数据结构，用于测试一个元素是否是一个集合的成员。假阳性匹配是可能的，但假阴性是不可能的；即查询返回“可能在集合中”或“肯定不在集合中”。元素可以添加到集合中，但不能删除。布鲁姆过滤器在许多情况下被使用，如缓存过滤，以防止“一击即中的奇迹”被存储在其磁盘缓存或博客中，以避免像媒体一样推荐用户以前读过的文章。

所以让我们试着实现一个，看看我们能学到什么。

## 输入代码

在我们开始之前，我们需要一些东西。由于 bloom filters 只是在扩展，我们无法可靠地从其中删除项目，所以让我们定义我们需要提供的最简单的抽象接口。

```
type Interface interface { 
  Add(item []byte)     // Adds the item into the Set 
  Test(item []byte) bool  // Check if items is maybe in the Set
} 
```

基于此，让我们定义并添加初始的构造函数，因为我们需要在使用它之前创建一个过滤器:

```
// BloomFilter probabilistic data structure definition
type BloomFilter struct { 
  bitset []bool      // The bloom-filter bitset 
  k      uint         // Number of hash values 
  n      uint         // Number of elements in the filter 
  m      uint         // Size of the bloom filter bitset 
  hashFuncs[]hash.Hash64           // The hash functions
}

// Returns a new BloomFilter object,
func New(size) *BloomFilter { 
  return &BloomFilter{  
    bitset: make([]bool, size),  
    k: 3,  // we have 3 hash functions for now
    m: size,  
    n: uint(0),  
    hashfns:[]hash.Hash64{murmur3.New64(),fnv.New64(),fnv.New64a()}, 
  }
} 
```

我在这里使用了 3 个不同的散列函数，为了简单起见，每个项目会产生 3 个不同的值。我使用了一个布尔切片来存储标志。

现在让我们尝试实现接口定义。

**向集合中添加条目**:当添加一个条目时，我们简单地将它通过散列函数，并将结果存储在一个数组中。这些数字是我们需要在布尔数组中设置的位置，以将它们标记为已占用。由于散列位置可能是很大的数字，我们用比特集的大小来修改位置，以防止索引错误。

```
// Adds the item into the bloom filter set by hashing in over the . // hash functions
func (bf *BloomFilter) Add(item []byte) { 
  hashes := bf.hashValues(item) 
  i := uint(0)

  for {  
    if i >= bf.k {   
      break  
    }

    position := uint(hashes[i]) % bf.m  
    bf.bitset[uint(position)] = true

    i+= 1 
  }

  bf.n += 1
}

// Calculates all the hash values by applying in the item over the // hash functions
func (bf *BloomFilter) hashValues(item []byte) []uint64  { 
  var result []uint64

  for _, hashFunc := range bf.hashfns {  
    hashFunc.Write(item)  
    result = append(result, hashFunc.Sum64())  
    hashFunc.Reset() 
  }

  return result
} 
```

如您所见，我们只生成值并将它们的值作为位置设置到位集中。

**检查项目是否可能在集合中**:为了检查项目是否可能在集合中，我们做了与 Add 方法相同的事情，但是现在我们检查散列位置是否在位集合中被标记为真。如果所有的位置都已设定，那么我们说该项目可能存在。如果其中一个位置没有被设定，那么我们说它肯定不存在。

```
// Test if the item into the bloom filter is set by hashing in over // the hash functions
func (bf *BloomFilter) Test(item []byte) (exists bool) { 
  hashes := bf.hashValues(item) 
  i := uint(0) 
  exists = true  

  for {  
    if i >= bf.k {   
      break  
    }   

    position := uint(hashes[i]) % bf.m
    if !bf.bitset[uint(position)] {   
      exists = false   
      break  
    }

    i+= 1 
  }

  return
} 
```

## 让我们写一些测试

我们检查一些值是否存在于集合中。

```
// Test items if items may exist into the set
func (s *MySuite) TestIfMayExist(c *C)  { 
  bf := New(1024)  
  bf.Add([]byte("hello")) 
  bf.Add([]byte("world")) 
  bf.Add([]byte("sir")) 
  bf.Add([]byte("madam")) 
  bf.Add([]byte("io"))

  c.Assert(bf.Test([]byte("hello")), Equals, true)
  c.Assert(bf.Test([]byte("world")), Equals, true)
  c.Assert(bf.Test([]byte("hi")), Equals, false)
} 
```

当物品在布景上时，存在性测试可能会给你假阳性，但实际上不是。这取决于许多因素，如比特集的大小、集合中元素的大小以及散列函数的质量。

## 结论

如你所见，实现布隆过滤器并不是一件困难的事情。你只需要把问题分成小块，一个一个地去做。在这种情况下，我们从数据结构的抽象表示开始，并使用简单的约定实现方法。我希望你喜欢这种努力，并且学到了一些东西。

## 习题

布隆过滤器具有用于计算最佳元素数量的一些数学属性，例如错误率在特定百分比内。例如来自[https://brilliant . org/wiki/bloom-filter/# false-positives-analysis](https://brilliant.org/wiki/bloom-filter/#false-positives-analysis)的 k 的最佳值是:

```
k = ln(2) * m / n 
```

修改 BloomFilter 的原始构造函数，例如它接受一个参数`e`，该参数将尝试计算最优的`k`，例如不变量 holds。

## 参考文献

您可以在以下网站中找到有关布隆过滤器的更多信息:

[**Wiki 文章**](https://en.wikipedia.org/wiki/Bloom_filter):Bloom filter
[**原文**](http://dmod.eu/deca/ft_gateway.cfm.pdf) :原文描述 Bloom filter
[**【Briliant.org 文章**](https://brilliant.org/wiki/bloom-filter/#_=_) :来自 Briliant.org 的精彩解说

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。

**如果您想安排一次指导会议，请访问我的[共同导师简介](https://www.codementor.io/theofanisdespoudis)** 。