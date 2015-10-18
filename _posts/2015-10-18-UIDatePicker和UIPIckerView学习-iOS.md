---
layout: post
date: 2015-10-18
title: UIDatePicker和UIPIckerView学习
categories: iOS
---

控制器类UIDatePicker封装了UIPIckerView，但它是UIControl的之类，只用于接收日期，时间和持续时长的输入，日期选择器的各列会按照指定的风格进行自动配置，我们不必关系如何配置表盘这样的底层操作

#UIDatePicker

>UIPickerView控件在给用户选择某些特定的数据时会经常使用到，它根据数据源提供的信息按照系统风格自动显示，初始化

	var datePicker = UIDatePicker(frame: CGRectMake(0, 200, 0, 0))   
	self.view.addSubview(datePicker)
	
>可以修改datePickerMode属性值来选择显示的样式

	//case Time  (e.g. 6 | 53 | PM)显示上午下午，和时分
	//case Date  (e.g. November | 15 | 2007)显示年月日
	//case DateAndTime  (e.g. Wed Nov 15 | 6 | 53 | PM)显示年月日，上午下午时分
	//case CountDownTimer  D (e.g. 1 | 53)显示小时和分钟
	datePicker.datePickerMode = UIDatePickerMode.DateAndTime
	
>可以给datePicker添加一个UIControlEvents.ValueChanged事件，检测值的改变

	datePicker.addTarget(self, action: "dateChanged:", forControlEvents: UIControlEvents.ValueChanged)
	
	//日期选择器值改变事件
	func dateChanged(sender :UIDatePicker)
	{
	    let date: NSDate = sender.date
	    let formatter = NSDateFormatter()
	    formatter.dateFormat = "yy-MMMM-dd HH:mm:s"
	    let timeStr = formatter.stringFromDate(date)
	    
	    print(date)
	    print(timeStr)
	}
	
NSDateFormatter对象，主意用来格式化时间，具体格式解释如下

YY      两位年份

YYYY    四位年份

M       一位月份，不带0

MM      两位月份

MMMM    完整的月份名，如十一月，January

d       不带0的天

dd      带0的天

h       不带0的12小时制

hh      带0的12小时制

H       没有0的24小时制

HH      两位小时

m       一位分钟

mm      两位分钟

s       一位秒

ss      两位秒

a       AM或PM

>设置选择的日期范围和间隔

	 /*可以通过设置minimumDate和maximumDate属性，来指定datePicker使用的日期范围，如果试图滚动到超
	 过这一日期的范围，表盘会回到最近的有效日期。两个方法都需要NSDate对象作参数，可以通过NSDateFormatter
	 自定义格式来将字符串转换成时间*/
	 
	 let formatter = NSDateFormatter()
	 formatter.dateFormat = "yy-MM-dd HH:mm:ss"
	 
	 //设置最小最大时间
	 datePicker.minimumDate = formatter.dateFromString("2010-01-01 00:00:00")
	 datePicker.maximumDate = formatter.dateFromString("2015-01-01 23:59:59")
	 
	 //设置时间间隔,默认间隔1分钟
	 datePicker.minuteInterval = 5