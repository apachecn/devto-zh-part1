# forEach -筛选器 0-1

> 原文：<https://dev.to/mogery/foreach---filter-0-1-dnf>

假设我们有一系列宠物食品:

```
var petFoods = [
    {
        name: "Cat Food",
        usableOn: ["cat"]
    },
    {
        name: "Dog Food",
        usableOn: ["dog"]
    },
    {
        name: "Pet Food",
        usableOn: ["cat", "dog"]
    }
]; 
```

Enter fullscreen mode Exit fullscreen mode

...假设我们想得到猫能吃的食物。

我们会使用 forEach 循环，对吗？

```
var usableOnCats = [];

petFoods.forEach(function(food) {
    if (food.usableOn.includes("cat")) {
        usableOnCats.push(food);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

好吧，那有点长...

如果 JS 有专门用于这个目的的数组会怎么样呢...

...哦，等等，当然了！

让我们使用一个过滤循环:

```
var usableOnCats = petFoods.filter(function(food) {
    return food.usableOn.includes("cat");
}); 
```

Enter fullscreen mode Exit fullscreen mode

...给你。好多了，不是吗？