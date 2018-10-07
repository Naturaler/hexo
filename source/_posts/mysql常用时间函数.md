---
title: mysql常用时间函数
date: 2018-04-01 15:25:52
tags: 
	- mysql
categories: 
	- mysql
---

# Mysql常用的时间处理函数

**MySQL函数对大小写不敏感**
姊妹篇：[spark-sql常用的时间处理函数](https://naturaler.github.io/2018/04/01/sparl-sql常用时间函数/)

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
select substring(date_sub(now(), interval 1 day), 1, 10); // 2018-03-05
```
- firstDayOfLastMonth
```
select concat(substring(date_sub(now(), interval 1 month), 1, 7), '-01 00:00:00'); // 2018-02-01 00:00:00
```
- firstDayOfThisMonth
```
select concat(substring(now(), 1, 7), '-01 00:00:00'); // 2018-03-01 00:00:00
```
- strToDate
```
select str_to_date('20:49:23 03/06/2018', '%s:%i:%H %m/%d/%Y'); // 2018-03-06 23:49:20
```
- nowOnYear
```
select year(now()); // 2018
```
- 时间运算
```
select date_add('2018-03-03 20:30:34', INTERVAL 1 DAY); // 2018-03-04 20:30:34（加一天）
select date_sub('2018-03-03 20:30:34', INTERVAL 1 DAY); // 2018-03-02 20:30:34（减一天）
date_add（或date_sub）的函数格式：date_add(var1, INTERVAL var2 var3)
参数有三个（INTERVAL是关键字）：
	- var1：时间值，可以是date或datetime类型
	- var2：数值，可以是正整数或负整数（如-1）
	- var3：单位，有
		- MICROSECOND（毫秒）
		- SECOND（秒）
		- MINUTE（分钟）
		- HOUR（小时）
		- DAY（天）
		- WEEK（周）
		- MONTH（月）
		- QUARTER（季度）
		- YEAR（年）
		- 还有其他组合格式 [具体可参考这里](https://dev.mysql.com/doc/refman/5.5/en/date-and-time-functions.html#function_date-add) 
```

### 附：MySQL的时间格式
```
- %Y：年份，如2018
- %m：月份，如03
- %d：日期，如06
- %H：小时，如20
- %i：分钟，如30
- %s：秒数，如45
```
