# 生成器设计模式

> 原文：<https://dev.to/nishparadox/builder-design-pattern>

## 要点

*   当构造函数主要由参数填充时，使用生成器设计模式。
*   如果构造函数接受如此多的参数，那么创建一个类的新实例就变得很乏味。

#### 举例

**建造者超过人口**

```
public class Burger {
    private int size;

    private boolean cheese = false;
    private boolean tomato = false;
    private boolean lettuce = false;
    private boolean pepperoni = false;

    public Burger(int size, boolean cheese, boolean tomato, boolean lettuce, boolean pepperoni) {
        this.size = size;
        this.cheese = cheese;
        this.tomato = tomato;
        this.lettuce = lettuce;
        this.pepperoni = pepperoni;
    }
}

Burger burger = new Burger(14, true, true, false, false); 
```

可以看出，随着要添加到汉堡中的条目数量的增加，我们也必须增加构造函数中的参数数量。这是一个非常糟糕的设计实践，因为很难记住参数的顺序以及参数中的参数类型。

**构建器模式**

```
public class Burger {
    private int size;

    private boolean cheese = false;
    private boolean pepperoni = false;
    private boolean lettuce = false;
    private boolean tomato = false;

    public Burger(BurgerBuilder builder) {
        this.size = builder.size;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
        this.lettuce = builder.lettuce;
        this.tomato = builder.tomato;
    }
}

public class BurgerBuilder {
    public int size;

    public boolean cheese = false;
    public boolean pepperoni = false;
    public boolean lettuce = false;
    public boolean tomato = false;

    public BurgerBuilder(int size) {
        this.size = size;
    }

    public BurgerBuilder addPepperoni() {
        this.pepperoni = true;
        return this;
    }

    public BurgerBuilder addLettuce() {
        this.lettuce = true;
        return this;
    }

    public BurgerBuilder addCheese() {
        this.cheese = true;
        return this;
    }

    public BurgerBuilder addTomato() {
        this.tomato = true;
        return this;
    }

    public Burger build() {
        return new Burger(this);
    }
}

Burger burger = (new BurgerBuilder(14))
                    .addPepperoni()
                    .addLettuce()
                    .addTomato()
                    .build(); 
```

在`BurgerBuilder`类中，所有的`add`方法都返回构建器类型，这样我们就可以形成后续添加的链。

最后，`build`方法返回我们想要的汉堡。

> 请确保根据上下文实现这种设计模式，否则这将是一种矫枉过正。:D