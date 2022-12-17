# 解释调用和设置 Getters 和 Setters，就像我五岁一样

> 原文：<https://dev.to/alexgwartney/explain-calling-and-setting-getters-and-setters-like-im-five-25p5>

有人能给我详细解释一下为什么我们可以用 get 和 set 方法做这两件事吗？

所以我知道如何定义 get 和 set 方法。我感到困惑的是，为什么我们不像普通方法那样调用它们？例如，正如您在 mdn 文档给出的以下示例中看到的。它们通过将 set 方法定义为带有值的对象属性来调用它。

`javascript language.current = 'EN';`

然后当我们调用 get 方法的时候，我们再次调用这个方法，就好像它是对象的一个属性，而不是调用对象的一个方法？希望这是有意义的，只是我不太理解正在发生的事情。

```
var obj = {
  log: ['a', 'b', 'c'],
  get latest() {
    if (this.log.length == 0) {
      return undefined;
    }
    return this.log[this.log.length - 1];
  }
}

var language = {
  set current(name) {
    this.log.push(name);
  },
  log: []
}

language.current = 'EN';

console.log(language.log); 
```

Enter fullscreen mode Exit fullscreen mode