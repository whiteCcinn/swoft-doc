# ORM

ORM用于实现面向对象编程语言里不同类型系统的数据之间的转换，ORM有多种设计模式，swoft采用的是data mapper，业务和实体分开，但是也实现了类似ActiveRecord的操作方式，其实都是同一个实现的。

数据库操作分为两种基础\(类ActiveRecord\)和高级的\(data mapper\)，基础的用于快速开发和常见的查询操作，高级的用于事务和一些复杂的业务查询。

## 查询器

查询器允许使用面向对象的方法组装SQL，无论是基础的还是高级的语法都是一样的。

```php
// ActiveRecord操作
$query1 = User::query()->selects(['id', 'sex' => 'sex2'])->leftJoin(Count::class, 'count.uid=user.id')->andWhere('id', 419)
            ->orderBy('user.id', QueryBuilder::ORDER_BY_DESC)->limit(2)->getDefer();
$result1 = $query1->getResult();

// 高级的操作方式
$em = EntityManager::create();
$query = $em->createQuery();
$query->select("*")->from(User::class, 'u')->leftJoin(Count::class, ['u.id=c.uid'], 'c')->whereIn('u.id', [419, 420, 421])
    ->orderBy('u.id', QueryBuilder::ORDER_BY_DESC)->limit(2);
$result = $query->getDefer()->getResult();
$sql = $query->getSql();
$em->close();
```

操作语法列表：

> 语句中的表名，可以是数据库表名，也可以是表对应的实体类名
>
> 查询器都是通过getResult\(\)和getDefer\(\)连个方法获取结果
>
> 插入操作，成功返回插入ID，如果ID传值，插入数据库返回0，错误返回false
>
> 更新操作，成功返回影响行数，如果失败返回false
>
> 删除操作，成功返回影响行数，如果失败返回false
>
>  查询操作，单条记录成功返回一维数组或一个实体，多条记录返回多维数组或实体数组

| 方法 | 功能 |
| :--- | :--- |
| insert | 指定插入表 |
| update | 指向更新的表 |
| delete | 删除语句 |
| select | 查询字段 |
| selects | 查询多个字段 |
| from | 指定删除和查询的表 |
| innerJoin | 内连接 |
| leftJoin | 左连接 |
| rightJoin | 右连接 |
| where | where条件语句 |
| andWhere | where and 条件语句 |
| openWhere | where 里面左括号 |
| closeWhere | where 里面右括号 |
| orWhere | where or 条件语句 |
| whereIn | where in语句 |
| whereNotIn | where not in 语句 |
| whereBetween | where between and 语句 |
| whereNotBetween | where not between and语句 |
| having | having语句 |
| andHaving | having and语句 |
| orHaving | having or语句 |
| havingIn | having in语句 |
| havingNotIn | having not in语句 |
| havingBetween | having between and语句 |
| havingNotBetween | havin not between and 语句 |
| openHaving | having括号开始语句 |
| closeHaving | having括号结束语句 |
| groupBy | group by语句 |
| orderBy | order by语句 |
| limit | limit语句 |
| set | 设置更新值 |
| setParameter | 设置参数 |
| setParameters | 设置多个参数 |
| getSql | 获取执行语句 |



