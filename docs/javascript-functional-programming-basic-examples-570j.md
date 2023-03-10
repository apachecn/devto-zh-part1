# Javascript 函数式编程基本示例

> 原文：<https://dev.to/ricardomol/javascript-functional-programming-basic-examples-570j>

## 功能编程:

是一种编程范式。

函数的输出值只取决于传递给该函数的参数，因此用参数 x 的相同值调用函数 f 两次，每次都会产生相同的结果 f(x)。

这与依赖于局部或全局状态的过程形成对比，当使用相同的参数但不同的程序状态调用时，过程可能在不同的时间产生不同的结果。

消除副作用，即不依赖于函数输入的状态变化，可以使理解和预测程序的行为变得容易得多，这是函数式编程发展的关键动机之一。

## 高阶函数:

对其他函数进行操作的函数，要么将它们作为参数，要么返回它们。

## 例子:

**过滤器():**

```
var animals = [
    { name: 'John', species: 'dog' },
    { name: 'Jane', species: 'cat' },
    { name: 'James', species: 'frog' },
    { name: 'Joe', species: 'horse' },
];

// With a for loop:
var dogs = [];
for (var i=0; i<= animals.length; i++) {
    if( animals[i].species === 'dog') 
        dogs.push(animals[i]);
}

// Functional:
var dogs = animals.filter(function(animal) {
    return animal.species === 'dog';
};

// Another way, decoupling:
var isDog = function(animal) {
    return animal.species ==='dog';
}
var dogs = animals.filter(isDog); 
```

Enter fullscreen mode Exit fullscreen mode

**地图():**

```
var animals = [
    { name: 'John', species: 'dog' },
    { name: 'Jane', species: 'cat' },
    { name: 'James', species: 'frog' },
    { name: 'Joe', species: 'horse' },
];

// With for loop:
var names = [];
for (var i=0; i<= animals.length; i++) {
    if ( animals[i]-species === 'dog') 
        names.push(animals[i].name);
}

// Functional:
var names = animals.map(function(animal) {
    return animal.name + ' is a ' + animal.species;
});

// Which using ES6 arrow functions can be:
var names = animals.map((animal) =>  { animal.name }); 
```

Enter fullscreen mode Exit fullscreen mode

**减少():**

```
var orders = [
    { amount: 250 },
    { amount: 400 },
    { amount: 100 },
    { amount: 350 },
];

// For loop:
var  totalAmount = 0;
for (var i=0; i < orders.length; i++) {
    totalAmount += orders[i].amount;
}

// Functional:
var totalAmount = orders.reduce(function(sum, order) {
    return sum + order.amount;
}, 0);

// Which using ES6 arrow functions can be:
var totalAmount = orders.reduce(sum, order) => sum + order.amount, 0);

console.log(totalAmount); 
```

Enter fullscreen mode Exit fullscreen mode

来源:https://www.youtube.com/watch?v=Wl98eZpkp-c