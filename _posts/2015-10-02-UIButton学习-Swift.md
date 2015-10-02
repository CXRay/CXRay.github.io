---
layout: post
date: 2015-10-2
title: UIButton学习
categories: Swift
---

UIButton也不是很难，就是设置一下button的标题，或者是图片，按钮的颜色之类的，按下的效果，还有要写一下按钮按下的函数，按下将会发生什么

>初始化

	//初始化
	var button = UIButton(frame: CGRectMake(0, 300, 200, 60))
	
	//UIButtonType来初始化，DetailDisclosure蓝色小箭头，InfoDark暗色小箭头，InfoLight亮色小箭头，ContactAdd十字加号,System系统风格，Custom自定义风格，好像有些要在特点适合地方才能用，我测试了有几个不能用
	var button2 = UIButton(type: UIButtonType.ContactAdd)
	
>按钮状态，颜色，字体，设置图片

	//需要指定状态
	button.setTitle("aaa", forState: .Normal)
	//获取标题标签控件
	let label1 = button.titleLabel
	print(label1)
	//正常状态颜色
	button.setTitleColor(UIColor.redColor(), forState: UIControlState.Normal)
	//鼠标按下时的颜色
	button.setTitleColor(UIColor.blackColor(), forState: .Highlighted)
	//设置标题阴影颜色
	button.setTitleShadowColor(UIColor.blackColor(), forState: .Normal)
	//设置图片,会覆盖标题
	//button.setImage(UIImage(named: "testImage.jpg"), forState: .Normal)
	
>富文本，前面两个有富文本的详细，这里不写了

	//设置富文本,富文本的其它看前两个控件
	let buttonAttribute = NSMutableAttributedString(string: "1234567890")
	//添加至按钮
	button.setAttributedTitle(buttonAttribute, forState: .Normal)
	
>有时候可能按钮可能会深一点，通过这两个方法调整，还有个发光功能，微博有用到

	//默认情况下，按钮高亮的情况下，图像会被画的深一些，如果下面的这个属性设置成false，可以去掉这个功能
	button.adjustsImageWhenHighlighted = false
	//默认情况下，当按钮禁用时，图像会被画得深一些，false可以取消
	button.adjustsImageWhenDisabled = false
	//下面这个属性设置成true，按钮按下会发光,和高亮不同,好像微博里有用到这个
	button.showsTouchWhenHighlighted = true
	
>获取按钮的文本，标题，颜色，阴影颜色，图片

	//获取富文本
	let attributedTitle = button.attributedTitleForState(.Normal)
	//获取标题
	let titleOfState = button.titleForState(.Normal)
	//获取标题颜色
	let titleColorOfState = button.titleColorForState(.Normal)
	//获取标题阴影颜色
	let titleShadowColorOfState = button.titleShadowColorForState(.Normal)
	//获取图片
	let imageOfState = button.imageForState(.Normal)

>通过这个函数来实现设置方法，当然可以用连线这种更简单的方法，一下这个比较适合用代码来创建一个按钮
	
	//target一般用self，指实现方法的对象,action后跟一个方法名，注意要加引号，forControlEvents是枚举事件类型
	button.addTarget(target: AnyObject?, action: Selector, forControlEvents: UIControlEvents)
	
如下面这个例子

	button.addTarget(self, action: "buttonUpInsideFunc", forControlEvents: .TouchUpInside)
	
	func buttonUpInsideFunc()
	{
	    print("按下了按钮，触摸并抬起")
	}
	
常用的事件类型如下

AllEditingEvents			通知所有关于文本编辑的窗口

AllEvents						通知所有事件

AllTouchEvents				通知所有触摸事件

ApplicationReserved		保留供应用程序的使用

EditingChanged				当文本控件中的文本被改变时触发

EditingDidBegin				当文本控件中开始编辑时触发

EditingDidEnd				当文本控件中编辑结束时触发

EditingDidEndOnExit		当文本控件内通过按下回车键结束编辑时触发

PrimaryActionTriggered	触发主要事件

SystemReserved				保留供内部框架的使用范围

TouchCancel					触摸取消事件，即一次触摸因为放上了太多手指而被取消，或接打电话

TouchDown						单点触摸下事件，点触屏幕

TouchDownRepeat				多点触摸按下事件，点触计数大于1，按下第2，3，4根手指的时候

TouchDragEnter				触摸从控件之外拖动到内部时

TouchDragExit				触摸从控件内部拖动到外部时

TouchDragInside				触摸在控件内拖动时

TouchDragOutside			触摸在控件之外拖动时

TouchUpInside				在控件之内触摸并抬起事件，这个用的最多

TouchUpOutside				在控件之外触摸并抬起事件

ValueChanged					当控件的值发生变化时，发送通知，用于滑块，分段控件，以及其它取值的控件