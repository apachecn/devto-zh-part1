# 房间—Un Vistazo a los“Android 架构组件”

> 原文：<https://dev.to/devpicon/room-un-vistazo-a-los-android-architecture-components-3jk0>

[![](img/ed7e51556fcfc72b5563d065c6560905.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PGKfYo2t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArZBIQ1AH1ufDTn8KldAyHA.png)

不久前，在最近的 Google I/O 中，他们公布了“T0”架构组件的开发情况，这是一套图书馆，将成为 Android 团队为 Android 的新开发建立推荐架构的提案的一部分。

> ***更新至 2019 年 4 月 23 日:我已将存储库和示例更新至最新版本的文件室，一般情况下更新至 androidx。***

所谓*架构元件*由以下库或元件组成:

*   房间
*   生命资料
*   搬运生命周期
*   视图模型

如今，许多依赖项已有稳定版本。现在我们来回顾一下第一部分房间。

### **房间:一个 SQL 对象映射库**

长期以来，我们一直在创建一组类来确定表、语句和 CRUD 操作的结构。现在，那些时间？都结束了。

虽然文档很容易理解，但我还是按照下面的步骤开始测试此功能，在 Kotlin，这是因为网络上充斥着使用 Java 编写的示例。

#### 添加依赖项

只需添加所需的依赖项:

```
implementation "androidx.room:room-runtime:2.0.0"
kapt "androidx.room:room-compiler:2.0.0"
testImplementation "androidx.room:room-testing:2.0.0"
implementation "androidx.room:room-rxjava2:2.0.0"

compile 'io.reactivex.rxjava2:rxkotlin:2.1.0'
compile 'io.reactivex.rxjava2:rxandroid:2.0.1' 
```

#### 造井界定实体

透过加入房间特有的注解来建立竖井。在下面的示例中，我们定义了一个将存储任务信息的类，通过注释“**@ entity**room 将该 POJO 标识为特定实体的表示形式，而注释“**@ primary key**”将指示该 POJO

```
import androidx.room.Entity
import androidx.room.PrimaryKey

**@Entity**
data class Task( **@PrimaryKey** (autoGenerate = true) val id : Long, val description : String) 
```

有可能定义复合主键，所有这些特性都可以在 Entities 段的文档中直接找到。

#### 定义道

之后，我们必须定义我们的*【数据访问对象】* ) 的界面，如下所示。此界面原则上应带有注释 **[@dao](https://dev.to/dao)** ，以便 Room 识别它，然后对于我们需要定义的每项操作，我们将使用注释 [**@Query**](https://developer.android.com/reference/android/arch/persistence/room/Query.html)

```
import androidx.room.\*
import io.reactivex.Flowable

**@Dao**
interface TaskDao {
**@Query("SELECT \* FROM Task")**
 fun getAllTasks(): Flowable\<List\<Task\>\>

**@Query("SELECT \* FROM Task WHERE description = :description")**
 fun getTask(description: String): Task

**@Insert(onConflict = OnConflictStrategy._REPLACE_)**
 fun insertTask(task: Task)

**@Update**
 fun updateTask(tasks: Array\<Task\>)

**@Delete**
 fun deleteTask(tasks: Array\<Task\>)

} 
```

虽然这种表示方式很简单，但可以通过向查询发送参数来设计一些更复杂的表示方式。这些特点也可在[Dao](https://developer.android.com/topic/libraries/architecture/room.html#daos)部分的文件中找到。

#### 申报数据库

最后，我们需要对数据库进行声明；以将其识别为使用文件室的数据库。

```
import androidx.room.Database
import androidx.room.RoomDatabase

**@Database(entities = [Task::class],version = 1)**
abstract class AppDatabase : RoomDatabase() {
 abstract fun taskDao() : TaskDao
} 
```

为了能够访问数据库对象，我定义了一个类*【custom application】*，其方式类似于如下所示:

```
import android.app.Application
import androidx.room.Room
import com.devpicon.android.myarchcomponentssampleapplication.AppDatabase

class MyApplication : Application() {
 companion object {
 lateinit var database : AppDatabase
 }

 override fun onCreate() {
 super.onCreate()
**database = Room.databaseBuilder(_applicationContext_, AppDatabase::class._java_, "sample-db").build()**
 }
} 
```

#### 考道？

我们创建了一个班级来容纳我们的测试。通过*immemorydatabasebuilder*功能，我们可以生成我们数据库的一个实例，该实例将在过程结束后销毁。

通过“[”florina muntenescu“](https://medium.com/u/d5885adb1ddf)”我知道，此外，还必须将调用添加到 allowmainthreadqueries 函数中，以便在主线上执行我们的查询。我把[你的帖子](https://medium.com/google-developers/room-rxjava-acb0cd4f3757)的链接留在参考资料科。

> **更新至 2019 年 4 月 23 日:文章最初提到需要添加一个****instanttaskperformer rule 实例，以确保任务立即运行，但迁移后我没有看到它更有用。**

下面我给大家举一个例子，说明如何为我们所写的 DAO 编写‘t0’测试，测试将是通过我们 DAO 中定义的操作插入和获取一个项目:

```
@RunWith(AndroidJUnit4::class)
class TaskDaoTest {

 private lateinit var database: AppDatabase

 private val DESCRIPTION = "Tarea 1"

 @Before
 fun initDb() {
 val context = ApplicationProvider.getApplicationContext\<Context\>()
 database = Room.inMemoryDatabaseBuilder(context,
 AppDatabase::class._java_)
 .allowMainThreadQueries()
 .build()
 }

 @Test
 fun insertAndGetTask() {
 // Insert
 database.taskDao().insertTask(Task(0, DESCRIPTION))

 // Query an specific description
 val foundTask: Task = database.taskDao().getTask(DESCRIPTION)
 assertNotNull(foundTask)
 assertEquals(DESCRIPTION, foundTask.description)

 // Query all elements
 val allTasks: Flowable\<List\<Task\>\> = database.taskDao().getAllTasks()
 allTasks.subscribe **{** tasks **-\>**  
_run_ **{**  
tasks.size
 assertThat(tasks.size, `is`(1))
 val task: Task = tasks[0]
 assertEquals(DESCRIPTION, task.description)
**}  
 }** }

 @After
 fun closeDb() {
 database.close()
 }
} 
```

完整的示例位于 github 上，我将对其进行更新，使其更加有用(目前仅从 Room 中插入和读取值)。

> **更新至 2019 年 4 月 23 日:我更新了依存关系，如果您想查看差异，可以查看存储库提交内容。**

[dev picon/arch-components-sample-app](https://github.com/DevPicon/arch-components-sample-app)

#### 参考文献

*   房间持久库| Android 开发者[https://developer . Android . com/topic/libraries/architecture/room . html](https://developer.android.com/topic/libraries/architecture/room.html)
*   架构组件| Android 开发者[https://developer . Android . com/topic/libraries/architecture/index . html](https://developer.android.com/topic/libraries/architecture/index.html)
*   科特林[https://kotlinlang.org/](https://kotlinlang.org/)
*   数据访问对象|教程点[https://www . tutorialspoint . com/design _ pattern/data _ Access _ Object _ pattern . htm](https://www.tutorialspoint.com/design_pattern/data_access_object_pattern.htm)
*   房间类文档| Android 开发者[https://developer . Android . com/reference/Android/arch/persistence/room/room . html](https://developer.android.com/reference/android/arch/persistence/room/Room.html)
*   房间🔗rx Java[https://medium . com/Google-developers/room-rx Java-ACB 0 CD 4 f 3757](https://medium.com/google-developers/room-rxjava-acb0cd4f3757)
*   InstantTaskExecutorRule[https://developer . Android . com/reference/Android/arch/core/executor/testing/InstantTaskExecutorRule . html](https://developer.android.com/reference/android/arch/core/executor/testing/InstantTaskExecutorRule.html)

* * *