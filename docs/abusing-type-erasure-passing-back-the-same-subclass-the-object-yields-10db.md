# 滥用类型擦除:传回对象产生的相同子类

> 原文：<https://dev.to/idanarye/abusing-type-erasure-passing-back-the-same-subclass-the-object-yields-10db>

与真正的泛型/模板相比，Java 的类型擦除泛型通常是有限的，因为你不能访问实际的类对象(除非你把它作为运行时参数传递)，所以你不能创建它的新实例，创建它的数组，或者检查它。但是最近我遇到了一个案例，类型擦除实际上是有帮助的。

# 问题

假设我们有这样一个接口:

```
public interface Foo {
    public Object createValue();
    public void handleValue(Object value);
    public void printValue(Object value);
} 
```

Enter fullscreen mode Exit fullscreen mode

由这些类实现:

```
import java.util.LinkedList;
import java.util.List;

public class Bar implements Foo {
    @Override
    public Object createValue() {
        List<String> list = new LinkedList<>();
        list.add("one");
        list.add("two");
        list.add("three");
        return list;
    }

    @Override
    @SuppressWarnings("unchecked")
    public void handleValue(Object value) {
        List<String> list = (List<String>) value;
        for (int i = 0; i < list.size(); ++i) {
            list.set(i, "(" + list.get(i) + ")");
        }
    }

    @Override
    @SuppressWarnings("unchecked")
    public void printValue(Object value) {
        List<String> list = (List<String>) value;
        for (String entry : list) {
            System.out.printf("- %s\n", entry);
        }
    }
}

public class Baz implements Foo {
    @Override
    public Object createValue() {
        return new int[]{ 1, 2, 3 };
    }

    @Override
    public void handleValue(Object value) {
        int[] array = (int[]) value;
        for (int i = 0; i < array.length; ++i) {
            array[i] = array[i] * 10;
        }
    }

    @Override
    public void printValue(Object value) {
        int[] array = (int[]) value;
        for (int entry : array) {
            System.out.printf("- %s\n", entry);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

是一个使用一个值的 API 它可以创建它，用它做一些事情，并打印它。`Bar`用一个可以用括号括起来的字符串列表实现了`Foo`，而`Baz`用一个乘以 10 的整数数组实现了它。这里有一个隐藏的假设，`handleValue`和`printValue`接受同一个`Foo`对象
的`createValue`返回的同类型对象

```
public class App {
    public static void main(String[] args) {
        Foo[] foos = new Foo[]{ new Bar(), new Baz() };
        for (Foo foo : foos) {
            Object value = foo.createValue();
            foo.handleValue(value);
            foo.printValue(value);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，这个假设在实际代码中很容易实现，但是没有任何东西强迫它。

正确的 OOP 实践规定`handleValue`和`printValue`应该是值为
的方法

```
public interface Value {
    public void handle();
    public void print();
}

public interface Foo {
    public Value createValue();
} 
```

Enter fullscreen mode Exit fullscreen mode

遗憾的是，这并不总是可能的。有时候我们不控制从`createValue`返回的类型。有时我们确实控制它们，但是它们属于不同的模块，不应该知道这些特定的操作。

# 仿制药来救场？

这就是仿制药的作用，不是吗？只是让`Foo`使用一个通用参数来代替`Object` :

```
public interface Foo<T> {
    public T createValue();
    public void handleValue(T value);
    public void printValue(T value);
}

import java.util.LinkedList;
import java.util.List;

public class Bar implements Foo<List<String>> {
    @Override
    public List<String> createValue() {
        List<String> list = new LinkedList<>();
        list.add("one");
        list.add("two");
        list.add("three");
        return list;
    }

    @Override
    public void handleValue(List<String> value) {
        for (int i = 0; i < value.size(); ++i) {
            value.set(i, "(" + value.get(i) + ")");
        }
    }

    @Override
    public void printValue(List<String> value) {
        for (String entry : value) {
            System.out.printf("- %s\n", entry);
        }
    }
}

public class Baz implements Foo<int[]> {
    @Override
    public int[] createValue() {
        return new int[]{ 1, 2, 3 };
    }

    @Override
    public void handleValue(int[] value) {
        for (int i = 0; i < value.length; ++i) {
            value[i] = value[i] * 10;
        }
    }

    @Override
    public void printValue(int[] value) {
        for (int entry : value) {
            System.out.printf("- %s\n", entry);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

于是，我们解决了在`handleValue`和`printValue`中不得不下倾的问题。所以现在我们是安全的，对吗？

不对！

我们没有解决类型问题——我们只是把它从声明中移到了用法中。当我们在我们的`Foo`数组中循环时，我们仍然将来自`createValue`的值保存为`Object`，并且仍然将它作为`Object`传递给`handleValue`和`printValue`。

Java 好心提醒我们:

```
$ javac App.java -Xlint:unchecked
App.java:6: warning: [unchecked] unchecked call to handleValue(T) as a member of the raw type Foo
            foo.handleValue(value);
                           ^
  where T is a type-variable:
    T extends Object declared in interface Foo
App.java:7: warning: [unchecked] unchecked call to printValue(T) as a member of the raw type Foo
            foo.printValue(value);
                          ^
  where T is a type-variable:
    T extends Object declared in interface Foo
2 warnings 
```

Enter fullscreen mode Exit fullscreen mode

但是...我们能做些什么呢？

# 解

```
public class App {
    public static void main(String[] args) {
        Foo[] foos = new Foo[]{ new Bar(), new Baz() };
        for (Foo<?> foo : foos) {
            createHandlePrint(foo);
        }
    }

    private static <T> void createHandlePrint(Foo<T> foo) {
        T value = foo.createValue();
        foo.handleValue(value);
        foo.printValue(value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个助手方法- `createHandlePrint`。这个方法有一个泛型参数，但是它的调用者没有传递它——既没有显式地也没有隐式地。我们只需要它在内部，能够声明`value`与传递给我们的`foo`的`createValue`返回的类型相同，所以将它传递回那个`foo`是安全的。

这只能归功于类型擦除。对于真正的泛型，`createHandlePrint`对于`T`的每个参数化都有不同的方法，我不能从`main`的同一行调用它们。除非我使用了反射——这有点违背了让编译器验证类型的目的...(也不适用于泛型的所有实现)