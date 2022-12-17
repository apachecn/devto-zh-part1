# Lotus DB 迁移技巧

> 原文：<https://dev.to/leandrocp/lotus-db-migrations-tips-20ea>

与 Rails 一样，Lotus 也支持通过 [Sequel Migrations](http://sequel.jeremyevans.net/rdoc/files/doc/migration_rdoc.html) 进行数据库迁移。这很简单，但是有些事情不是很清楚。

**创建迁移**

```
lotus g migration create_my_table 
```

**启用 UUID(仅限 PostgreSQL)**

```
Lotus::Model.migration do
  up do
    execute 'CREATE EXTENSION "uuid-ossp"'
  end

  down do
    execute 'DROP EXTENSION "uuid-ossp"'
  end
end 
```

**使用 UUID 作为主键**

```
Lotus::Model.migration do
  up do
    create_table :my_table do
      column :id, :uuid, null: false, default: Sequel.function(:uuid_generate_v4), primary_key: true
    end
  end

  down do
    drop_table :my_table
  end
end 
```

**使用 UUID 作为外键**

```
Lotus::Model.migration do
  up do
    create_table :my_table do
      column :id, :uuid, null: false, default: Sequel.function(:uuid_generate_v4), primary_key: true
      foreign_key :author_id, :authors, type: 'uuid', null: false
    end
  end
end 
```

**索引一栏**

```
Lotus::Model.migration do
  up do
    create_table :my_table do
      column :code, :integer, null: false
      index :code
    end
  end
end 
```

**表文档**

```
Lotus::Model.migration do
  up do
    create_table :my_table do
      column :code, :integer, null: false
    end

    execute %Q(COMMENT ON TABLE my_table IS 'You should do it')
    execute %Q(COMMENT ON COLUMN my_table.code IS 'Easy and useful')
  end
end 
```

**向上迁移**

```
lotus db migrate 
```

**向下迁移**

没有`db migrate down`命令，尽管您可以指定要迁移的版本。只需指定`0`来迁移到初始版本(在任何迁移之前)。

```
lotus db migrate 0 
```