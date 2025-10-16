---
title: Android：DatePickerDialog
date: 2017-08-20 17:17:17
tags:
  - android
categories: Android
---
DatePickerDialog 用来设置日期。基本用法：

```java
 Calendar mCalendar = Calendar.getInstance();
 //格式化日期
 SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
 
 DatePickerDialog pickerDialog = new DatePickerDialog(this, new DatePickerDialog.OnDateSetListener() {

            @Override
            public void onDateSet(DatePicker arg0, int year, int month, int day) {
                mCalendar.set(year, month, day);//将点击获得的年月日获取到calendar中。
                Toast.makeText(this, format.format(mCalendar.getTime()),Toast.LENGTH_LONG).show(); 
            }
        },mCalendar.get(Calendar.YEAR), mCalendar.get(Calendar.MONTH), mCalendar.get(Calendar.DAY_OF_MONTH));
		//设置最大可选日期
        pickerDialog.getDatePicker().setMaxDate((new Date()).getTime());
        pickerDialog.show();
```

把上述代码放在 `view` 的 `click` 时间监听中便可触发，并且默认选择的日期是当前日期。

但在实际应用中，我们可能需要默认显示的是传来的值，如生日等，如何做？

上面代码中，在实例化 `DatePickerDialog` 时传入的 `mCalendar.get(Calendar.YEAR), mCalendar.get(Calendar.MONTH), mCalendar.get(Calendar.DAY_OF_MONTH)` ，便是用来设置默认选择的日期，我们可以把传来的日期转化为 `Calendar` 对象即可，代码如下：

```java
 Calendar mCalendar = Calendar.getInstance();
 //格式化日期
 SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
 
 String str = "1988-11-11"; //这是可以是传来的日期，也可从 `view`获取
 if (str != null){
            try {
                Date date = format.parse(str);
                mCalendar.setTime(date);
            } catch (ParseException e) {
                e.printStackTrace();
            }
        }
 
 DatePickerDialog pickerDialog = new DatePickerDialog(this, new DatePickerDialog.OnDateSetListener() {

            @Override
            public void onDateSet(DatePicker arg0, int year, int month, int day) {
                mCalendar.set(year, month, day);//将点击获得的年月日获取到calendar中。
                Toast.makeText(this, format.format(mCalendar.getTime()),Toast.LENGTH_LONG).show(); 
            }
        },mCalendar.get(Calendar.YEAR), mCalendar.get(Calendar.MONTH), mCalendar.get(Calendar.DAY_OF_MONTH));
		//设置最大可选日期
        pickerDialog.getDatePicker().setMaxDate((new Date()).getTime());
        pickerDialog.show();
```

打开日期对话框时，默认选择的便是 `1988-11-11`。