# 从现有的类定义生成 CakePHP 迁移类

> 原文：<https://dev.to/reoring/generate-cakephp-migration-class-from-existing-class-definition-6cg>

从现有的类定义生成 CakePHP 迁移类
我们创建了一个生成器，从现有的类定义生成 CakePHP 3 迁移。

我想我可以用它来创建一个有很多属性的类，然后为一个迁移类做一个草稿。

## 发电机类

```
 <?php

class MigratinoClassGenerator
{
  public function generate(string $className)
  {
    $ref = new ReflectionClass($className);
    $properties = $ref->getProperties();

    $migrationClass = new MigrationClass();

    foreach ($properties as $property) {
      $migrationClass->addColumn($property->getName());
    }

    return $migrationClass->generate($ref->getName());
  }
}

class MigrationClass
{
  private $columns = [];

  public function addColumn(string $name, $type = 'string')
  {
    $this->columns[] = [
      'name' => $name,
      'type' => $type,
    ];
  }

  public function generate(string $className)
  {
    $buf = [];

    $buf[] = '<?php';
    $buf[] = '';
    $buf[] = 'use Migrations\AbstractMigration;';
    $buf[] = '';
    $buf[] = 'class CreateProduct extends AbstractMigration';
    $buf[] = '{';
    $buf[] = '    public function up()';
    $buf[] = '    {';

    $tableName = strtolower($className);
    $buf[] = sprintf('        $this->table(\'%s\')', $tableName);

    foreach ($this->columns as $column) {
      $buf[] = $this->generateColumn($column);
    }

    $buf[] = '            ->create();';
    $buf[] = '    }';
    $buf[] = '';
    $buf[] = '    public function down()';
    $buf[] = '    {';
    $buf[] = '        $this->dropTable(\''. $tableName . '\');';
    $buf[] = '    }';
    $buf[] = '}';

    return join("\n", $buf);
  }

  private function generateColumn(array $column)
  {
    $template = <<<TPL
            ->addColumn('%s', 'string', [
                'default' => null,
                'limit'   => null,
                'null'    => false,
            ])
TPL;

    return sprintf($template, $this->camelCase($column['name']));
  }

  private function camelCase(string $input) {
    preg_match_all('!([A-Z][A-Z0-9]*(?=$|[A-Z][a-z0-9])|[A-Za-z][a-z0-9]+)!',
                   $input,
                   $matches);

    $ret = $matches[0];
    foreach ($ret as &$match) {
      $match = $match == strtoupper($match) ? strtolower($match) : lcfirst($match);
    }

    return implode('_', $ret);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 执行方法

```
<?php

$g = new MigratinoClassGenerator();
echo $g->generate('Product'); // クラス名を指定する 
```

Enter fullscreen mode Exit fullscreen mode

## 输出结果

```
<?php

use Migrations\AbstractMigration;

class CreateProduct extends AbstractMigration
{
    public function up()
    {
        $this->table('product')
            ->addColumn('id', 'string', [
                'default' => null,
                'limit'   => null,
                'null'    => false,
            ])
            ->addColumn('code', 'string', [
                'default' => null,
                'limit'   => null,
                'null'    => false,
            ])
            ->create();
    }

    public function down()
    {
        $this->dropTable('product');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode