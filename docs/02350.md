# Python 类变量在 python 中区分实例变量和类变量

> 原文：<https://dev.to/ogwurujohnson/distinguishing-instance-variables-from-class-variables-in-python-81>

在上一篇文章中，我们学习了如何创建一个类及其实例。今天我们将看看实例变量和类变量之间的区别。

实例变量是用于特定实例独有的数据的变量。实例变量的例子是我们在上一篇文章中使用的那些。

```
def __init__(self,first,last,pay):
    self.first = first
    self.last = last
    self.pay = pay
    self.email = first + '.' +last+ '@company.com' 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都是为我们要创建的每个唯一实例初始化的，这个上下文中的实例是我们要创建的雇员。

然而，
类变量是由一个类的所有实例共享的变量。因此，虽然实例变量对于每个实例可以是唯一的，如我们的姓名、电子邮件和付款；对于每个实例，类变量应该是相同的。

现在看一下前面例子中的 **(Employee)** 类，Employee 类的所有实例共享的任何数据都必须存储在一个类变量中，这样所有创建的实例都可以访问它。这种数据的一个例子可以是每个雇员每年的加薪。

为了做到这一点，假设我们创建了一个名为 **(apply_raise)** 的新方法，我们不使用类变量，而是使用实例变量；

```
class Employee:
    def __init__(self,first,last,pay):
        self.first = first
        self.last = last
        self.pay = pay
    def apply_raise(self):
        self.pay = int(self.pay * 1.04)
emp_1 = Employee('John','Son',60000)
print (emp_1.pay)
emp_1.apply_raise()
print(emp_1.pay) 
```

Enter fullscreen mode Exit fullscreen mode

运行这个程序会给出我们想要的输出；但是，如果我们有一个情况，我们需要输出筹集金额，甚至更新筹集金额。我们的**(大)**大脑会狡猾地改变 **(apply_raise)** 方法上的 **(1.04)** 比率，但是因为我们正在学习 OOP，所以这不是一个好方法。我们需要创建一个类变量来包含关于加薪的数据。

```
class Employee:
    raise_amount = 1.04 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们的 **(apply_raise)** 方法中，我们将拥有:

```
def apply_raise(self):
    self.pay = int(self.pay * self.raise_amount) 
```

Enter fullscreen mode Exit fullscreen mode

我们能够传入 **(raise_amount)** ，因为它是一个类变量，可以被父类中的所有实例访问。
现在如果我们打印募集金额；

```
print(Employee.raise_amount)
print(emp_1.raise_amount)
print(emp_2.raise_amount) 
```

Enter fullscreen mode Exit fullscreen mode

我们得到相同的输出，这证明类变量可以被你的类和类的实例同时访问。
所以这里发生的是，当我们试图访问一个实例上的属性时，它首先检查该实例是否包含该属性；如果没有，它检查父类或它继承的任何类是否包含这些属性；所以当我们从我们的实例中访问 **(raise_amount)** 时，它们实际上并没有那个属性，而是在访问类的 **(raise_amount)** 变量属性。

另一个地方我们可以使用一个**(类变量)**；比方说我们预计输出的员工人数。我们的整个代码现在看起来像这样:

```
class Employee:
    no_emps = 0
    raise_amount = 1.04
    def __init__(self,first,last,pay):
        self.first = first
        self.last = last
        self.pay = pay
        self.email = first + '.' +last+ '@company.com'
        Employee.no_emps +=1
    def fullname(self):
        return self.first + ' ' + self.last
    def apply_raise(self):
        self.pay = int(self.pay * self.raise_amount )
print(Employee.no_emps)    
emp_1 = Employee('John','Son',50000)
emp_2 = Employee('Jane','Son',80000)
print(Employee.no_emps)
print (emp_1.fullname())
print (emp_1.email)
print (emp_1.pay)
print (emp_2.pay)
emp_1.apply_raise()
emp_2.apply_raise()
print (emp_1.pay)
print (emp_2.pay) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将看看如何区分 python 中的常规、静态和实例方法。请尽可能多的提问，这样我们可以一起学习。也请在评论框中留下你的贡献，为所有人改善这篇文章。喜欢和分享。