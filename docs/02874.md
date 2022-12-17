# 合并具有部分类型的对象

> 原文：<https://dev.to/lacolaco/merging-objects-with-partial-type-5ce>

我通常在 TypeScript 中实现不可变合并的模式。

```
class MyClass {
    constructor(public id: string, public name: string) {}

    clone() {
        return new MyClass(this.id, this.name);
    }

    merge(another: Partial<MyClass>) {
        return Object.assign(this.clone(), another);
    }
}

const objA = new MyClass("1", "foo");

const objB = objA.merge({ name: "bar" });

console.log(objA !== objB);
console.log(objB.id === "1");
console.log(objB.name === "bar"); 
```

Enter fullscreen mode Exit fullscreen mode

`Partial<MyClass>`允许我们传递部分匹配`MyClass`的对象。