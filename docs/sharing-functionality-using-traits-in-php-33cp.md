# 在 PHP 中使用 Traits 共享功能

> 原文：<https://dev.to/sarfraznawaz2005/sharing-functionality-using-traits-in-php-33cp>

官方[文件](http://php.net/manual/en/language.oop5.traits.php)将特质定义为:

> 特征是 PHP 等单一继承语言中代码重用的一种机制。Trait 旨在通过使开发人员能够在位于不同类层次结构中的几个独立类中自由地重用方法集，来减少单一继承的一些限制。特征和类的组合的语义以降低复杂性的方式定义，并且避免了与多重继承和混合相关的典型问题。

那么这意味着什么呢？让我们通过实际例子来了解一下。假设我们有一个聊天应用程序，数据库包含两个表；`users`和`messages`，我们希望能够为每个实体提供 [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 功能。下面是`User`的示范课:

```
class User {
    protected $table = 'users';

    public function getAll() {
        return DB::getAll($this->table);
    }

    public function get($id) {
        return DB::get($this->table, $id);
    }

    public function add($data) {
        return DB::add($this->table, $data);
    }

    public function update($id) {
        return DB::update($this->table, $id);
    }

    public function remove($id) {
        return DB::remove($this->table, $id);
    }
} 
```

`Message`模型类:

```
class Message {
    protected $table = 'messages';

    public function getAll() {
        return DB::getAll($this->table);
    }

    public function get($id) {
        return DB::get($this->table, $id);
    }

    public function add($data) {
        return DB::add($this->table, $data);
    }

    public function update($id) {
        return DB::update($this->table, $id);
    }

    public function remove($id) {
        return DB::remove($this->table, $id);
    }
} 
```

乍一看，我们可以看到这两个类对于这五个方法有完全相同的代码，唯一的区别是`$table`不同。这种气味和遵循[不重复自己](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself)的原则，我们当然可以利用继承。所以我们创建了一个新的抽象类，它将包含这五个方法形式的公共功能，然后这两个类都将扩展这个基类:

```
abstract class Model {     
    public function getAll() {
        return DB::getAll($this->table);
    }

    public function get($id) {
        return DB::get($this->table, $id);
    }

    public function add($data) {
        return DB::add($this->table, $data);
    }

    public function update($id) {
        return DB::update($this->table, $id);
    }

    public function remove($id) {
        return DB::remove($this->table, $id);
    }
} 
```

现在我们的`User`和`Message`类是这样的:

```
class User extends Model {
    protected $table = 'users';
}

class Message extends Model {
    protected $table = 'messages';
} 
```

就这样，现在这两个类都从基类`Model`继承了那五个方法，现在不需要重复了，完美！

现在想象一下，客户机突然要求加密这两个表中的一些字段。为此，我们创建了一个包含两个方法`encrypt()`和`decrypt()` :
的类

```
class Encoder {
    public static function encrypt($text, $key) {
        // code to encrypt text
    }

    public static function decrypt($text, $key) {
        // code to decrypt text
    } 
} 
```

但是我们现在怎么用呢？首先想到的是在需要时使用它:

```
$user = new User();
$user->add(array('email' => 'foo@bar.com', 'password' => Encoder::encrypt('some_password', 'key'))); 
```

这听起来不错，但是每当我们添加一个用户时，它应该自动将`encrypt()`应用于`password`字段或我们想要的其他字段，这不是更好吗？这当然会帮助我们节省时间，避免我们在这样的领域操作时重复`encrypt()`和`decrypt()`的代码。所以为了达到这个目的，我们也用这个功能扩展了我们的类:

```
// wrong code...
class User extends Model extends Encoder {
    protected $table = 'users';
} 
```

上面的我们当然做不到。我们不能用多个类来扩展一个类，因为在 PHP 中你只能做单一继承。

所以我想到的另一个想法是扩展`Model`类来代替:

```
abstract class Model extends Encoder {
    // ....
} 
```

这肯定是可行的，我们将在所有的模型类中拥有可用的`Encoder`功能，我们可以根据需要使用它。但是仔细想想。将模型和加密功能混合在一起是个好主意吗？它们是完全不同的东西，将加密逻辑放入您的业务逻辑听起来不是一个好主意。但是怎么做呢？我们可以用性状！

因此，让我们将前面的`Encoder`类转换成一个特征:

```
trait Encoder {
    public static function encrypt($text, $key) {
        // code to encrypt text
    }

    public static function decrypt($text, $key) {
        // code to decrypt text
    } 
} 
```

为了在其他一些类中使用这个特性，我们需要使用`use Encoder;`语法，所以让我们在我们的类中添加这个功能:

```
class User extends Model {
    use Encoder;
    protected $table = 'users';
}

class Message extends Model {
    use Encoder;
    protected $table = 'messages';
} 
```

现在，不仅我们的类继承了基类`Model`的功能，还可以使用`Encoder` trait 的功能。我们还能够从核心模型逻辑中分离出加密逻辑。因此，在我们的`User`和`Message`类中，我们现在可以使用`$this->encrypt()`或`$this->decrypt()`方法，就好像它们是在其中定义的一样(实际上这就是 PHP 在运行时所做的)。我们已经能够克服单一继承的缺点。

所以现在当你重新审视这篇文章顶部给出的特质定义时，它应该会更有意义。所以特征只是代码重用的一种方式。要了解更多关于特征及其特性的信息，请参考其[文档](http://php.net/manual/en/language.oop5.traits.php)。

> 注意:有人可能会说我们的`User`和`Message`类也是幕后的模型逻辑，但这只是一个给出特征概念的例子。在我们需要公共功能的地方，我们也可以有控制器或其他具体的类。

[![](img/7bd729bf579c42a821def6b42593f153.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tHaQXJBE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/0EybE_hXvo4)