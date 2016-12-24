title：SQL基本语法

SQL 是用于访问和处理数据库的标准的计算机语言。SQL对大小写不敏感，每条SQL 语句的末端使用分号。
一些最重要的 SQL 命令的作用：
* SELECT：从数据库中提取数据
* UPDATE：更新数据库中的数据
* DELETE：从数据库中删除数据
* INSERT INTO：向数据库中插入新数据
* CREATE DATABASE：创建新数据库
* ALTER DATABASE：修改数据库
* CREATE TABLE：创建新表
* ALTER TABLE：变更（改变）数据库表
* DROP TABLE：删除表
* CREATE INDEX：创建索引（搜索键）
* DROP INDEX：删除索引

## SELECT语句
SELECT语句用于从数据库中选取数据。结果被存储在一个结果表中，称为结果集。其语法为：
```
# 从数据表table_name中选取列column_name1和column_name2
SELECT column_name1,column_name2 FROM table_name;

# 从数据表table_name中选取所有列
SELECT * FROM table_name;

```
借助select语句可以为表名称或列名称指定别名。基本上，创建别名是为了让列名称的可读性更强。其语法为：

```
# 列的别名
SELECT column_name AS alias_name
FROM table_name;
# 表的别名
SELECT column_name(s)
FROM table_name AS alias_name;
```

## SELECT DISTINCT语句
SELECT DISTINCT语句用于返回唯一不同的值。语法为：
```
# 从数据表table_name的列column_name1和column_name2选出不同的值
SELECT DISTINCT column_name1,column_name2
FROM table_name;
```
## WHERE语句
WHERE语句用于提取那些满足指定标准的记录。

```
SELECT column_name1,column_name2
FROM table_name
WHERE column_name operator value;
```
这里的value如果是字符型的，要使用''；operator为运算符，常用的运算符有：=(等于)，<>(不等于)，>(大于)，<(小于)，>=(大于等于)，<=(小于等于)，BETWEEN(在某个范围内)，LIKE(搜索某种模式)和IN(指定针对某个列的多个可能值) 。这些运算符还可以通过NOT，AND和OR运算符连接。
### BETWEEN操作符
BETWEEN操作符用于选取介于两个值之间的数据范围内的值。这些值可以是数值、文本或者日期。其语法为：
```
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```
### LIKE操作符
LIKE操作符用于搜索列中的指定模式。其语法为：

```
SELECT column_name(s)
FROM table_name
WHERE column_name LIKE pattern;
```
LIKE操作还涉及到字符的匹配，包括通配符和正则表达式。
#### 通配符
通配符可用于替代字符串中的任何其他字符。常与与LIKE操作符一起使用，搜索表中的数据。常用的通配符有：
* %:替代 0 个或多个字符
* _:替代一个字符
* [charlist]:字符列中的任何单一字符
  * [^charlist]或[!charlist]:不在字符列中的

#### 正则表达式
MySQL中使用REGEXP或NOT REGEXP运算符(或RLIKE和NOT RLIKE)来操作正则表达式。其语法为：

```
SELECT * FROM Websites
WHERE name REGEXP pattern;
```
### IN操作符
IN操作符允许您在 WHERE 子句中规定多个值。其语法为：

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1,value2,...);
```

### AND和OR运算符
AND和OR运算符用于基于一个以上的条件对记录进行过滤。AND运算符显示同时满足多个条件的记录。OR运算符显示的记录只要满足其中一个条件即可。


## ORDER BY关键字
ORDER BY 关键字用于对结果集按照一个列或者多个列进行排序。默认按照升序对记录进行排序。如果需要按照降序对记录进行排序，使用DESC关键字。其语法为：

```
# 从数据表table_name中选取列column_name1和column_name2
# 将结果先按column_name1升序或降序，再按column_name2升序或降序排列
SELECT column_name1,column_name2
FROM table_name
ORDER BY column_name1 ASC|DESC, column_name2 ASC|DESC;
```
## LIMIT关键字
LIMIT关键字用于规定要返回的记录的数目。其语法为：

```
# 提取数据表table_name中选取列column_name(s)前number行的记录
SELECT column_name(s)
FROM table_name
LIMIT number;
```

\- - - - - -

参考文章：

[SQL 教程](http://www.runoob.com/sql/sql-tutorial.html)
