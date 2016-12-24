title：SQL中的创建和删除

## 数据库、数据表和索引的创建
### 创建数据库
CREATE DATABASE语句用于创建数据库。其语法为：
```
### 创建数据库dbname
CREATE DATABASE dbname;
```

### 创建数据表
CREATE TABLE语句用于创建数据库中的表。表由行和列组成，每个表都必须有个表名。其语法为：
```
CREATE TABLE table_name
(
column_name1 data_type(size),
column_name2 data_type(size),
column_name3 data_type(size),
....
);
```
其中，column_name 参数规定表中列的名称；data_type 参数规定列的数据类型(如 varchar、integer、decimal和date等等；size 参数规定表中列的最大长度。

### 创建索引
CREATE INDEX语句用于在表中创建索引。
索引可以在不读取整个表的情况下使数据库应用程序更快地查找数据。用户无法看到索引，它们只能被用来加速搜索/查询。
要注意的更新一个包含索引的表需要比更新一个没有索引的表花费更多的时间，这是由于索引本身也需要更新。因此，理想的做法是仅仅在常常被搜索的列(以及表)上面创建索引。


在表上创建一个简单的索引(允许使用重复的值)，其语法为：
```
CREATE INDEX index_name
ON table_name (column_name)
```

在表上创建一个唯一的索引。不允许使用重复的值，其语法为：
```
CREATE UNIQUE INDEX index_name
ON table_name (column_name)
```
## 索引、表和数据库的撤销

### 删除索引

```
ALTER TABLE table_name DROP INDEX index_name
```

### 删除表

```
DROP TABLE table_name
```

### 删除数据库

```
DROP DATABASE database_name
```

如果我们仅仅需要删除表内的数据，但并不删除表本身，可以使用 TRUNCATE TABLE 语句：

```
TRUNCATE TABLE table_name
```

