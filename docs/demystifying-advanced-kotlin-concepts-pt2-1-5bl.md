# 揭开先进科特林概念的神秘面纱第 2-1 部分

> 原文：<https://dev.to/praveenkajla/demystifying-advanced-kotlin-concepts-pt2-1-5bl>

本文是[揭开 Kotlin 高级概念第 1 部分](https://dev.to/praveenkajla/demystifying-advance-kotlin-concepts-a97)的后续文章

# 科特林中的班级

* * *

### 1。菲尔茨

记住 Kotlin 中的类不能有字段。

但是，在使用自定义访问器时，有时有必要拥有一个支持字段(存储公共属性公开的数据的私有字段)。
出于这些目的，Kotlin 提供了一个自动支持字段，可以使用 ***字段*** 标识符访问该字段:

让我们创建一个具有可变属性的客户类(使用 var 关键字)*。
我们也为这个属性创建定制的 getter 和 setter。* 

```
 class Customer(){

    var lastPurchased:Double = 0.0 // Property type is optional as kotlin provide type inference
        get() = field //field is built-in variable and can only be used in getter and setter
        set(value){
            if(value>100){
                field = value
            }
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里 getter 和 setter 是可选的，因为 kotlin 提供了默认的 getter 和 setter。

但是因为我们是有条件地设置值，所以我们需要自己的 getter 和 setter。

```
 fun main(args: Array<String>) {

    val customer = Customer() // no new keyword needed to instantiate
    println(customer.lastPurchased)
    customer.lastPurchased = 200.0
    println(customer.lastPurchased)
    customer.lastPurchased = 50.0
    println(customer.lastPurchased)

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行这个:

```
output -> 
  0.0
  200.0
  200.0 
```

Enter fullscreen mode Exit fullscreen mode

在下列情况下，将自动创建属性的支持字段:

*   自定义 getter 或 setter 通过字段标识符引用它
*   使用至少一个访问器的默认实现

对于字段不够用的情况，无论出于什么原因，我们都不能只使用支持字段，那么唯一的解决方法就是创建一个私有属性，比如:

```
 class Customer(){

    private var myCustomField = 10

    .... 
```

Enter fullscreen mode Exit fullscreen mode

### 2。后期初始化

我们经常需要对属性进行后期初始化。

让我们为上面的客户类创建一个 web 控制器，它从一个存储库(数据库)

```
 interface Repository{
    fun getAll(): List<Customer>
}

class CustomerController(){

    var repository:Repository // ide would show "Property must be initialized or be abstract"

    fun index():String{
        return repository.getAll().toString()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们希望 repository 是一个需要由某个 IoC 容器初始化的属性，也就是说，我们没有将这个属性作为构造函数的一部分传递，但是我希望它稍后被初始化。

因此，一种解决方案是使其可为空，即

```
 class CustomerController(){

    var repository:Repository? = null

    fun index():String{
        return repository?.getAll().toString()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是每次我们访问存储库的时候，我们都需要在它后面加上？。我们也不希望它为空，或者让阅读我们代码的人认为我们希望它为空。

所以科特林提供修饰语 ***lateinit*** 来解决这个问题:

```
 class CustomerController(){

    lateinit var repository:Repository // tells the compiler that we are going to initialize it later on.

    ... 
```

Enter fullscreen mode Exit fullscreen mode

然而，这并不能保证一切安全。
如果我们在没有初始化存储库的情况下运行

```
 fun main(args: Array<String>) {
    val cc = CustomerController()
    cc.index()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不会得到空引用，但是:

```
 Exception in thread "main" kotlin.UninitializedPropertyAccessException: lateinit property repository has not been initialized
    at CustomerController.index(Class.kt:15)
    at ClassKt.main(Class.kt:21) 
```

Enter fullscreen mode Exit fullscreen mode

这使得调试更加容易。

### 3。嵌套类

就像嵌套函数或局部函数一样，我们可以在 kotlin 中使用嵌套类。

让我们创建一个具有函数 ***listFolder*** 的类***directory explorer***，它还封装了检查特定用户是否可以访问该文件夹的
权限的功能。

```
class DirectoryExplorer(){

    class PermissionCheck(){

        fun validatePermission(user: String) {

        }

    }

    fun listFolder(folder:String,user:String){

        val permissionCheck = PermissionCheck()
        permissionCheck.validatePermission(user)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此我们将 PermissionCheck 作为嵌套类。另外，我们可以访问这个嵌套类并创建它的一个实例。

```
 fun main(args: Array<String>) {
    val de = DirectoryExplorer()
    val pc = DirectoryExplorer.PermissionCheck()
} 
```

Enter fullscreen mode Exit fullscreen mode

如果不想让它被访问或实例化，就将其设为私有。

现在，如果我们想在嵌套类中访问外部类的属性呢？为此，我们必须使用修饰符 ***内部*** 作为嵌套类的前缀。

```
 class DirectoryExplorer(val user:String){

    inner class PermissionCheck(){
        fun validatePermission() {
            if(user != "Bruce"){

            }
        }
    }
}

fun main(args: Array<String>) {
    val de = DirectoryExplorer("Bruce")
    val pc = DirectoryExplorer.PermissionCheck() //Ide would show "Constructor of inner class PermissionCheck can be called only with receiver of containing class"
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 ***内部*** 修饰符使得嵌套类成为外部类的实际实例的一部分，这就是为什么我们不能简单地访问内部类。

所以它可以作为类的实际实例的一部分来访问，例如

```
...

    val pc = DirectoryExplorer().PermissionCheck() 

... 
```

Enter fullscreen mode Exit fullscreen mode

### 下一课:第二课*