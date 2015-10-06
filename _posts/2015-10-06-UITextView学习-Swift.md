---
layout: post
date: 2015-10-6
title: UITextView学习
categories: Swift
---

这个控件也很容易，毕竟只是写文字上的东西

>创建UITextView，前面要加一个代理UITextViewDelegate

	//创建UITextView对象
	var textView = UITextView(frame: CGRectMake(10, 120, 300, 400))
	
>设置颜色，字体，内容，对齐方式，键盘类型等等

	textView.backgroundColor = UIColor.grayColor()
	self.view.addSubview(textView)
	textView.delegate = self
	
	//设置textView字体颜色
	textView.textColor = UIColor.greenColor()
	//设置文本字体
	textView.font = UIFont.systemFontOfSize(18)
	textView.font = UIFont(name: "Helvetica-Bold", size: 18)
	//内容
	textView.text = "aaaaa\nbbbbbb\ncccccccc\nddddd\nc\nd\ne\nf\ng\nh\ni\nj\nk\nl\nm\nn\no\np\nq\nr\ns\nt\n"
	
	//文本对齐方式
	textView.textAlignment = NSTextAlignment.Right
	
	//文本视图设置圆角
	textView.layer.cornerRadius = 20
	
	//是否允许点击链接和附件
	textView.selectable = true
	
	//返回键的类型
	textView.returnKeyType = UIReturnKeyType.Done
	
	//键盘类型
	textView.keyboardType = UIKeyboardType.Default
	
	//是否可以滚动
	textView.scrollEnabled = true
	
	//自适应高度
	textView.autoresizingMask = UIViewAutoresizing.FlexibleHeight
	
	//富文本不写了
	
	//选中一段文本
	//textView.becomeFirstResponder()
	//textView.selectedRange = NSMakeRange(5, 10)
	
	//获取内容整体高度
	let height = textView.contentSize.height
	print(height)

>自定义文本视图菜单，就是选中文本，可以复制粘帖那里，可以增加一个分享到微信微博

	//自定义文本视图菜单
	var menuItem1 :UIMenuItem = UIMenuItem(title: "分享到微信", action: "shareWXMenu:")
	var menuItem2 = UIMenuItem(title: "分享到微博", action: "shareWBMenu:")
	//获取菜单控制器
	var menuController = UIMenuController.sharedMenuController()
	
	//重写canPerformAction方法，实现分享
	override func canPerformAction(action: Selector, withSender sender: AnyObject?) -> Bool {
	    //判断有没有选中文字
	    //如果选中，输出选择的文字
	    let isSelect:Bool = textView.selectedRange.length > 0
	    
	    if (action == "shareWXMenu:" && isSelect)   //选择文本并点了分享到微信
	    {
	        return true
	    }
	    else if(action == "shareWBMenu:" && isSelect)   //选择文本并点了分享到微博
	    {
	        return true
	    }
	    
	    return false    //不显示系统的菜单
	}
	
	//分享到微信
	func shareWXMenu(sender: AnyObject?)
	{
	    if textView.selectedRange.length > 0
	    {
	        print((textView.text as NSString).substringWithRange(textView.selectedRange))
	    }
	    print("微信分享")
	}
	//分享到微博
	func shareWBMenu(sender: AnyObject?)
	{
	    if textView.selectedRange.length > 0
	    {
	        print((textView.text as NSString).substringWithRange(textView.selectedRange))
	    }
	    print("微博分享")
	}
	
	//菜单控制器，放到viewDidLoad中
	menuController.menuItems = [menuItem1,menuItem2]
	
>UITextViewDelegate,所有的UITextViewDelegate都是optional可以选择实现

	//这个特别有用，之前一直不知道该如何把键盘给隐藏
    func textView(textView: UITextView, shouldChangeTextInRange range: NSRange, replacementText text: String) -> Bool {
        //文本视图内容改变时，触发本方法，能得到改变的坐标和改变的内容
        
        //如果是回车符号，则textView释放第一响应值，返回false
        if text == "\n"
        {
            textView.resignFirstResponder()
            return false
        }
        return true
    }
    
    
    func textViewShouldBeginEditing(textView: UITextView) -> Bool {
        return true //如果返回false，文本视图不能编辑
    }
    
    func textViewShouldEndEditing(textView: UITextView) -> Bool {
        return true //如果返回false，表示编辑结束之后，文本视图将不能编辑
    }
    
    func textViewDidBeginEditing(textView: UITextView) {
        //文本视图开始编辑，这个时候我们可以处理一些事情
        print("文本视图开始编辑")
    }
    
    func textViewDidEndEditing(textView: UITextView) {
        //文本视图编辑结束，这个时候我嘛可以处理一些事情
    }
    
    func textViewDidChange(textView: UITextView) {
        //文本视图改变后触发本代理方法
        print("文本改变")
    }
    
    func textViewDidChangeSelection(textView: UITextView) {
        //文本视图改变选择内容，触发本代理方法
        print("文本视图改变选择内容")
    }
    
    func textView(textView: UITextView, shouldInteractWithURL URL: NSURL, inRange characterRange: NSRange) -> Bool {
        //链接在文本中显示，当链接被点击时，会触发本方法
        return true
    }
    
    func textView(textView: UITextView, shouldInteractWithTextAttachment textAttachment: NSTextAttachment, inRange characterRange: NSRange) -> Bool {
        //文本视图允许提供文本附件，文本附件点击时，会触发本代理方法
        return true
    }
    
>TextView有三个监听

	//创建3个监听，viewDidLoad中
	//文本框开始编辑时触发
	NSNotificationCenter.defaultCenter().addObserver(self, selector: "textDidBeginEditing", 
	name: UITextViewTextDidBeginEditingNotification, object: nil)
	
	//文本框结束编辑时，触发
	NSNotificationCenter.defaultCenter().addObserver(self, selector: "textDidEndEditing", 
	name: UITextViewTextDidEndEditingNotification, object: nil)
	
	//文本框内容改变时，触发
	NSNotificationCenter.defaultCenter().addObserver(self, selector: "textDidChange", 
	name: UITextViewTextDidChangeNotification, object: nil)
	
	
	//实现3个通知事件
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
