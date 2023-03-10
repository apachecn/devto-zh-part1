# Python3 中的接口和注释

> 原文：<https://dev.to/dmerejkowsky/interfaces-and-annotations-in-python3>

*原载于[我的博客](https://dmerej.info/blog/post/interfaces-and-annotations-in-python3/)*

TL；DR:python 3 中的注释在使用`abc`元类声明接口时非常有用。

如果你想在这里停止阅读，我不会阻止你:)

如果没有，请允许我带你进行一次小小的旅行，在那里我会解释这一切是怎么回事...

# 一个 Java 例子

让我们假设您有一个包含员工记录的`PostgreSQL`数据库。
您正在构建一个网站，该网站将能够显示给定 ID 号的雇员的信息。

您有一个名为`SQLRepository`的类，它允许您通过 ID 获取雇员，当用户通过正确的 URL 时，将调用一个带有`viewEmployee`方法的`EmployeeController`类。

最后，您有一个`Renderer`类，它知道如何生成员工的 HTML 描述。

## 第一次尝试

实现控制器最明显的方式如下:

```
package mypackage.controller;

import mypackage.Employee;
import mypackage.repository.SQLRepository;
import mypackage.renderer.Renderer;

public class Controller {
    private SQLRepository repository;
    private Renderer renderer;

    public Controller() {
        repository = new SQLRepository();
        renderer = new Renderer();
    }

    public String viewEmployee(int id) {
        Employee employee = repository.getEmployeeByID(id);
        String res = renderer.renderEmployee(employee);
        return res;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这个实现有一些问题。

## 测试控制器

会很困难，对吧？

一旦你想实例化一个新的控制器，`SQLRepository()`的构造函数也会被调用，这意味着你必须建立一个`PostgreSQL`数据库，这样你就可以运行测试。

我们说“控制流”从控制器到 SQLRepository 类，或者在控制器和 SQLRepository 之间有一个*运行时依赖关系*。

由于`Controller.java`包含了`SQLRepository`类的`import`，我们说在控制器和 SQLRepository 类之间有一个*源代码依赖*。

这就是接口的用武之地。

## 介绍一个界面

我们将通过引入接口来“解耦”控制器和 SQL repository:

```
// In Repository.java
public interface Repository {
    public Employee getEmployeeByID(int id);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们告诉`SQLRepository`实现接口:

```
+import mypackage.repository.Repository; 
-public class SQLRepository {
+public class SQLRepository implements Repository { 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将存储库作为参数传递给控制器构造函数:

```
-import mypackage.repository.SQLRepository; +import mypackage.repository.Repository;
 import mypackage.renderer.Renderer;

