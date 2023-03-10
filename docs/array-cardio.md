# 阵列有氧运动

> 原文：<https://dev.to/19h47/array-cardio>

> *Array cardio* 是[韦斯博斯](https://dev.to/wesbos)的一个说法，我从他身上学到了很多。âœŒï

在我看来， **JavaScript** 中的数组是语言的关键。所以我一遍又一遍地练习，以便真正理解它，并在获取数组时避免难看的循环。

所以我们走吧！

首先，我创建了一个 JSON 文件，其中包含虚拟数据。我们保持简单，但我们已经有很多可以玩的了。

```
[  {  "id":  1,  "title":  "Lorem",  "categories":  [  "Adipisicing",  "Elit"  ]  },  {  "id":  2,  "title":  "Ipsum",  "categories":  [  "Elit"  ]  },  {  "id":  3,  "title":  "Dolor",  "categories":  [  "Maxime",  "Animi"  ]  },  {  "id":  4,  "title":  "Sit",  "categories":  [  "Minima"  ]  },  {  "id":  5,  "title":  "Amet",  "categories":  [  "Culpa"  ]  },  {  "id":  7,  "title":  "Architecto",  "categories":  [  "Culpa",  "Maxime"  ]  },  {  "id":  8,  "title":  "Aliquam"  },  {  "id":  9,  "title":  "Libero",  "categories":  [  "Maxime"  ,  "Elit",  "Neque"  ]  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我需要我的`index.js`中的文件。

```
let datas = require('./datas.json') 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们来玩吧！

## `array.push`

首先，让我们用 [`array.push`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push?v=a)
向数组中添加一个新对象

```
datas.push(
    { 
        id: 6, 
        title: "Consectetur", 
        categories: [ "Dignissimos" ] 
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

## `array.filter`

现在，我们将根据给定的 id 整数过滤`datas`,并将结果存储在一个新数组中。这样，原始数组不会改变。

[`array.filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 非常适合这份工作。

```
const datasFilteredById = datas.filter((obj) => {
    return obj.id === 5
}) 
```

Enter fullscreen mode Exit fullscreen mode

`datasFilteredById`的一个`console.log`给我们

```
[ { id: 5, title: 'Amet', categories: [ 'Culpa' ] } ] 
```

Enter fullscreen mode Exit fullscreen mode

## `array.find`

我们将对类别做同样的事情:通过给定的类别字符串过滤`datas`。

但是，`id`属性只有整数，`categories`是列表。而且除此之外`categories`的财产也许根本不存在。

我们开始吧。如果`categories`属性是`undefined`，我们*返回* <sup>*****</sup> ，如果不是，我们使用 [`array.find`](https://developer.mozilla.org/en-US/search?q=array+find) 返回在`categories`属性中找到的匹配类别的对象。

```
const datasFilteredByCategory = datas.filter((obj) => {

    if (obj.categories === undefined) return

    return obj.categories.find((category) => {

        return category === 'Maxime';
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

`datasFilteredByCategory`的一个`console.log`给我们

```
[ 
    { id: 3, title: 'Dolor', categories: [ 'Maxime', 'Animi' ] },
    { id: 7, title: 'Architecto', categories: [ 'Culpa', 'Maxime' ] },
    { id: 9, title: 'Libero', categories: [ 'Maxime', 'Elit', 'Neque' ] } 
] 
```

Enter fullscreen mode Exit fullscreen mode

## `array.map`

[`array.map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 通过对给定数组的每一项调用一个函数来创建一个新数组。

这里我们只是检索每个对象的`title`属性，并将它们存储到`datasTitles`中。

```
const datasTitles = datas.map((data, index, array) => { return data.title }) 
```

Enter fullscreen mode Exit fullscreen mode

`datasTitles`的一个`console.log`给我们

```
[ 
    'Lorem',
    'Ipsum', 
    'Dolor', 
    'Sit', 
    'Amet', 
    'Architecto', 
    'Aliquam', 
    'Libero', 
    'Consectetur' 
] 
```

Enter fullscreen mode Exit fullscreen mode

## `array.sort`

让我们再做一次同样的事情，但是 id 按递增的数字排序。这是 [`array.sort`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Array/sort) 的工作。`array.sort`比较数组中的每一项。

```
const datasIds = datas
    .map((data, index, array) => { return data.id })

    .sort((a, b) => { return a - b }) 
```

Enter fullscreen mode Exit fullscreen mode

`datasIds`中的一个`console.log`给我们:

```
[ 1, 2, 3, 4, 5, 6, 7, 8, 9 ] 
```

Enter fullscreen mode Exit fullscreen mode

目前一切都很好。

## `array.reduce`和传播语法

最后，也是我认为最棘手的。我们将创建数据使用的每个类别的列表。

*   首先，我们必须用`array.filter`检查`categories`属性是否存在。

*   然后我们用所有现有的`categories`属性和`array.map`创建一个数组。

*   我们连接这个新的数组，因为它是一个数组的数组，用来自 ES6 的 [`array.reduce`](https://developer.mozilla.org/en-US/search?q=array+reduce) 和[展开语法。 <small>*(说实话，我不确定我完全理解这个特性。)*</small>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)

*   然后我们用`array.filter`删除重复的类别。

```
const datasCategories = datas

    .filter((obj) => { 

        return obj.categories !== undefined ? obj : '' 
    })

    .map((data, index, array) => { return data.categories })

    .reduce((a, b) => { return [...a, ...b] })

    .filter((category, index, array) => { 

        return array.indexOf(category) === index 
    }) 
```

Enter fullscreen mode Exit fullscreen mode

当然，有一个更简单的方法来做到这一点，但它的工作。`datasCategories`中的一个`console.log`来证实:

```
[
    'Adipisicing',
    'Elit',
    'Maxime',
    'Animi',
    'Minima',
    'Culpa',
    'Neque',
    'Dignissimos'
] 
```

Enter fullscreen mode Exit fullscreen mode

也许我做得不对，我希望得到反馈、纠正或最佳实践。

你对数组的日常工作是什么？ðŸ ƒðŸ "â™€ï

* * *

<sup>*****</sup> <small>不知道怎么说:*我们回归*？*我们逃*？*我们破*？</small>