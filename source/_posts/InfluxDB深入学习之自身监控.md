---
title: InfluxDB深入学习之自身监控
tags:
  - InfluxDB
categories:
  - ["数据库"]
  - ["InfluxDB"]
date: 2021-03-03 14:20:37
---

## 常用监控脚本

### series

Series会被索引且存在内存中，如果量太大会对资源造成过多损耗，且查询效率也得不到保障。 

```bash
influx -database 'telegraf' -execute 'show series' -format 'csv'|wc -l
```

或者使用如下sql：

```sql
SELECT * FROM "database" WHERE time > now() - 5m and "database"='telegraf' order by time desc
```

### tag values

```bash
influx -database 'telegraf' -execute 'SHOW TAG VALUES FROM cpu WITH KEY = cpu' -format 'csv'|wc -l
```

### 每秒写数据量

```bash
influx -execute 'select derivative(pointReq, 1s) from "write" where time > now() - 5m' -database '_internal' -precision 'rfc3339'
```

<!--more-->

## 常用命令

### explain

```sql
explain select * from cpu where time > now() - 1m
QUERY PLAN
----------
EXPRESSION: <nil>
AUXILIARY FIELDS: cpu::tag, host::tag, usage_active::float, usage_guest::float, usage_guest_nice::float, usage_idle::float, usage_iowait::float, usage_irq::float, usage_nice::float, usage_softirq::float, usage_steal::float, usage_system::float, usage_user::float
NUMBER OF SHARDS: 1
NUMBER OF SERIES: 57
CACHED VALUES: 3135
NUMBER OF FILES: 0
NUMBER OF BLOCKS: 0
SIZE OF BLOCKS: 0
```

### explain analyze

```sql
explain analyze select * from cpu where time > now() - 30d
EXPLAIN ANALYZE
---------------
.
└── select
    ├── execution_time: 230.982833ms
    ├── planning_time: 8.062578ms
    ├── total_time: 239.045411ms
    └── build_cursor
        ├── labels
        │   └── statement: SELECT cpu::tag, host::tag, usage_active::float, usage_guest::float, usage_guest_nice::float, usage_idle::float, usage_iowait::float, usage_irq::float, usage_nice::float, usage_softirq::float, usage_steal::float, usage_system::float, usage_user::float FROM telegraf.autogen.cpu
        └── iterator_scanner
            ├── labels
            │   └── auxiliary_fields: cpu::tag, host::tag, usage_active::float, usage_guest::float, usage_guest_nice::float, usage_idle::float, usage_iowait::float, usage_irq::float, usage_nice::float, usage_softirq::float, usage_steal::float, usage_system::float, usage_user::float
            └── create_iterator
                ├── labels
                │   ├── measurement: cpu
                │   └── shard_id: 2
                ├── cursors_ref: 0
                ├── cursors_aux: 627
                ├── cursors_cond: 0
                ├── float_blocks_decoded: 0
                ├── float_blocks_size_bytes: 0
                ├── integer_blocks_decoded: 0
                ├── integer_blocks_size_bytes: 0
                ├── unsigned_blocks_decoded: 0
                ├── unsigned_blocks_size_bytes: 0
                ├── string_blocks_decoded: 0
                ├── string_blocks_size_bytes: 0
                ├── boolean_blocks_decoded: 0
                ├── boolean_blocks_size_bytes: 0
                └── planning_time: 7.428681ms
```

> Note: 由于EXPLAIN ANALYZE忽略查询结果的输出，所以不包括转换为JSON或CSV的成本

#### execution_time（执行查询耗时）

1. reading the time series data
2. performing operations as data flows through iterators
3. draining processed data from iterators
4. 不包括：转换为JSON或CSV的耗时

#### planning_time（计划查询耗时）

资源消耗和时间消耗与查询的复杂性相关；比如，series keys会影响查询速度和内存消耗。首先确认时间范围和shards，然后each shard and each measurement, InfluxDB performs the following steps:

1. Select matching series keys from the index, filtered by tag predicates in the WHERE clause.
2. Group filtered series keys into tag sets based on the GROUP BY dimensions.
3. Enumerate each tag set and create a cursor and iterator for each series key.
4. Merge iterators and return the merged result to the query executor.

#### iterator type

1. **create_iterator：** 本地数据库，──a complex composition of nested iterators combined and merged to produce the final query output.
2. **remote_iterator：**(InfluxDB Enterprise only)  在远程完成

#### cursor type

虽然每种游标类型有相同的数据结构和CPU、IO成本，但每种游标的构造原因不同，并在最终的结果中分开。

1. **cursor_ref**: 聚合函数的投影游标，比如`last()` or `mean()`.
2. **cursor_aux**: 用于简单表达式计算的辅助游标
3. **cursor_cond**: 执行where中的fields创建的游标

#### block types

EXPLAIN ANALYZE separates storage block types, and reports the total number of blocks decoded and their size (in bytes) on disk. The following block types are supported:

1. float | 64-bit IEEE-754 floating-point number
2. integer | 64-bit signed integer | | unsigned | 64-bit unsigned integer
3. boolean | 1-bit, LSB encoded
4. string | UTF-8 string

### show retention policies

```sql
> show retention policies
name    duration shardGroupDuration replicaN default
----    -------- ------------------ -------- -------
autogen 0s       168h0m0s           1        true
```

### show shards

```sql
name: telegraf
id database retention_policy shard_group start_time           end_time             expiry_time          owners
-- -------- ---------------- ----------- ----------           --------             -----------          ------
2  telegraf autogen          2           2021-03-08T00:00:00Z 2021-03-15T00:00:00Z 2021-03-15T00:00:00Z 

name: _internal
id database  retention_policy shard_group start_time           end_time             expiry_time          owners
-- --------  ---------------- ----------- ----------           --------             -----------          ------
1  _internal monitor          1           2021-03-10T00:00:00Z 2021-03-11T00:00:00Z 2021-03-18T00:00:00Z
```

## 参考

1. [explain](https://docs.influxdata.com/influxdb/v1.7/query_language/spec/#explain)