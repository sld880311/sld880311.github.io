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
