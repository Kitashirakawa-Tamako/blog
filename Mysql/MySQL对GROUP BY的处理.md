| 前言

在工作中，同事想要对查询结果去重，写下了这样的SQL:
```sql
select a, b from table group by c
```
但是我一眼就看出，`a`和`b`的值绝对不依赖于`c`，同样的`c`会有不同的`a`和`b`组合，我说这肯定会报错，但是实际测试是可以运行的，返回了`a`和`b`的第一个组合。经过百度和AI的搜索，我查到了Mysql文档对GROUP BY的说明，整理后便有了这篇笔记。

| 以下内容摘自：https://dev.mysql.com/doc/refman/8.4/en/group-by-handling.html

SQL-92和更早版本不允许`SELECT`列、`HAVING`条件或`ORDER BY`列引用`GROUP BY`子句中未命名的非聚合列的查询。例如，此查询在标准SQL-92中是非法的，因为选择列表中的非聚合`name`列未出现在`GROUP BY`中：

```sql
SELECT o.custid, c.name, MAX(o.payment)
  FROM orders AS o, customers AS c
  WHERE o.custid = c.custid
  GROUP BY o.custid;
```

要使查询在SQL-92中是合法的，必须从选择查询列中去除`name`列或在GROUP BY子句中命名`name`列。

SQL：1999和更高版本允许这样的非聚合列，根据可选特性T301，如果它们在功能上依赖于GROUP BY列：如果name和custid之间存在这样的关系，那么查询就是合法的。例如，如果`custid`是`customers`表的主键，就符合这种情况。

MySQL实现了对功能依赖的检测。如果启用了`ONLY_FULL_GROUP_BY SQL`模式（5.7+默认情况下），MySQL会拒绝那些在`SELECT`列、`HAVING`条件或`ORDER BY`列中引用了既未在`GROUP BY`子句中命名也不在功能上依赖于它们的非聚合列的查询。

然而，当SQL ONLY_FULL_GROUP_BY模式启用时，MySQL允许在某些条件下使用未在GROUP BY子句中命名的非聚合列，前提是该列被限制为单个值，如以下示例所示：

```sql
mysql> CREATE TABLE mytable (
    ->    id INT UNSIGNED NOT NULL PRIMARY KEY,
    ->    a VARCHAR(10),
    ->    b INT
    -> );

mysql> INSERT INTO mytable
    -> VALUES (1, 'abc', 1000),
    ->        (2, 'abc', 2000),
    ->        (3, 'def', 4000);

mysql> SET SESSION sql_mode = sys.list_add(@@session.sql_mode, 'ONLY_FULL_GROUP_BY');

mysql> SELECT a, SUM(b) FROM mytable WHERE a = 'abc';
+------+--------+
| a    | SUM(b) |
+------+--------+
| abc  |   3000 |
+------+--------+
```

使用`ONLY_FULL_GROUP_BY`时，`SELECT`列表中也可能有多个非聚合列。在这种情况下，每个这样的列必须限制为`WHERE`子句中的单个值，并且所有这样的限制条件必须通过逻辑`AND`连接，如下所示：

```sql
mysql> DROP TABLE IF EXISTS mytable;

mysql> CREATE TABLE mytable (
    ->    id INT UNSIGNED NOT NULL PRIMARY KEY,
    ->    a VARCHAR(10),
    ->    b VARCHAR(10),
    ->    c INT
    -> );

mysql> INSERT INTO mytable
    -> VALUES (1, 'abc', 'qrs', 1000),
    ->        (2, 'abc', 'tuv', 2000),
    ->        (3, 'def', 'qrs', 4000),
    ->        (4, 'def', 'tuv', 8000),
    ->        (5, 'abc', 'qrs', 16000),
    ->        (6, 'def', 'tuv', 32000);

mysql> SELECT @@session.sql_mode;
+---------------------------------------------------------------+
| @@session.sql_mode                                            |
+---------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION |
+---------------------------------------------------------------+

mysql> SELECT a, b, SUM(c) FROM mytable
    ->     WHERE a = 'abc' AND b = 'qrs';
+------+------+--------+
| a    | b    | SUM(c) |
+------+------+--------+
| abc  | qrs  |  17000 |
+------+------+--------+
```


如果禁用 ONLY_FULL_GROUP_BY，MySQL 对标准 SQL 中 GROUP BY 语法的扩展允许在`SELECT`列、`HAVING` 条件或 `ORDER BY` 列中引用非聚合列，即使这些列不依赖于 GROUP BY 列。这会导致 MySQL 接受前面的查询。在这种情况下，服务器可以自由地从每个分组中选择任意值，因此，除非它们是相同的，否则选择的值是非确定性的，这可能不是你想要的。此外，通过添加 ORDER BY 子句无法影响从每个分组中选择值的过程。结果集的排序是在值选择之后发生的，ORDER BY 不会影响服务器在每个分组中选择哪个值。禁用`ONLY_FULL_GROUP_BY`主要在你了解数据的某些属性的情况下是有用的，例如在 GROUP BY 中未命名的每个组中的每个非聚合列的所有值都是相同的。

可以通过使用`ANY_VALUE()`引用非聚合列，在不禁用`ONLY_FULL_GROUP_BY`的情况下实现相同的效果。

以下讨论展示了函数依赖关系、在没有函数依赖关系时 MySQL 产生的错误信息，以及在缺乏函数依赖关系时让 MySQL 接受查询的方法。

启用`ONLY_FULL_GROUP_BY`时，此查询可能无效，因为`SELECT`列表中的非聚合`address`列未在`GROUP BY`子句中命名：

```sql
SELECT name, address, MAX(age) FROM t GROUP BY name;
```

如果`name`是`t`的主键或唯一的NOT NULL列，则查询有效。在这种情况下，MySQL识别出所选列在功能上依赖于分组列。例如，如果`name`是一个主键，它的值决定了`address`的值，因为每个组只有一个主键值，因此只有一行。因此，在组中选择地址值时没有随机性，并且不需要拒绝查询。

如果`name`不是`t`的主键或唯一的NOT NULL列，则查询无效。在这种情况下，无法推断出函数依赖关系，并且会发生错误：

```sql
mysql> SELECT name, address, MAX(age) FROM t GROUP BY name;
ERROR 1055 (42000): Expression #2 of SELECT list is not in GROUP
BY clause and contains nonaggregated column 'mydb.t.address' which
is not functionally dependent on columns in GROUP BY clause; this
is incompatible with sql_mode=only_full_group_by
```

如果能确定，对于给定的数据集，每个`name`值实际上唯一地确定了`address`值，则`address`实际上在功能上依赖于`name`。要告诉MySQL接受查询，可以使用`ANY_VALUE()`函数：

```sql
SELECT name, ANY_VALUE(address), MAX(age) FROM t GROUP BY name;
```

或者，禁用`ONLY_FULL_GROUP_BY`。

如果查询具有聚合函数但没有GROUP BY子句，则在启用`ONLY_FULL_GROUP_BY`的情况下，`SELECT`列、`HAVING`条件或`ORDER BY`列中不能有非聚合列：

```sql
mysql> SELECT name, MAX(age) FROM t;
ERROR 1140 (42000): In aggregated query without GROUP BY, expression
#1 of SELECT list contains nonaggregated column 'mydb.t.name'; this
is incompatible with sql_mode=only_full_group_by
```

如果没有GROUP BY，则只有一组数据，并且不确定为该组选择哪个`name`值。在这里，也可以使用`ANY_VALUE()`，如果MySQL选择哪个`name`值无关紧要：

```sql
SELECT ANY_VALUE(name), MAX(age) FROM t;
```
