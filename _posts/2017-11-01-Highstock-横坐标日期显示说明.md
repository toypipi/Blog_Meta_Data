---
title: Highstock 横坐标日期显示说明
categories:
  - 技术
tags:
  - Highstock
  - 日期
  - UTC
date: 2017-11-01 17:00:11
---

---
在使用 Highstock 制图时，后端将数据库中存储的日期直接转换为毫秒值发送到前端，但是前端根据接收到的数据显示出现了异常，本来查询到的是一天的数据，但是横坐标的日期却显示了两天的时间。
经过检索，发现后端发送给前端的数据需要转化为 UTC 格式下的毫秒值才不会出现错位。

java下取utc下的毫秒值：
```java
Date date = null;
DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
date = df.parse("2017-11-02");
Calendar cal = Calendar.getInstance(TimeZone.getDefault());//TimeZone.getDefault()获取主机的默认 TimeZone，即时区偏移量。
cal.setTime(date);
list.add(date.getTime()+cal.getTimeZone().getRawOffset());//cal.getTimeZone().getRawOffset()是相对utc的偏移量
//date.getTime()+cal.getTimeZone().getRawOffset()就是2012-11-26对应的utc下的毫秒值非UTC格式毫秒
```
**参考文档：**
[关于highstock横坐标的一些的一些说明(1)使用UTC时间](http://www.cnblogs.com/onetwo/p/6050829.html)