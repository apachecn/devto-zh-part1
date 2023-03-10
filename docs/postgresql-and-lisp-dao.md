# Postgresql 和 Lisp (DAO)

> 原文：<https://dev.to/jreynoso666/postgresql-and-lisp-dao>

[http://www.huuii.com/people/juan/3868](http://www.huuii.com/people/juan/3868)

Postgresql 和 Lisp

有一个项目叫做后现代。

后现代是一个用于与 PostgreSQL 数据库交互的公共 Lisp 库。

详情请见:[http://marijnhaverbeke.nl/postmodern/#quickstart](http://marijnhaverbeke.nl/postmodern/#quickstart)

首先，我将在 postgresql 中创建用户和数据库:

用密码“foobar”创建用户 foo

创建数据库栏所有者 foo

现在我们需要通过 quicklisp 加载库

(ql:quickload“后现代”)

要载入《后现代》:

负载 1 ASDF 系统:

```
postmodern 
```

Enter fullscreen mode Exit fullscreen mode

；加载“后现代”

(“后现代”)

之后，我们将使用以下内容创建一个文件:

(包装内:后现代)

；；；用数据库
的值定义 var(def var*d b-parameters*'(" bar " " foo " " foobar " " localhost ":POOLED-P T))

；；定义连接 postgresql 服务器的宏
(def macro with-database(&body query)
“该宏创建与 *db-parameter* 中指定数据库的连接和断开，并执行查询。”
`(后现代:with-connection*d b-parameters*，@query))

创建表格

；；；定义一个简单表格

(水果类()

((id-fruit:accessor id-fruit:col-type serial:initarg:id-fruit)

(名称:访问者名称:列类型字符串:initarg:名称:initform " "))

(:documentation“道类为一果录。”)

(:元类后现代:道类)

(:表名水果)(:关键字 id-水果))

；；在 postgresql 服务器中创建表

(使用-数据库(创建-表'水果')

数据库访问对象(CRUD)

；；插入
(与-数据库
(插入-刀(制作-实例'水果:名称“苹果”))
(插入-刀(制作-实例'水果:名称“橙子”))

；；选择
(带-数据库
(get-dao 'fruits 1))

(与-数据库
(选择-道'水果(:= ' id-水果 2)))

；；定义一个显示信息的方法
(def method read-information((obj fruits))
(format t " id = ~ a ~ % name = ~ a ~ % "(id-fruit obj)(name obj)))

；；删除
(带-数据库
(删除-Dao(make-instance ' fruits:id-fruit 1)))

；；更新
(defun the-update(id new-name)
(let((record nil))
；；首先我们需要记录
(setf 记录(带数据库
(get-Dao ' fruits id)))
；；新值
(setf(name record)new-name)
；；最后更新记录
(用-database
(更新-dao 记录)))