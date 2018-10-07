---
title: "mysql group by取第一条数据"
date: 2018-04-01 02:04:13
tags: 
	- mysql
categories: 
	- mysql
---

# mysql：取group by第一条数据

## 示例数据（表enterprise_info）
```mysql
SELECT * FROM enterprise_info;
```
id | name | gdp | update_time
-- | ---- | --- | -----------
 1 | 讯腾 | 240 | 2018/3/31 2:49
 2 | 度百 | 120 | 2018/3/24 2:50
 3 | 里阿 | 340 | 2018/3/25 0:00
 4 | 里阿 | 460 | 2018/3/28 0:00
 5 | 里阿 | 530 | 2018/2/25 0:00
 6 | 里阿 | 450 | 2018/1/25 0:00
 7 | 讯腾 | 320 | 2018/1/12 0:00
 8 | 讯腾 | 435 | 2018/3/24 0:00
 9 | 讯腾 | 564 | 2018/2/15 0:00
 10 | 度百 | 435 | 2018/3/31 0:00
 11 | 度百 | 675 | 2018/2/22 0:00
 12 | 度百 | 232 | 2018/3/30 0:00

数据说明：
- 共有三个name：讯腾、度百、里阿
- id与update_time不是正相关的关系

## 目标
获取三个name的最新数据，并插入到新表(n_enterprise_info)中

### 步骤一：创建新表
```mysql
CREATE TABLE n_enterprise_info LIKE enterprise_info;
```

### 步骤二：查询并插入结果
```mysql
INSERT INTO n_enterprise_info(name, gdp, update_time)  
	SELECT en.name, en.gdp, en.update_time FROM enterprise_info en 
	JOIN (
		SELECT name, max(update_time) up_time 
		FROM enterprise_info 
		GROUP BY name ORDER BY update_time DESC 
		) tmp 
	ON en.name = tmp.name AND en.update_time = tmp.up_time;
```

### 结果示例
```mysql
SELECT * FROM n_enterprise_info;
```

 id | name | gdp | update_time
 -- | ---- | --- | -----------
 1 | 讯腾 | 240 | 2018/3/31 2:49
 2 | 里阿 | 460 | 2018/3/28 0:00
 3 | 度百 | 435 | 2018/3/31 0:00
