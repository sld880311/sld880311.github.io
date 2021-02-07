---
title: MySQL之原理分析-Count
tags:
  - MySQL
  - Count
  - 语法分析
categories:
  - [MySQL]
  - [数据库]
date: 2021-02-07 17:26:55
---
## 语法

COUNT(expr) [over_clause]

Returns a count of the number of non-NULL values of expr in the rows retrieved by a SELECT statement. The result is a BIGINT value.

If there are no matching rows, COUNT() returns 0.

This function executes as a window function if over_clause is present. over_clause is as described in Section 12.21.2, “Window Function Concepts and Syntax”.

```sql
mysql> SELECT student.student_name,COUNT(*)
       FROM student,course
       WHERE student.student_id=course.student_id
       GROUP BY student_name;
```
COUNT(*) is somewhat different in that it returns a count of the number of rows retrieved, whether or not they contain NULL values.

<!--more-->

通过官方文档说明可以得出以下结论：  

1. 返回的是`select`中`expr`不为`null`的数据
2. 返回的数据类型是`bigint`
3. 如果命中数据则返回0
4. count(*)包含值为null的行数，count(column_name)返回的是该列非空的数据
5. 一种聚合方式，用于统计结果集
6. 用法：count(*)、count(pkid)、count(cloumn_name)、count(1)

count() 是一个聚合函数，对于返回的结果集，一行行地判断，如果 count 函数的参数不是 NULL，累计值就加 1，否则不加。最后返回累计值。所以，count(*)、count(主键 id) 和 count(1) 都表示返回满足条件的结果集的总行数；而 count(字段），则表示返回满足条件的数据行里面，参数“字段”不为 NULL 的总个数。

## 具体分析

### 数据准备

```sql
CREATE TABLE `test_count` (
  `a` varchar(255) DEFAULT NULL,
  `b` varchar(255) DEFAULT NULL,
  `c` varchar(255) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert test_count VALUES(null, null, null);
insert test_count VALUES(1, 2, 3);
insert test_count VALUES(11, 112, 3);
insert test_count VALUES(1, null, 3);
```

### count(*)、count(常量）与count(column_name)的区别

```sql
MariaDB [test]> select * from test_count;
+------+------+------+
| a    | b    | c    |
+------+------+------+
| NULL | NULL | NULL |
| 1    | 2    | 3    |
| 11   | 112  | 3    |
| 1    | NULL | 3    |
+------+------+------+
4 rows in set (0.00 sec)

MariaDB [test]> select count(*), count(a),count(1) from test_count;
+----------+----------+----------+
| count(*) | count(a) | count(1) |
+----------+----------+----------+
|        4 |        3 |        4 |
+----------+----------+----------+
1 row in set (0.00 sec)
```

通过运行结果可知：**COUNT(常量) 和 COUNT(*)表示的是直接查询符合条件的数据库表的行数（可为null）。而COUNT(column_name)表示的是查询符合条件的列的值不为NULL的行数。**  
另外，COUNT(*)是 **SQL92** 定义的标准统计行数的语法，因为他是标准语法，所以MySQL数据库对他进行过很多优化。
> SQL92，是数据库的一个ANSI/ISO标准。它定义了一种语言（SQL）以及数据库的行为（事务、隔离级别等）。

### Count(*)优化

MySQL针对不同的存储引擎做了不同的优化，目前常用的存储引擎是InnoDB和MyISAM。这两种存储引擎关键的区别是**MyISAM不支持事务，MyISAM中的锁是表级锁；而InnoDB支持事务，并且支持行级锁。**

1. MyISAM优化：（前提：**单表，无其他返回数据、无where**），数据库会把表的总行单独记录（保障条件：表级锁，数据是顺序处理），COUNT(1) is only subject to the same optimization if the first column is defined as NOT NULL.
2. InnoDB优化：由于事务性，所以数据变化的可能性会增大，导致看到的数据量是不准确的；SELECT COUNT(*) statements only count rows visible to the current transaction；InnoDB在数据扫描过程中做了优化
   - MySQL 5.7.18之前使用的是`clustered index`扫描
   - MySQL 5.7.18之后会优先使用`secondary index`，如果索引没有完全在buffer pool可能会导致比较耗时，一种实现方式可以使用一种计数器来实现。（非主键索引的必要性）

### count(*)与count(1)

> InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way. There is no performance difference.
> 总结：官方说明count(*)与count(1)在性能上是没有差异，建议大家使用count(*)

### 执行统计原理

#### count(主键 id)

InnoDB 引擎会遍历整张表，把每一行的 id 值都取出来，返回给 server 层。server 层拿到 id 后，判断是不可能为空的，就按行累加。

#### count(1)

InnoDB 引擎遍历整张表，但不取值。server 层对于返回的每一行，放一个数字“1”进去，判断是不可能为空的，按行累加。单看这两个用法的差别的话，count(1) 执行得要比 count(主键 id) 快。因为从引擎返回 id 会涉及到解析数据行，以及拷贝字段值的操作。

#### count(字段)

如果这个“字段”是定义为 not null 的话，一行行地从记录里面读出这个字段，判断不能为 null，按行累加；
如果这个“字段”定义允许为 null，那么执行的时候，判断到有可能是 null，还要把值取出来再判断一下，不是 null 才累加。

#### count(*)

并不会把全部字段取出来，而是专门做了优化，不取值。count(*)按行累加。结论如下：
按照效率排序，count(字段)<count(主键id)<count(1)≈count(*)

### COUNT(DISTINCT expr,[expr...])使用

COUNT(DISTINCT expr,[expr...])

Returns a count of the number of rows with different non-NULL expr values.

If there are no matching rows, COUNT(DISTINCT) returns 0.

```sql
mysql> SELECT COUNT(DISTINCT results) FROM student;
```
In MySQL, you can obtain the number of distinct expression combinations that do not contain NULL by giving a list of expressions. In standard SQL, you would have to do a concatenation of all expressions inside COUNT(DISTINCT ...).

## 其他

### 阿里规范

1. 【强制】不要使用count(列名)或count(常量)来替代count(*)，count(*)是SQL92定义的标准统计行数的语法，与数据库无关，与NULL和非NULL无关。
   > 说明：count(*)会统计值为NULL的行，而count(列名)不会统计此列值为NULL的行。
2. 【强制】count(distinct column) 计算该列除NULL外的不重复行数。
   >注意，count(distinct column1, column2)，如果其中一列全为NULL，那么即使另一列有不同的值，也返回为0。
3. 【强制】当某一列的值全为NULL时，count(column)的返回结果为0，但sum(column)的返回结果为NULL，因此使用sum()时需注意避免NPE问题。
   >正例：可以使用如下方式避免sum的NPE问题：SELECT IFNULL(SUM(column), 0) FROM table;


## 参考

1. [简简单单SELECT COUNT语句，竟被面试官吊起来锤](https://www.toutiao.com/i6790650338174042632/)
2. [MySQL中文官方文档](https://www.mysqlzh.com/)
3. [MySQL英文官方文档](https://dev.mysql.com/doc/)
4. [MySQL Count官方语法](https://dev.mysql.com/doc/refman/5.7/en/group-by-functions.html#function_count)
