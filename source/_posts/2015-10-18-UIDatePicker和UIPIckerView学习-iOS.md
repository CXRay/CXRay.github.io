---
layout: post
date: 2015-10-18
title: UIDatePicker和UIPIckerView学习
categories: iOS
---

控制器类UIDatePicker封装了UIPIckerView，但它是UIControl的子类，只用于接收日期，时间和持续时长的输入，日期选择器的各列会按照指定的风格进行自动配置，我们不必关系如何配置表盘这样的底层操作

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
	 
	 
#UIPickerView

选择视图控制器中系统只定义了整体行为和外观，至于选择控制器显示图片还是文本，显示的具体内容是什么，这些都通过数据源协议来实现，它比UIDatePicker更通用，可以定制单列的选择器，也可定制多列的选择器，用法非常灵活

>初始化，添加到视图，并创建两个数组，添加到视图时，因为没有实现数据源协议，所以没有数据，下面我们来实现一个两列选择控制器，首先，UIViewController类必须声明要实现UIPickerView的数据源协议和代理，UIPickerViewDelegate,UIPickerViewDataSource

	//创建选择控制器
	var pickerView = UIPickerView(frame: CGRectMake(0,200,0,0))
	
	//创建两个数组
	var authorsArray = ["刘洋明","汪红军","任晓蕾"]
	var appsArray = ["租房点评","哪里逃","AllOff","iJump","数独"]
	self.view.addSubview(pickerView)
	
>其次，来设置pickerView的数据协议和代理

	//指定数据源
	pickerView.dataSource = self
	//指定代理
	pickerView.delegate = self
	
>实现pickerView的数据源协议和代理
    
	//返回多少列
	func numberOfComponentsInPickerView(pickerView: UIPickerView) -> Int {
	    return 2
	}
	
	//每一列返回多少行，这个必须实现
	func pickerView(pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
	    //第一列返回authorsArray的个数
	    if component == 0
	    {
	        return authorsArray.count
	    }
	    else if component == 1
	    {
	        return appsArray.count
	    }
	    return 0
	}
	
	//通过字符串来创建选择控制器
	func pickerView(pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
	    //第一列返回authorArray对应的值
	    if component == 0
	    {
	        return authorsArray[row]
	    }
	    //第二列返回appsArray对应的值
	    else if component == 1
	    {
	        return appsArray[row]
	    }
	    
	    return ""
	}
这样就能实现一个PickerView了

>一些常用属性和方法介绍如下

	//获取多少列，只读
	print(pickerView.numberOfComponents)
	
	//获取某一列有多少行
	print(pickerView.numberOfRowsInComponent(0))
	print(pickerView.numberOfRowsInComponent(1))
	
	//每一列的size大小，返回CGSize
	print(pickerView.rowSizeForComponent(0))
	print(pickerView.rowSizeForComponent(1))
	
	//如果通过View初始化选择控制器，可以获取对应列对应行的View，返回UIView?
	//pickerView.viewForRow(row:Int, forComponent:Int)
	
	//重新加载所有的列
	pickerView.reloadAllComponents()
	//重新加载某一列
	pickerView.reloadComponent(0)
	pickerView.reloadComponent(1)
	
	//设置选择哪一行哪一列，选中的一行内容在中间显示
	pickerView.selectRow(2, inComponent: 1, animated: true)
	pickerView.selectRow(1, inComponent: 0, animated: true)
	
	//某一列选择哪一行
	print(pickerView.selectedRowInComponent(0))
	
>通过以下方法可以知道选择的是哪一行哪一列，选项改变后触发代理

	func pickerView(pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
	    print("当前选择的是第\(component)列   第\(row)行")
	}
	
>创建带图片的PickerView

	func pickerView(pickerView: UIPickerView, viewForRow row: Int, forComponent component: Int, reusingView view: UIView?) -> UIView {
	    let view = UIView(frame: CGRectMake(0,0,200,40))
	    
	    //创建图像视图
	    let imageView = UIImageView(frame: CGRectMake(0, 0, 40, 40))
	    imageView.image = UIImage(named: "IMG_3882.JPG")
	    
	    //创建文本标签
	    let label = UILabel(frame: CGRectMake(40,0,160,40))
	    
	    //第一行返回authorArray对应的值
	    if component == 0
	    {
	        label.text = authorsArray[row]
	    }
	    //第二行返回appsArray对应的值
	    else if component == 1
	    {
	        label.text = appsArray[row]
	    }
	    
	    //添加图像
	    view.addSubview(imageView)
	    
	    //添加文本标签
	    view.addSubview(label)
	    
	    return view
	}