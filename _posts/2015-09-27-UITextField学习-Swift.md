---
layout: post
date: 2015-9-27
title: UITextField学习
categories: Swift
---

这个也算简单的，只是这些控件需要记住一些方法而已

>代码初始化UITextField控件

	var textField = UITextField(frame: CGRectMake(0, 100, 300, 40))
	
>添加到视图，文本，背景色，背景图片
	
	textField.backgroundColor = UIColor.redColor()//背景色
	self.view.addSubview(textField)//添加到视图上
	textField.text = "aaaaa"//添加到视图
	var textString = textField.text//获取文本框内容，类型为optional
	textField.background = UIImage(named: "testImage");//设置背景图片
	
>设置富文本

	//设置富文本
	let attributesStr = NSMutableAttributedString(string: "0000123456789")
	//文本0开始5个字符字体HaveticaNeue-Bold,16号
	attributesStr.addAttribute(NSFontAttributeName, value: UIFont(name: "HaveticaNeue-Bold", size: 0)!, range: NSMakeRange(0, 4))
	//设置字体颜色
	attributesStr.addAttribute(NSForegroundColorAttributeName, value: UIColor.redColor(), range: NSMakeRange(4, 2))
	//设置文字背景颜色
	attributesStr.addAttribute(NSBackgroundColorAttributeName, value: UIColor.greenColor(), range: NSMakeRange(0, 4))
	//添加到textField
	textField.attributedText = attributesStr

>字体，颜色，对齐方式

	textField.font = UIFont.systemFontOfSize(18)//系统默认字体，18号字体
	textField.font = UIFont(name: "Helvetica-Bold", size: 18)//指定字体制定字号
	textField.textColor = UIColor.magentaColor()//字体颜色
	/*对齐方式，Left左对齐,Center居中,Right右对齐,Justified最后一行自然对齐,Natural默认对齐脚步*/
	textField.textAlignment = .Center
	
>其他一些设置

	//文本框样式,四种,None,Line,Bezel,RoundRect
	textField.borderStyle = .RoundedRect
	
	//设置富文本占位符
	textField.attributedPlaceholder = attributesStr
	//设置占位符，当textField没有文字时显示
	textField.placeholder = "请输入学校名称"	
	textField.minimumFontSize = 10.0//最小字体
	textField.adjustsFontSizeToFitWidth = true//文本内容自动适应标签大小，默认false
	
	/*设置删除效果，Never,WhileEditing,UnlessEditing,Always,就是右边那个x的按钮*/
	textField.clearButtonMode = .WhileEditing
	
	//返回文本框编辑状态，默认true可以编辑,这个不能set,只能get
	textField.editing
	
	//文本框是否可用
	textField.enabled = true
	
	//开始输入时，清空文本框原有内容
	textField.clearsOnBeginEditing = true
	
	//允许编辑富文本
	textField.allowsEditingTextAttributes = true

	//创建左视图
	let leftView = UIView(frame: CGRectMake(0, 0, 40, 40))
	leftView.backgroundColor = UIColor.blackColor()
	textField.leftView = leftView
	
	/*设置做视图显示类型,Never,WhileEditing,UnlessEditing,Always*/
	textField.leftViewMode = .WhileEditing//左边会出现一个黑框，不明白有什么用
	
	//同理，可以创建右视图
	let rightView = UIView(frame: CGRectMake(260, 0, 40, 40))
	
	//成为第一响应者，光标定位到该文本框，一打开视图就直接可以输入
	textField.becomeFirstResponder()
	
	//取消光标，释放键盘
	textField.resignFirstResponder()
	
	/*首字母是否大小，None不自动大写，Word单词首字母大写，句子首字母大写，所有字母大写*/
	textField.autocapitalizationType = .Words
	
>实现UITextFieldDelegate
	
	func textFieldShouldBeginEditing(textField: UITextField) -> Bool {
		return true //返回false，文本框将不能编辑
	}
	
	func textFieldDidBeginEditing(textField: UITextField) {
		//文本框开始编辑，这个时候可以处理一些事情
	}
	
	func textFieldShouldEndEditing(textField: UITextField) -> Bool {
		return true //如果返回false，表示编辑结束之后，文本框不可再编辑
	}
	
	func textFieldDidEndEditing(textField: UITextField) {
		//文本框结束编辑，触发本事件，这时候可以处理一些事情
	}
	
	func textField(textField: UITextField, shouldChangeCharactersInRange range: NSRange, replacementString string: String) -> Bool {
		//文本框内容改变时，触发本方法，能得到改变的坐标和改变的内容
		return true
	}
	
	func textFieldShouldClear(textField: UITextField) -> Bool {
		//文本框清空时触发此事件
		return true
	}
	
	func textFieldShouldReturn(textField: UITextField) -> Bool {
		//按键盘换行位置，会触发此方法，一般用于用户回收键盘，或者是提交数据
		return true
	}

>文本框状态监听

有三个通知类型

public let UITextFieldTextDidBeginEditingNotification: String

public let UITextFieldTextDidEndEditingNotification: String

public let UITextFieldTextDidChangeNotification: String

创建三个监听，代码如下

	//文本框开始编辑时，触发
	NSNotificationCenter.defaultCenter().addObserver(self, selector: "textDidBeginEditing", name: UITextFieldTextDidBeginEditingNotification, object: nil)
	//文本框编辑结束时，触发
	NSNotificationCenter.defaultCenter().addObserver(self, selector: "textDidEndEditing", name: UITextFieldTextDidEndEditingNotification, object: nil)
	//文本框内容改变时，触发
	NSNotificationCenter.defaultCenter().addObserver(self, selector: "textDidChange", name: UITextFieldTextDidChangeNotification, object: nil)

实现3个通知事件，代码如下

	func textDidBeginEditing()
	{
	    print("开始输入文本")
	}
	
	func textDidEndEditing()
	{
	    print("结束输入")
	}
	
	func textDidChange()
	{
	    print("正在输入")
	}
	
>还可以设置键盘外观和完成的按钮样式

	//键盘外观
	textField.keyboardAppearance = .Dark
	textField.keyboardAppearance = .Default
	textField.keyboardAppearance = .Light
	
	//完成按钮形式，还有一些其他
	textField.returnKeyType = .Go
	textField.returnKeyType = .Continue
	
	//键盘类型，还有一些其他
	textField.keyboardType = .PhonePad