 public class Controller {
-    private SQLRepository repository; +    private Repository repository;
     private Renderer renderer;

-    public Controller() {
-        repository = new SQLRepository(); +    public Controller(Repository repository) {
+        this.repository = repository;
         renderer = new Renderer();
     } 
```

Enter fullscreen mode Exit fullscreen mode

注意，控制流仍然是从控制器到存储库，但是现在`Controller.java`文件不再依赖于`SQLRepository.java`源文件。

相反，两个源文件现在都依赖于`Repository.java`接口。

这就是所谓的“依赖倒置原则”。

描述这种变化的另一种方式是，我们现在在控制器和存储库之间有了一个*边界*，接口允许代码跨越边界。

使用`Repository`接口的不同实现，现在可以在不接触数据库的情况下测试控制器:

```
public class FakeRepository implements Repository {
    private ArrayList<Employee> employees;

    public FakeRepository() {
        employees = new ArrayList<Employee>();
    }

    public void addEmployee(Employee employee) {
        employees.add(employee);
    }

    public Employee getEmployeeByID(int id) {
        return employees.get(id);
    }
}

public class ControllerTest extends TestCase {

    // ...

    public void testViewEmployee() {
        Employee smith = new Employee("John Smith");

        FakeRepository fakeRepository = new FakeRepository();
        fakeRepository.addEmployee(smith);

        Controller controller = new Controller(fakeRepository);

        String html = controller.viewEmployee(0);
        assertEquals(html, "<span class=\"employe\">John Smith</p>");
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

注意:Java 中的接口除了依赖倒置还有其他用途，这里的例子主要是为了后面的内容。

# 同样的事情，用 Python 来说

让我们用 Python 重写我们的第一版 Java 代码:

```
# First version, hard to test 
from renderer import Renderer
from repository import SQLRepository

class Controller:
    def __init__(self):
        self.repository = SQLRepository()
        self.renderer = Renderer()

    def view_employee(self, id):
        employee = self.repository.get_employee_by_id(id)
        res = self.renderer.render_employee(employee)
        return res 
```

Enter fullscreen mode Exit fullscreen mode

同样的，我们在`controller.py`和`repository.py`之间有一个源代码依赖。

现在，让我们颠倒一下依赖关系:

```
-from repository import SQLRepository 
 class Controller:
-    def __init__(self):
-        self.repository = SQLRepository() +    def __init__(self, repository):
+        self.repository = repository
         self.renderer = Renderer() 
```

Enter fullscreen mode Exit fullscreen mode

是的，这就是我们要改变的！

使用伪造品仓库进行测试非常简单:

```
from controller import Controller
from employee import Employee

class FakeRepository:
    def __init__(self):
        self._employees = list()

    def add_employee(self, employee):
        self._employees.append(employee)

    def get_employee_by_id(self, id):
        return self._employees[id]

def test_view_employee():
    smith = Employee("John Smith")

    fake_repository = FakeRepository()
    fake_repository.add_employee(smith)

    controller = Controller(fake_repository)

    assert "John Smith" in controller.view_employee(0) 
```

Enter fullscreen mode Exit fullscreen mode

这里刚刚发生了什么？

我们完全消除了存储库和控制器之间的源代码依赖。

我们*确实*引入了一个接口，但是它隐藏在实现的后面:控制器和存储库之间的接口只是从控制器发送到其存储库实例的所有方法的列表。

这也被称为“鸭子打字”:我们可以通过一只海象而不是一只鸭子，只要它是一只嘎嘎叫的海象。

注意:你可能听过鸭式打字的另一种定义。我觉得这个更准确。它是由 Avdi Grim 创造的，来自 Ruby Tapas 的名声。

## 鸭子打字的问题

让我们假设数据库模式发生了变化。我们现在在同一个数据库中有几个公司，ID 只对给定的公司是唯一的。

这意味着在查询存储库时，我们需要提供公司名称和员工 id。

`getEmployeeByID`必须被重命名为`getEmployee`,现在接受一个字符串(公司名称)和一个整数(员工 id)。

在 Java 世界中，只要我们重命名了`SQLRepository`类中的方法，就会出现编译错误。

```
SQLRepository.java:[6,8]
mypackage.repository.SQLRepository is not abstract and does not override
abstract method getEmployeeByID(int) in mypackage.repository.Repository 
```

Enter fullscreen mode Exit fullscreen mode

事实上，我们会得到各种各样的错误，直到我们也改变了`Repository`接口、`FakeRepository`类、生产代码和测试代码。

但是在 Python 中，不会发生这样的事情。我们的测试会继续通过，而产品代码会崩溃。

```
AttributeError: 'SQLRepository' object has no attribute 'get_employee_by_id' 
```

Enter fullscreen mode Exit fullscreen mode

(顺便说一下，这就是“静态”和“动态”类型的区别)

有许多方法可以解决这个问题:

*   编写集成测试，检查跨越边界时会发生什么。
*   使用`mock.MagicMock(SQLRepository)`以确保`FakeRepository`具有与`SQLRepository`相同的方法
*   使用由 linter 提供的静态分析
*   从静态类型语言中获取灵感，编写一个显式的`Repository`接口。

在本文中，我们将只讨论最后一种方法，但请记住，其他方法也可以很好地工作:)

无论如何，让我们改变代码，使`SQLRepository`和`FakeRepository`都从`Repository`类继承。

我们如何确保接口中的所有方法都被实现？

## 黑暗时代

很久以前，你必须这样写:

```
class Repository:

    def get_employee(self, company_name, id):
        raise NotImplementedError() 
```

Enter fullscreen mode Exit fullscreen mode

这样，如果您忘记覆盖`get_employee`方法，当调用该方法时，您会得到一个`NotImplementedError`异常。

但是有时你*确实*有一些还没有实现的代码，你想用`NotImplementedError`向你的函数的调用者发出信号。

如何区分真正的“未实现”错误和您在子类中定义的方法名称中的拼写错误？

## 元类来救援了！

现如今，最好把你的界面写成这样:

```
import abc

class Repository(metaclass=abc.ABCMeta):

    @abc.abstractmethod
    def get_employee(self, company_name, id):
        pass 
```

Enter fullscreen mode Exit fullscreen mode

这样，如果你没有实现所有的抽象方法，当子类被实例化时，你会得到一个错误:

```
TypeError: Can't instantiate abstract class FakeRepository with abstract methods get_employee 
```

Enter fullscreen mode Exit fullscreen mode

## 记录

但是还有一个问题。如果您没有使用正确的类型调用该方法，比如使用字符串而不是整数作为`id`参数，会怎么样？

直到不久前，一种解决方案是使用 docstring 来实现这一点，然后使用类似于`sphinx.autodoc`的工具来生成漂亮的 HTML 文档:

```
@abc.abstractmethod
def get_employee(self, company_name, id):
    """ Fetch an employee from the database

    :param company_name: name of the company
    :param id: ID of the employee in the company
    :type company_name: string
    :type id: int
    :returns: an `Employee` instance

    """
    pass 
```

Enter fullscreen mode Exit fullscreen mode

我不知道你怎么想，但我不觉得这真的可读。

事实上，至少还有另外两种方法来记录参数名称和类型:

```
# Numpy style def get_employee(self, company_name, id):
    """ Fetch an employee from the database

    Parameters
    ----------
    company_name : str
      Name of the company
    id : integer
      ID of the employee

    Returns
    -------
    An Employee instance
    """

# Google style: def get_employee(self, company_name, id):
    """ Fetch an employee from the database

    Args:
      company_name : name of the company as a string
      id : id of the employee as an integer
    Returns:
      An employee instance
    """ 
```

Enter fullscreen mode Exit fullscreen mode

但是在任何情况下，您都必须两次拼写参数的名称:一次在函数定义中，一次在 docstring 中。

## 注解营救！

注释是 Python3 中添加的 Python 特性。

基本上，您可以在参数名称后使用冒号来指定它们的类型，并在定义的末尾添加一个箭头来指定返回的类型:

```
 import abc

class Repository(metaclass=abc.ABCMeta):

    @abc.abstractmethod
    def get_employee(self, company_name: str, id: int) -> Employee
        pass 
```

Enter fullscreen mode Exit fullscreen mode

我真的很喜欢这个解决方案。我发现它简洁、可读性强，并且当函数和参数的名称很明显时，它使得去掉整个 docstring 成为可能。

所以如果你需要在 Python3 中引入一个接口，我强烈建议你使用注释来描述你的参数类型。未来你和你界面的用户会为此感谢你。

## 最后一句话

它们是像 [mypy](http://mypy-lang.org/) 这样的工具，甚至更进一步，使用注释为 Python 提供渐进的静态类型。

我从来没有使用过它们中的任何一个，因为现在我很高兴通过 linters 和自动测试来捕捉类型错误，但是如果你有，请让我知道！

更新:一年后，我终于给了 mypy 一次机会。你可以在名为[给我的小宝贝一个机会](https://dmerej.info/blog/post/giving-mypy-a-go/)的博客文章中读到更多。