---
title: MYSQL之数据类型与Schema
tags:
  - MySQL
  - 数据类型
categories:
  - [数据库]
  - [MySQL]
date: 2021-02-08 13:52:44
---
## 选择优化的数据类型

1. 更小的通常更好
2. 简单就好
3. 尽量避免使用NULL

### 数值类型

在mysql中的数值类型主要包括：整数和实数。
1. 在mysql中可以指定整数类型的宽度，但是该宽度只是用来显示可展示的长度，不能作为限制类型的实际长度
2. mysql中支持精确类型的实数和非精确类型的实数，需要根据实际场景进行选择，也可以根据业务场景转换成BIGINT进行存储
具体类型如下表所示：
<!--more-->

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#bbb;border-spacing:0;}
.tg td{background-color:#E0FFEB;border-color:#bbb;border-style:solid;border-width:1px;color:#594F4F;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#9DE0AD;border-color:#bbb;border-style:solid;border-width:1px;color:#493F3F;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg" style="undefined;table-layout: fixed; width: 800px">
<colgroup>
<col style="width: 106px">
<col style="width: 134px">
<col style="width: 261px">
<col style="width: 215px">
<col style="width: 84px">
</colgroup>
<thead>
  <tr>
    <th class="tg-0lax"> 类型 </th>
    <th class="tg-0lax"> 大小 </th>
    <th class="tg-0lax"> 范围（有符号） </th>
    <th class="tg-0lax"> 范围（无符号） </th>
    <th class="tg-0lax"> 用途 </th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax"> TINYINT </td>
    <td class="tg-0lax"> 1 byte </td>
    <td class="tg-0lax"> (-128，127) </td>
    <td class="tg-0lax"> (0，255) </td>
    <td class="tg-0lax"> 小整数值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> SMALLINT </td>
    <td class="tg-0lax"> 2 bytes </td>
    <td class="tg-0lax"> (-32 768，32 767) </td>
    <td class="tg-0lax"> (0，65 535) </td>
    <td class="tg-0lax"> 大整数值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> MEDIUMINT </td>
    <td class="tg-0lax"> 3  bytes </td>
    <td class="tg-0lax"> (-8 388 608，8 388 607) </td>
    <td class="tg-0lax"> (0，16 777 215) </td>
    <td class="tg-0lax"> 大整数值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> INT或INTEGER </td>
    <td class="tg-0lax"> 4  bytes </td>
    <td class="tg-0lax"> (-2 147 483 648，2 147 483 647) </td>
    <td class="tg-0lax"> (0，4 294 967 295) </td>
    <td class="tg-0lax"> 大整数值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> BIGINT </td>
    <td class="tg-0lax"> 8  bytes </td>
    <td class="tg-0lax"> (-9,223,372,036,854,775,808，9 223 372 036 854 775 807) </td>
    <td class="tg-0lax"> (0，18 446 744 073 709 551 615) </td>
    <td class="tg-0lax"> 极大整数值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> FLOAT </td>
    <td class="tg-0lax"> 4  bytes </td>
    <td class="tg-0lax"> (-3.402 823 466 E+38，-1.175 494 351 E-38)，<br>0，<br>(1.175 494 351 E-38，3.402 823 466 351 E+38)  </td>
    <td class="tg-0lax"> 0，<br>(1.175 494 351 E-38，3.402 823 466 E+38) </td>
    <td class="tg-0lax"> 单精度<br>浮点数值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> DOUBLE </td>
    <td class="tg-0lax"> 8  bytes </td>
    <td class="tg-0lax"> (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，<br>0，<br>(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) </td>
    <td class="tg-0lax"> 0，<br>(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) </td>
    <td class="tg-0lax"> 双精度<br>浮点数值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> DECIMAL </td>
    <td class="tg-0lax"> 对DECIMAL(M,D) ，如果M&gt;D，M+2<br>否则为D+2 </td>
    <td class="tg-0lax"> 依赖于M和D的值 </td>
    <td class="tg-0lax"> 依赖于M和D的值 </td>
    <td class="tg-0lax"> 小数值 </td>
  </tr>
</tbody>
</table>

### 字符类型

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#bbb;border-spacing:0;}
.tg td{background-color:#E0FFEB;border-color:#bbb;border-style:solid;border-width:1px;color:#594F4F;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#9DE0AD;border-color:#bbb;border-style:solid;border-width:1px;color:#493F3F;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-0lax"> 类型 </th>
    <th class="tg-0lax"> 大小 </th>
    <th class="tg-0lax"> 用途 </th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax"> CHAR </td>
    <td class="tg-0lax"> 0-255 bytes </td>
    <td class="tg-0lax"> 定长字符串 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> VARCHAR </td>
    <td class="tg-0lax"> 0-65535 bytes </td>
    <td class="tg-0lax"> 变长字符串 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> TINYBLOB </td>
    <td class="tg-0lax"> 0-255 bytes </td>
    <td class="tg-0lax"> 不超过 255 个字符的二进制字符串 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> TINYTEXT </td>
    <td class="tg-0lax"> 0-255 bytes </td>
    <td class="tg-0lax"> 短文本字符串 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> BLOB </td>
    <td class="tg-0lax"> 0-65 535 bytes </td>
    <td class="tg-0lax"> 二进制形式的长文本数据 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> TEXT </td>
    <td class="tg-0lax"> 0-65 535 bytes </td>
    <td class="tg-0lax"> 长文本数据 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> MEDIUMBLOB </td>
    <td class="tg-0lax"> 0-16 777 215 bytes </td>
    <td class="tg-0lax"> 二进制形式的中等长度文本数据 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> MEDIUMTEXT </td>
    <td class="tg-0lax"> 0-16 777 215 bytes </td>
    <td class="tg-0lax"> 中等长度文本数据 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> LONGBLOB </td>
    <td class="tg-0lax"> 0-4 294 967 295 bytes </td>
    <td class="tg-0lax"> 二进制形式的极大文本数据 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> LONGTEXT </td>
    <td class="tg-0lax"> 0-4 294 967 295 bytes </td>
    <td class="tg-0lax"> 极大文本数据 </td>
  </tr>
</tbody>
</table>

#### varchar

1. 可变长字符串，通常比定长类型节省空间。但是如果使用ROW_FORMAT=FIXED创建表则每一行都会使用定长存储
2. 需要1(<>=255)或2(超过255)个额外的字节记录字符串长度
3. InnoDB在varchar空间变化时会使用分裂页的方式处理数据，从而会产生碎片
4. 使用场景：字符串列的最大长度比平均长度大很多，列的更新很少

#### char

1. 定长
2. 不易产生碎片
3. 自动删除右侧的空格
4. 适合存储MD5这种定长的数据

#### 枚举类型enum

1. 把一些不重复的字符串存储成一个预定义的集合
2. 在存储枚举类型时，数据库会根据数据进行压缩到一个或两个字节中
3. mysql内部会将每个值在列表中的位置保存为整数，并且在表的.frm文件中保存“整数-字符串”映射关系的查找表
4. 缺点：字符串固定，只能通过alter table进行修改；在不同类型之间关联会出现性能下降（由于有转换的过程）

#### 其他

1. char(n) 和 varchar(n) 中括号中 n 代表字符的个数，并不代表字节个数，比如 CHAR(30) 就可以存储 30 个字符，根据不同的编码方式，占用的字节数不同
2. CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等方面也不同。在存储或检索过程中不进行大小写转换。
3. BINARY 和 VARBINARY 类似于 CHAR 和 VARCHAR，不同的是它们包含二进制字符串而不要非二进制字符串，存储字节码。也就是说，它们包含字节字符串而不是字符字符串。这说明它们没有字符集，并且排序和比较基于列值字节的数值值；binary使用\0进行填充而不是空格，并且检索时也不会去掉填充值
4. BLOB 是一个二进制大对象，可以容纳可变数量的数据。有 4 种 BLOB 类型：TINYBLOB、BLOB、MEDIUMBLOB 和 LONGBLOB。它们区别在于可容纳存储范围不同。
5. 有4种 TEXT 类型：TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT。对应的这 4 种 BLOB 类型，可存储的最大长度不同，可根据实际情况选择。
6. BLOB与TEXT不同的是BLOB存储二进制数据，没有排序规则或字符集；TEXT存储的是字符串，存在字符集和排序规则，注意：排序的时候只是针对每个列的最前max_sort_length字节进行处理，可以配置max_sort_length的大小或者使用order by substring(column,length)进行设置，在实际开发中尽量避免BLOB和TEXT的使用

### 时间类型

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#bbb;border-spacing:0;}
.tg td{background-color:#E0FFEB;border-color:#bbb;border-style:solid;border-width:1px;color:#594F4F;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#9DE0AD;border-color:#bbb;border-style:solid;border-width:1px;color:#493F3F;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg" style="undefined;table-layout: fixed; width: 784px">
<colgroup>
<col style="width: 99px">
<col style="width: 62px">
<col style="width: 301px">
<col style="width: 182px">
<col style="width: 140px">
</colgroup>
<thead>
  <tr>
    <th class="tg-0lax"> 类型 </th>
    <th class="tg-0lax"> 大小<br>( bytes) </th>
    <th class="tg-0lax"> 范围 </th>
    <th class="tg-0lax"> 格式 </th>
    <th class="tg-0lax"> 用途 </th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax"> DATE </td>
    <td class="tg-0lax"> 3 </td>
    <td class="tg-0lax"> 1000-01-01/9999-12-31 </td>
    <td class="tg-0lax"> YYYY-MM-DD </td>
    <td class="tg-0lax"> 日期值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> TIME </td>
    <td class="tg-0lax"> 3 </td>
    <td class="tg-0lax"> '-838:59:59'/'838:59:59' </td>
    <td class="tg-0lax"> HH:MM:SS </td>
    <td class="tg-0lax"> 时间值或持续时间 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> YEAR </td>
    <td class="tg-0lax"> 1 </td>
    <td class="tg-0lax"> 1901/2155 </td>
    <td class="tg-0lax"> YYYY </td>
    <td class="tg-0lax"> 年份值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> DATETIME </td>
    <td class="tg-0lax"> 8 </td>
    <td class="tg-0lax"> 1000-01-01 00:00:00/9999-12-31 23:59:59 </td>
    <td class="tg-0lax"> YYYY-MM-DD HH:MM:SS </td>
    <td class="tg-0lax"> 混合日期和时间值 </td>
  </tr>
  <tr>
    <td class="tg-0lax"> TIMESTAMP </td>
    <td class="tg-0lax"> 4 </td>
    <td class="tg-0lax"> <br>1970-01-01 00:00:00/2038<br>结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038年1月19日 凌晨 03:14:07 </td>
    <td class="tg-0lax"> YYYYMMDD HHMMSS </td>
    <td class="tg-0lax"> 混合日期和时间值，时间戳 </td>
  </tr>
</tbody>
</table>

1. 表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。
2. 每个时间类型有一个有效值范围和一个"零"值，当指定不合法的MySQL不能表示的值时使用"零"值。
3. TIMESTAMP类型有专有的自动更新特性
4. 建议尽量使用TIMESTAMP
5. mysql支持的时间最小粒度为s，可以使用BIGINT或DOUBLE替换，或者使用mariadb数据库

## ID选择（主键的处理）

1. 在选择ID列的类型时，不经要考虑存储类型，还需要考虑mysql对这种类型的计算和比较的方式。
2. 确定之后需要在关联表中都是用同样的类型和长度，防止出现性能问题或位置错误
3. 根据实际的业务场景选择最小的数据类型

### 类型选择技巧

1. 整数类型：最好的选择，并且支持自增长
2. enum和set类型：尽量避免使用该类型
3. 字符串类型
   - 相对于整数类型，则不建议选择，不仅会浪费空间，而且会影响查询性能（MYISAM默认会对字符串进行压缩）
   -  特别注意随机字符串，比如MD5()、SHA1()或UUID()，会任意分布到很大的空间中，影响insert和select性能
      -  插入式索引的位置不定，会导致页分裂、磁盘随机访问，以及对于聚簇存储引擎产生聚簇索引碎片
      -  查询会由于相邻数据分布到不同的磁盘和内存中导致数据加载过慢
      -  随时值会导致缓存的不可用，热数据的丢失，查询效果变差
   -  UUID可以去掉-或者使用unhex函数转换成数字存储在BINARY(16)中，在使用时通过HEX进行转换；相对于SHA1具有一定的顺序性

## Schema设计中的陷阱

1. 太多的列：MySQL的存储引擎API工作时需要在服务器层和存储引擎层之间通过行缓冲格式拷贝数据，然后在服务器层将缓冲内容解码成各个列。
2. 太多的关联：单个查询最好在12个以内做关联，mysql最大限制为61个
3. 全能的枚举：避免多读使用枚举
4. 变相的枚举：可以使用枚举代替集合列
5. 非此发明的NULL：在实际业务场景中如果需要使用NULL，就去使用，不要使用其他变量进行替换，比如使用-1表示null

## 设计范式

### 分类

#### 第一范式(1st NF －列都是不可再分) 

第一范式的目标是确保每列的原子性:如果每列都是不可再分的最小数据单元（也称为最小的原子单元），则满足第一范式（1NF） 
<div align=center>

![](MYSQL之数据类型与Schema/1589106620753.png)

</div>

#### 第二范式(2nd NF－每个表只描述一件事情) 
 
首先满足第一范式，并且表中非主键列不存在对主键的部分依赖。 第二范式要求每个表只描述一件事情。 
<div align=center>

![](MYSQL之数据类型与Schema/1589106645458.png)

</div>

 
#### 第三范式(3rd NF－ 不存在对非主键列的传递依赖) 

第三范式定义是，满足第二范式，并且表中的列不存在对非主键列的传递依赖。除了主键订单编号外，顾客姓名依赖于非主键顾客编号。 
<div align=center>

![](MYSQL之数据类型与Schema/1589106668000.png)

</div>

### 范式优缺点

1. 更新相对较快
2. 修改的数据相对较少
3. 表通常会更小，所在可以缓存到内存中，执行操作相对较快
4. 很少有冗余的数据，所以很少会用到distinct或group by语句
5. 缺点是需要做关联操作，影响查询效率

### 反范式优缺点

1. 可以避免多表关联，避免过多的随机IO
2. 可以更加有效的使用索引策略

## 缓存表和汇总表（常用于报表数据存储）

## 加快alter table操作的速度

### 原理

MySQL执行大部分修改表的结构操作的方法是用新的结构创建一个空表，从旧表中查询出所有数据然后进行写入，之后删除表。（**性能低下**）

### 常规操作

1. 使用slave节点的数据进行处理，然后进行切换使用
2. 影子拷贝，然后进行重命名和删除

### 不重建表的场景

该表或删除一个列的默认值。

1. 慢：alter table *** modify column *** tinyint(3) not null default 5;
2. 快（直接修改.frm文件不涉及表数据）：alter table *** alter column *** set default 5;

### 只修改.frm文件

#### 可能不需要重建表的场景

1. 移除（不是添加）一个列的auto_increment属性
2. 增加、移除、或更改enum和set常量。如果移除的是已经有行数据用到其值的常量，查询将返回一个空字符串

#### 处理思路

为目的表创建一个新的frm文件，然后替换掉已经存在的对应文件

1. 创建相同结构的空表，然后按照要求修改对应的表信息
2. 执行flush tables with read lock，关闭所有正在使用的表，并且禁止任何表被打开
3. 更换frm文件
4. 执行unlock tables

#### 快速创建MyISAM索引

1. 提供载入数据的效率：先禁用索引，载入数据然后启用索引，对非唯一索引有效
2. 使用alter table处理（有风险）
   -  用需要的表结构创建新表，不包括索引
   -  载入数据到表中，构建.myd文件
   -  按照需要的结构创建另外一个空表，包含索引。自会创建需要的frm和myi文件
   -  获取读锁并刷新表
   -  重命名第二张表的frm和myi文件，让mysql认为是第一张表的文件
   -  释放读锁
   -  使用repair table来重建表的索引。该操作会通过排序来构建所有索引，包括唯一索引