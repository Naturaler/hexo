---
title: sparl-sql常用时间函数
date: 2018-04-01 15:26:47
tags: 
	- spark-sql
categories: 
	- spark
---

# Spark-SQL常用的时间处理函数

**Spark-SQL函数对大小写不敏感**
姊妹篇：[Mysql常用时间处理函数](https://naturaler.github.io/2018/04/01/mysql常用时间函数/)

- year
```
select year('2018-03-03 20:30:34'); // 2018
```
- month
```
select month('2018-03-03 20:30:34'); // 3
```
- weekOfYear
```
select weekOfYear('2018-03-03 20:30:34'); // 9
```
- dayOfYear
```
select dayOfYear('2018-03-03 20:30:34'); // 62
```
- unixTimestamp
```
select unix_timestamp('2018-03-03 20:30:34'); // 1520080234
```
- parseToDay
```
select substring(now(), 1, 10); // 2018-03-06
```
- yesterday
```
select substring(date_sub(now(), 1), 1, 10); // 2018-03-05
```
- firstDayOfLastMonth
```
select concat(substring(add_months(now(), -1), 1, 7), '-01 00:00:00'); // 2018-02-01 00:00:00【与mysql不同】
```
- firstDayOfThisMonth
```
select concat(substring(now(), 1, 7), '-01 00:00:00'); // 2018-03-01 00:00:00
```
- strToDate
```
select cast(unix_timestamp('03/13/2018 01:59:20', 'MM/dd/yyyy mm:ss:HH') as timestamp); // 2018-03-13 20:01:59【与mysql不同】
```
- nowOnYear
```
select year(now()); // 2018
```
- 时间运算
```
select date_add(now(), 1); // 2018-03-14【加一天】
select date_sub(now(), 1); // 2018-03-12【减一天】
select add_months(now(), 1); // 2018-04-13【加一月】
select add_months(now(), -1); // 2018-02-13【减一月】
```

### 附：Spark-SQL的时间格式【与java一致】
```
- yyyy：年份，如2018
- MM：月份，如03
- dd：日期，如06
- HH：小时，如20
- mm：分钟，如30
- ss：秒数，如45
```
