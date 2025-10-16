---
title: JDK8 中的 LocalDate、LocalTime 和 LocalDateTime
date: 2023-09-27 17:17:17
tags:
  - java
  - jdk8
categories: Java
---

```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
```

## LocalDate：本地日期（年 月 日 星期）

```java
//0、获取本地日期对象
LocalDate localDate = LocalDate.now();
System.out.println("localDate = " + localDate); // 2023-09-07

//1、获取日期对象中的信息
int localDateYear = localDate.getYear(); // 年
System.out.println("localDateYear = " + localDateYear); // 2023

int localDateMonth = localDate.getMonthValue(); // 月(1-12)
System.out.println("localDateMonth = " + localDateMonth); // 9

int localDateDayOfMonth = localDate.getDayOfMonth(); // 日
System.out.println("localDateDayOfMonth = " + localDateDayOfMonth); // 7

int localDateDayOfWeek = localDate.getDayOfWeek().getValue();// 星期几
System.out.println("localDateDayOfWeek = " + localDateDayOfWeek); // 4

int localDateDayOfYear = localDate.getDayOfYear(); // 一年中的第几天
System.out.println("localDateDayOfYear = " + localDateDayOfYear); // 250

//2、修改信息 withYear() withMonth() withDayOfMonth() withDayOfYear()
LocalDate localDate1 = localDate.withYear(2035);
System.out.println("localDate1 = " + localDate1); // 2035-09-07
System.out.println("localDate = " + localDate); // 2023-09-07

LocalDate localDate2 = localDate.withMonth(10);
System.out.println("localDate2 = " + localDate2); // 2023-10-07
System.out.println("localDate = " + localDate); // 2023-09-07

LocalDate localDate3 = localDate.withDayOfMonth(17);
System.out.println("localDate3 = " + localDate3); // 023-09-17
System.out.println("localDate = " + localDate); // 2023-09-07

LocalDate localDate4 = localDate.withDayOfYear(360);
System.out.println("localDate4 = " + localDate4); // 2023-12-26
System.out.println("localDate = " + localDate); // 2023-09-07

//3、把某个信息加多少 plusYears() plusMonths() plusDays() plusWeeks()
LocalDate localDate5 = localDate.plusYears(1); // 增加 1 年
System.out.println("localDate5 = " + localDate5); // 2024-09-07

LocalDate localDate6 = localDate.plusMonths(12); // 增加 12 月
System.out.println("localDate6 = " + localDate6);  // 2024-09-07

LocalDate localDate7 = localDate.plusDays(366); // 增加 366 天
System.out.println("localDate7 = " + localDate7); // 2024-09-07

LocalDate localDate8 = localDate.plusWeeks(1); // 增加一周
System.out.println("localDate8 = " + localDate8); // 2023-09-14
System.out.println("localDate = " + localDate); // 2023-09-07

//4、把某个信息减多少 minusYears() minusMonths() minusDays() minusWeeks()
LocalDate localDate9 = localDate.minusYears(1); // 减去 1 年
System.out.println("localDate9 = " + localDate9); // 2022-09-07

LocalDate localDate10 = localDate.minusMonths(12); // 减去 12 月
System.out.println("localDate10 = " + localDate10); // 2022-09-07

LocalDate localDate11 = localDate.minusDays(365); // 减去 365 天
System.out.println("localDate11 = " + localDate11); // 2022-09-07

LocalDate localDate12 = localDate.minusWeeks(1); // 减去 1 周
System.out.println("localDate12 = " + localDate12); // 2023-08-31

//5、获取特定日期的 LocalDate 对象
LocalDate localDate13 = LocalDate.of(2035, 9, 7);
System.out.println("localDate13 = " + localDate13);  // 2035-09-07

//6、判断两个日期是否相等，在前还是在后 equals() isBefore() isAfter()
System.out.println("localDate13.equals(localDate) = " + localDate13.equals(localDate)); // false
System.out.println("localDate13.isBefore(localDate) = " + localDate13.isBefore(localDate)); // false
System.out.println("localDate13.isAfter(localDate) = " + localDate13.isAfter(localDate)); // true
```

## LocalTime：本地时间（时 分 秒 纳秒）

```java
//0、获取本地日期对象
LocalTime localTime = LocalTime.now();
System.out.println("localTime = " + localTime); //16:14:01.281273700

// 1、获取日期对象中的信息
int localTimeHour = localTime.getHour(); // 时
System.out.println("localTimeHour = " + localTimeHour); // 16

int localTimeMinute = localTime.getMinute(); // 分
System.out.println("localTimeMinute = " + localTimeMinute); // 14

int localTimeSecond = localTime.getSecond(); // 秒
System.out.println("localTimeSecond = " + localTimeSecond); // 1

int localTimeNano = localTime.getNano(); // 纳秒
System.out.println("localTimeNano = " + localTimeNano); // 281273700

//2、修改信息 withHour() withMinute() withSecond() withNano()
LocalTime localTime1 = localTime.withHour(1);
LocalTime localTime2 = localTime.withMinute(1);
LocalTime localTime3 = localTime.withSecond(1);
LocalTime localTime4 = localTime.withNano(1000);

//3、把某个信息加多少 plusHours() plusMinutes() plusSeconds() plusNanos()
LocalTime localTime5 = localTime.plusHours(1);
LocalTime localTime6 = localTime.plusMinutes(1);
LocalTime localTime7 = localTime.plusSeconds(1);
LocalTime localTime8 = localTime.plusNanos(1000);

//4、把某个信息减多少 minusHours() minusMinutes() minusSeconds() minusNanos()
LocalTime localTime9 = localTime.minusHours(1);
LocalTime localTime10 = localTime.minusMinutes(1);
LocalTime localTime11 = localTime.minusSeconds(1);
LocalTime localTime12 = localTime.minusNanos(1000);

//5、获取特定时间的 LocalTime 对象
LocalTime localTime13 = LocalTime.of(22, 22, 22);
System.out.println("localTime13 = " + localTime13); // 22:22:22

//6、判断两个时间是否相等，在前还是在后 equals() isBefore() isAfter()
System.out.println("localTime13.equals(localTime) = " + localTime13.equals(localTime)); // false
System.out.println("localTime13.isBefore(localTime) = " + localTime13.isBefore(localTime)); // false
System.out.println("localTime13.isAfter(localTime) = " + localTime13.isAfter(localTime)); // true
```

## LocalDateTime：本地日期、时间（年 月 日 星期 时 分 秒 纳秒）

```java
LocalDateTime localDateTime = LocalDateTime.now();
System.out.println("localDateTime = " + localDateTime);
```

`LocalDate` 和 `LocalTime` 有的 `LocalDateTime` 都有！！！

```java
LocalDate localDate14 = localDateTime.toLocalDate();
LocalTime localTime14 = localDateTime.toLocalTime();
```

[如何才能在Java中优雅的操纵时间？](https://mp.weixin.qq.com/s/J-pmgYFRBXIABSfBB_Ax2w)