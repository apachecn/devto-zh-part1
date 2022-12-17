# Sugar ORM:在 Android 中使用 SQLite 数据库的一种更简单的方法。

> 原文：<https://dev.to/kwabenberko/sugar-orm-an-easier-way-to-work-with-sqlite-databases-in-android-cpg>

如果您已经使用 Android SQLite 数据库有一段时间了，您会注意到一些事情:设置它需要大量的时间和大量的样板代码。

我最近发现了 [Sugar ORM](https://github.com/chennaione/sugar) ，这是一个非常棒的库，它使得使用 SQLite 数据库变得非常简单，而且耗时更少。

如果您还不知道，一个 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) (对象关系映射器)允许您将数据表示为对象，然后将这些对象保存在关系数据库中，自动为您处理数据转换。

要通过 gradle 安装 Sugar ORM，只需将这一行添加到您的模块级 gradle 依赖项中，并同步您的项目:

```
compile 'com.github.satyan:sugar:1.5' 
```

Enter fullscreen mode Exit fullscreen mode

撰写本文时的当前版本是 1.5 版。

Sugar ORM 只需要对您的清单文件进行最低限度的配置。您必须通过更改应用程序标签中的 android name 属性来指定 **SugarApp** 作为您的应用程序类。您还需要包含一些关于您的数据库的元数据，例如您的**数据库名称**、**版本**、**查询日志**和**域包名称**。您的清单文件应该如下所示:

```
 <application
        android:name="com.orm.SugarApp"
        android:allowBackup="true"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <meta-data android:name="DATABASE" android:value="sugar_example.db" />
        <meta-data android:name="VERSION" android:value="1" />
        <meta-data android:name="QUERY_LOG" android:value="true" />
        <meta-data android:name="DOMAIN_PACKAGE_NAME" android:value="com.kwabenaberko" />
    </application> 
```

Enter fullscreen mode Exit fullscreen mode

当使用 sugar ORM 时，所有想要持久化的数据模型或类都需要扩展 SugarRecord，并且至少有一个空的默认构造函数，如下:

```
import com.orm.SugarRecord;

public class Developer extends SugarRecord{
    private String firstname;
    private String lastname;
    private String favouriteLanguage;

    public Developer(){

    }

    public Developer(String firstname, String lastname, String favouriteLanguage){
        this.firstname = firstname;
        this.lastname = lastname;
        this.favouriteLanguage = favouriteLanguage;
    }

    public String getFirstname() {
        return firstname;
    }

    public void setFirstname(String firstname) {
        this.firstname = firstname;
    }

    public String getLastname() {
        return lastname;
    }

    public void setLastname(String lastname) {
        this.lastname = lastname;
    }

    public String getFavouriteLanguage() {
        return favouriteLanguage;
    }

    public void setFavouriteLanguage(String favouriteLanguage) {
        this.favouriteLanguage = favouriteLanguage;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦一切都设置好了，我们就可以开始在模型上执行操作了。

#### 插入新记录

```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Developer developer = new Developer("Kwabena", "Berko", "JavaScript");
        developer.save();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

插入新记录时， **Sugar ORM** 会自动为您添加一个自动递增的 id 字段。

#### 通过 id 选择现有记录

```
 Developer developer = Developer.findById(Developer.class, 1); // 1 is the record's id.
 Log.v("RESULTS", developer.getId() + 
                ", " + developer.getFirstname() +
                ", " + developer.getLastname() +
                ", " + developer.getFavouriteLanguage()
 ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 更新现有记录

```
Developer developer = Developer.findById(Developer.class, 1);
developer.setFavouriteLanguage("Golang");
developer.save(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 选择所有记录

```
List<Developer> developers = Developer.listAll(Developer.class);
for(Developer developer : developers){
    Log.v("RESULTS", developer.getId() +
            ", " + developer.getFirstname() +
            ", " + developer.getLastname() +
            ", " + developer.getFavouriteLanguage()
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 删除现有记录

```
Developer developer = Developer.findById(Developer.class, 1);
developer.delete(); 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！