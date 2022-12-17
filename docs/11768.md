# 简单的抽象类解释

> 原文：<https://dev.to/adamkdean/simple-abstract-classes-explained-6i9>

这里的部落活动受到了一点影响；但是，建立一个车间，并制定计划，以建立蒸汽机在上述车间会这样做给你。今天一些简单的代码，写来简化显示抽象类。我看到的每一个教程，他们都抛出一些愚蠢的代码，这些代码并不真的需要用来展示抽象类的原始功能。这是一个除了它所需要的以外什么都没有的例子。

```
class Program
{
    static void Main()
    {
        // here we create a happy and pass it to print mood to screen
        // happy is of type happy but also of type mood
        Happy happy = new Happy();
        PrintMoodToScreen(happy);

        // again, we create a mood, this one is a sad mood but still
        // a mood nonetheless
        Sad sad = new Sad();
        PrintMoodToScreen(sad);

        Console.ReadKey();
    }

    // here we take an object of the type Mood, which means we don't require
    // one method for sad, one method for happy and one for all others
    // and it means we dont have to box them into objects either
    static void PrintMoodToScreen(Mood mood)
    {
        mood.PrintMood();
    }
} 
```

如你所见，我们将对象传递给一个带有情绪类型的方法，而不是悲伤或快乐。想想你当地的兽医，你可能有德国牧羊犬或狮子狗，但它们都是由狗类衍生而来的。如果这还不够，想想咖啡，美丽的咖啡，我们有咖啡，然后我们有速溶咖啡和研磨咖啡，然后我们有 KenkoInstantCoffee 和 NescafeInstantCoffee，每个都是另一个的孩子，咖啡->速溶咖啡-> KenkoInstantCoffee 等等。你可以写一个方法，只接受速溶咖啡，然后加入水和牛奶，快速做出一杯“正常”的咖啡。

```
abstract class Mood
{
    // protected means it's accessible to Mood, and to anything derived
    // from it, i.e. sad, happy, etc
    protected string CurrentMood { get; set; }

    // here we write a method to print the mood, we only need to code
    // it once, that's one reason why abstract classes exist
    public void PrintMood()
    {
        Console.WriteLine(CurrentMood);
    }
}

// we create the happy class and derive from mood
class Happy : Mood
{
    public Happy()
    {
        // we set the protected property, which is accessible to us
        // but not to anyone using a mood object
        CurrentMood = "Current Mood is Happy :)";
    }
}

class Sad : Mood
{
    public Sad()
    {
        // cheer up Sad class!
        CurrentMood = "Current Mood is Sad :(";
    }
} 
```

如果需要更多的解释，那么我真的不确定你是否还有希望。

祝你好运！