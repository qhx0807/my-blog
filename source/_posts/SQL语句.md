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

2.
```sql
SELECT
	CASE ode.areaid
WHEN 1 THEN
	'重庆'
WHEN 2 THEN
	'南江'
WHEN 3 THEN
	'宣汉'
WHEN 4 THEN
	'邻水'
WHEN 5 THEN
	'其他'
ELSE
	'未知'
END,
 COUNT(1) 交易笔数,
 SUM(of.total) / 100 应付总金额,
 SUM(of.cash) / 100 实付总金额,
 SUM(of.coupon) / 100 优惠总金额
FROM
	t_order_fictitious of
INNER JOIN t_org_department_equipment ode ON of.equipmentno = ode.equipmentno
OR of.id = ode.equipmentno
WHERE
	of.type = 1
AND of.ispay = 1
AND of.paytime >= '2018-07-31'
AND of.paytime < '2018-08-01'
GROUP BY
	ode.areaid
```

```sql
<!-- 替换回车换行为空字符串 -->
REPLACE(REPLACE(`desc`, CHAR(10),''), CHAR(13),'')
```
