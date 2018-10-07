---
title: java时间格式化相关事项
date: 2018-04-01 14:54:45
tags: java
categories:
	- java
---

# 记Java时间格式化的注意事项

今天在尝试将格式不明的时间转换为标准的时间格式时，遇到了几个问题，在此记录下

【需求：将格式不明的时间转换为标准格式，如将“HH:mm yyyy/MM/dd”、“HH:mm:ss”、“yyyy/MM/dd”等转换为“yyyy-MM-dd HH:mm:ss”。缺失部分用o补齐】

## 注意点1：simpledateformat
```java
SimpleDateFormat format = new SimpleDateFormat("yyyy/MM/dd");
// 设置以严格的格式解析string
format.setLenient(false);
try {
    Date date = format.parse("2018/03/28 23:23:12");	// 不会报错

    SimpleDateFormat standardFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    System.out.println(standardFormat.format(date));
} catch (ParseException e) {
    e.printStackTrace();
}
```
输出结果：
```java
2018-03-28 00:00:00
```
很明显，结果中时分秒被忽略了。如果想精确解析未知格式的时间时，需要注意这种情况


## 注意点2：java8中的localdatetime、localdate、localtime的支持格式
采用了java8的java.time包下的时间类
```java
LocalDateTime localDateTime = LocalDateTime.parse(datetime, DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss"));    // 只支持年月日时分秒都齐全的情况，如2018/03/28 23:23:12
LocalDate localDate = LocalDate.parse(date, DateTimeFormatter.ofPattern("yyyy/MM/dd"));    // 只支持年月日，如2018/03/28
LocalTime localTime = LocalTime.parse(time, DateTimeFormatter.ofPattern("HH:mm:ss"));    // 只支持时分秒，如23:23:12
```
若想将为止的时间格式转换为标准的格式，则需要轮询并捕获异常的方式一个个尝试

## 结论
综上所述，如果想精确的转换时间，则应该采用java.time包下的时间类
小白只想到这种方案，欢迎各路大神指出错误
