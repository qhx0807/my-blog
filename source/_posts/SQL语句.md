---
title: 一个前端收藏SQL语句
date: 2018-07-22 13:28:25
tags: [sql]
categories: sql
---

1. 查询表里重复次数最多的
```sql
SELECT userid, count( * ) AS count FROM t_order_m GROUP BY userid ORDER BY count desc
```
