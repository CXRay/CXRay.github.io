---
layout: post
date: 2015-10-10
title: UISwitch & UIStepper学习
categories: Swift
---

开关和进步控件东西的东西不多

>创建开关,添加到视图
	
	//创建开关控件，虽然设置了宽度和高度，但会被忽略
	var switchView = UISwitch(frame: CGRectMake(20, 100, 60, 40))
	 
	self.view.addSubview(switchView)
	
>开关的属性
	
	//开关默认是关闭的，可以设置开关的on属性来开启和关闭开关
	switchView.on = true
	//或
	switchView.setOn(true, animated: true)
	
>检测开关的切换

	//可以给开关添加UIControlEvents.ValueChanged事件，检测开关的切换
	switchView.addTarget(self, action: "switchChange:", forControlEvents: UIControlEvents.ValueChanged)

	func switchChange(sender :AnyObject)
	{
		let newSwitch = sender as! UISwitch
		print(newSwitch.on)
	}
	
>创建进步控件，添加到视图

	//创建进步控件
	var stepperView = UIStepper(frame: CGRectMake(200, 100, 80, 40))
    
	self.view.addSubview(stepperView)
	
>进步控件的属性

	//设置最小值，默认是0.0.
	stepperView.minimumValue = 60
	//设置最大值，默认是100
	stepperView.maximumValue = 80
	//设置当前值
	stepperView.value = 70
	//设置递增或递减的值，默认是1.0

>检测进步控件的变化

	stepperView.addTarget(self, action: "stepChange:", forControlEvents: UIControlEvents.ValueChanged)
	
	func stepChange(sender : AnyObject)
	{
	   let newStepper = sender as! UIStepper
	   print(newStepper.value)
	}