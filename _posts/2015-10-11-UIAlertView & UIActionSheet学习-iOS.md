---
layout: post
date: 2015-10-11
title: UIAlertView & UIActionSheet学习
categories: iOS
---

提醒视图和操作表这两个视图不能通过Xib创建，只能通过代码添加视图，UIAlertView 提醒视图主要以弹框的形式提醒或警告用户以引起用户注意，UIActionSheet 操作表从界面底部弹出并有很多操作按钮供用户选择,iOS9已经不建议使用这种方式了

#UIAlertView 提醒视图

>提醒视图一般用于程序发生一些变化的时候通知用户，让用户知晓并采取一些措施，例如网络连接失败，分享信息成功等等

	//创建提醒视图，单一按钮提醒视图
	let oneButtonAlert = UIAlertView(title: "提醒", message: "这是一个简单的提醒视图", delegate: nil, cancelButtonTitle: "确定")
	
	//创建多按钮提醒视图
	let moreButtonAlert = UIAlertView(title: "提醒", message: "多按钮提醒视图，请选择一个按钮", delegate: nil, cancelButtonTitle: "确定", otherButtonTitles: "按钮1", "按钮2","按钮3","按钮4")
	
	//显示提醒视图
	oneButtonAlert.show()
	moreButtonAlert.show()

	//也可以通过以下方法创建视图
	let alert = UIAlertView()
	//设置标题，代理，提醒信息
	alert.title = "提醒"
	alert.delegate = self
	alert.message = "另外一种创建方式"
	//添加按钮
	alert.addButtonWithTitle("按钮1")
	alert.addButtonWithTitle("按钮2")
	alert.addButtonWithTitle("按钮3")
	//cancelButton设置
	alert.cancelButtonIndex = 2
	
	//显示
	alert.show()
	
>其它的提醒视图风格
	
	//上面介绍的提醒框都是.Default风格，还有其它几种风格
	//SecureTextInput一个密码输入框，PlainTextInput一个明文文本框，LoginAndPasswordInput具有账号输入框和密码输入框
	oneButtonAlert.alertViewStyle = .LoginAndPasswordInput
	
	//可以通过textFieldAtIndex方法来获取文本框
	let textField = oneButtonAlert.textFieldAtIndex(0)
	//设置文本框占位符
	textField?.placeholder = "请输入帐号"
	oneButtonAlert.show()
	
>实现UIAlertViewDelegate

    //实现提醒视图的代理
    //根据被点击按钮的索引处理点击事件
    func alertView(alertView: UIAlertView, clickedButtonAtIndex buttonIndex: Int) {
        switch buttonIndex
        {
        case 0:
            print("按钮1")
        case 1:
            print("按钮2")
        case 2:
            print("按钮3")
        case 3:
            print("按钮4")
        default:
            print("确定")
        }
    }
    
    //取消按钮的事件
    func alertViewCancel(alertView: UIAlertView) {
        print("取消")
    }
    
    //即将显示时的事件
    func willPresentAlertView(alertView: UIAlertView) {
        print("即将显示时的事件")
    }
    
    //已经显示时的事件
    func didPresentAlertView(alertView: UIAlertView) {
        print("已经显示时的事件")
    }
    
    //即将消失时的事件
    func alertView(alertView: UIAlertView, willDismissWithButtonIndex buttonIndex: Int) {
        print("即将消失时的事件")
    }
    
    func alertView(alertView: UIAlertView, didDismissWithButtonIndex buttonIndex: Int) {
        print("已经消失时的事件")
    }
    
    
    //编辑任何默认字体的字段添加的风格之后调用，没理解这句话...........
    func alertViewShouldEnableFirstOtherButton(alertView: UIAlertView) -> Bool {
        return false
    }

#UIActionSheet 操作表

>初始化

	//创建操作表代码如下，创建不带红色按钮的操作表,主要是将destructiveButtonTitle改成nil
	var actionSheet = UIActionSheet(title: "请选择分享方向", delegate: nil, cancelButtonTitle: "取消", 
	destructiveButtonTitle: nil, otherButtonTitles: "邮件", "短信","微博","微信")
	
	//创建带有红色按钮的操作表
	var actionSheet2 = UIActionSheet(title: "将删除该条评论", delegate: nil, cancelButtonTitle: "取消", destructiveButtonTitle: "确定删除")

	//showInView方法让操作表出现在某一视图上，如果想将操作表视图与工具栏或者标签栏对齐，可以使用
	//actionSheet.showFromToolbar(view: UIToolbar)
	//actionSheet.showFromTabBar(view: UITabBar)
	//actionSheet.showInView(self.view)
	actionSheet2.showInView(self.view)
	
>操作表常用的一些属性方法

	//设置标题
	actionSheet.title = "新标题"
	//设置样式
	actionSheet.actionSheetStyle = UIActionSheetStyle.Default
	//添加按钮
	actionSheet.addButtonWithTitle("新加按钮")
	//根据index坐标获取一个按钮文本,取消按钮是
	let butIndex = actionSheet.buttonTitleAtIndex(2)
	print(butIndex)
	//获取取消按钮的坐标
	let cancelIndex = actionSheet.cancelButtonIndex
	print(cancelIndex)
	//获取按钮个数
	let butCount = actionSheet.numberOfButtons
	print(butCount)
	
>实现UIActionSheetDelegate

	//单独设置delegate为self，才能是弹出的操作表视图响应点击事件
	actionSheet.delegate = self
	
	 //实现操作表视图的代理
    //根据被点击按钮的索引处理点击事件
    func actionSheet(actionSheet: UIActionSheet, clickedButtonAtIndex buttonIndex: Int) {
        switch buttonIndex
        {
        case 0:
            print("点击取消")
        case 1:
            print("1")
        case 2:
            print("2")
        case 3:
            print("3")
        default:
            print("未知")
        }
    }
    
    //选择取消按钮
    func actionSheetCancel(actionSheet: UIActionSheet) {
        print("你点击了取消按钮")
    }
    
    //即将显示时
    func willPresentActionSheet(actionSheet: UIActionSheet) {
        print("即将显示")
    }
    
    //已显示时
    func didPresentActionSheet(actionSheet: UIActionSheet) {
        print("已经显示")
    }
    
    //即将消失
    func actionSheet(actionSheet: UIActionSheet, willDismissWithButtonIndex buttonIndex: Int) {
        print("即将消失")
    }
    
    //已消失
    func actionSheet(actionSheet: UIActionSheet, didDismissWithButtonIndex buttonIndex: Int) {
        print("已消失")
    }
    
    