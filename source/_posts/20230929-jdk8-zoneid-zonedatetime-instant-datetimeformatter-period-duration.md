---
title: JDK8 中的 ZoneId、ZoneDateTime、Instant、DateTimeFormatter、Period 和 Duration
date: 2023-09-29 17:17:17
tags:
  - java
  - jdk8
categories: Java
---
## ZoneId 时区

```java
ZoneId zoneId = ZoneId.systemDefault(); // 获取系统默认时区
System.out.println("zoneId = " + zoneId.getId()); // Asia/Shanghai
System.out.println("zoneId = " + zoneId); // Asia/Shanghai

Set<String> availableZoneIds = ZoneId.getAvailableZoneIds(); // 获取 Java 支持的全部时区 id
System.out.println("availableZoneIds = " + availableZoneIds);

ZoneId zoneId1 = ZoneId.of("Asia/Shanghai"); // 根据时区 id 获取 ZoneId 对象
```

## ZoneDateTime

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now(zoneId1); // 根据时区获取带时区的日期时间
System.out.println("zonedDateTime = " + zonedDateTime); //2023-09-07T17:19:35.243171400+08:00[Asia/Shanghai]

ZonedDateTime zonedDateTime1 = ZonedDateTime.now(Clock.systemUTC()); // 世界标准时间
System.out.println("zonedDateTime1 = " + zonedDateTime1); //zonedDateTime1 = 2023-09-07T09:22:13.064384800Z

ZonedDateTime zonedDateTime2 = ZonedDateTime.now(); // 获取系统默认时区的 ZonedDateTime
System.out.println("zonedDateTime2 = " + zonedDateTime2); // 2023-09-07T17:23:03.864487500+08:00[Asia/Shanghai]

zonedDateTime2.withYear(1);
zonedDateTime2.plusYears(1);
```

## Instant 时间线上的某个时刻（时间戳）,精确到纳秒

```java
Instant instant = Instant.now();
System.out.println("instant = " + instant.toString()); //2023-09-07T10:25:26.357256800Z

long epochSecond = instant.getEpochSecond(); // 总秒数
System.out.println("epochSecond = " + epochSecond); //1694082326

int nano = instant.getNano(); // 不够 1 秒的纳秒
System.out.println("nano = " + nano); //357256800

instant.plusSeconds(1);
instant.minusSeconds(1);
```

## DateTimeFormatter

```java
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

LocalDateTime localDateTime = LocalDateTime.now();
System.out.println("localDateTime = " + localDateTime); //2023-09-07T18:32:46.794010300

// 格式化方案 1
String format = dateTimeFormatter.format(localDateTime);
System.out.println("format = " + format); //2023-09-07 18:32:46

// 格式化方案 2
String format1 = localDateTime.format(dateTimeFormatter);
System.out.println("format1 = " + format1);

String datetimeStr = "2023-09-07 16:16:16";
LocalDateTime parse = LocalDateTime.parse(datetimeStr, dateTimeFormatter);
System.out.println("parse = " + parse);
```

## Period 计算日期间隔

```java
LocalDate localDate = LocalDate.now();
LocalDate localDate1 = LocalDate.of(2022, 12, 12);
Period period = Period.between(localDate, localDate1);

int periodYears = period.getYears();
System.out.println("periodYears = " + periodYears); // 0

int periodMonths = period.getMonths();
System.out.println("periodMonths = " + periodMonths); // -8

int periodDays = period.getDays();
System.out.println("periodDays = " + periodDays); // -26
```

## Duration 计算时间间隔

```java
LocalDateTime localDateTime1 = LocalDateTime.of(2023, 8, 6, 15, 15, 15);
LocalDateTime localDateTime2 = LocalDateTime.of(2023, 9, 7, 18, 18, 18);
Duration duration = Duration.between(localDateTime1, localDateTime2);

long days = duration.toDays();
System.out.println("days = " + days); //32

long hours = duration.toHours();
System.out.println("hours = " + hours); //771

long minutes = duration.toMinutes();
System.out.println("minutes = " + minutes); //46263
```