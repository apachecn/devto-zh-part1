# 如何在 Java 8 中编写多行 Lambda

> 原文：<https://dev.to/monknomo/how-to-write-a-multiline-lambda-in-java-8-2pl9>

在大多数情况下，单行 lambda 函数就是你所需要的。

这是一条单线λ:

```
Predicate<Sound> isBark = sound -> Sound.valueOf("bark").equals(sound); 
```

Enter fullscreen mode Exit fullscreen mode

有时一行文字不足以表达 lambda 的复杂性。如何制作多线 lambda？

这是怎么回事:

```
//set up - elsewhere in the pseudo code Animal interface: 
public Sound getSound() throws MuteAnimalException {...} 
Predicate<Sound> isBark = sound -> Sound.valueOf("bark").equals(sound); 
//payoff, a multiline lambda 
Predicate<Animal> isDog = animal -> { 
    try { 
        return isBark.test(animal.getSound()); 
    } catch (MuteAnimalException e){ 
        logger.severe(e.getMessage); return false; 
    } 
}; 
```

Enter fullscreen mode Exit fullscreen mode