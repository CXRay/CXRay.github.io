---
layout: post
date: 2015-9-26
title: UILabel学习
categories: Swift
---

代码创建UILabel,可以放在viewDidLoad()中,Label算是这么多控件中最简单的一个了

>初始化

	var nameLabe: UILabel = UILabel(frame: CGRectMake(100, 100, 200, 60))//初始化
	nameLabe.backgroundColor = UIColor.redColor()//背景红色
	self.view.addSubview(nameLabe)//添加到视图中

>tag值

	nameLabe.tag = 68 //设置tag值
	nameLabe = self.view.viewWithTag(68) as! UILabel//可以通过tag值获取nameLabel
	
>设置文本，对其方式

	nameLabe.text = "aa"//设置文本
	/*对齐方式:Left左对齐,Center居中,Right右对齐,Justified最后一行自然对齐,Natural默认对齐脚本*/
	nameLabe.textAlignment = .Right
>字体	

	nameLabe.font = UIFont.systemFontOfSize(18)//设置字体，使用系统默认字体，18号字
	nameLabe.font = UIFont(name: "Helvetica-Bold", size: 18)//指定字体

>颜色和阴影

	nameLabe.backgroundColor = UIColor.blueColor()
	nameLabe.backgroundColor = UIColor(red: 125/255.0, green: 125/255.0, blue: 255/255.0, alpha: 0.5)//通过RGB来设置颜色，alpha透明度
	
	nameLabe.textColor = UIColor.blueColor()//设置文本颜色
	nameLabe.shadowColor = UIColor.yellowColor()//设置文本字体阴影
	/*设置阴影方向和大小
	( 0,-1)上阴影
	( 0, 1)下阴影
	(-1, 0)左阴影
	( 1, 0)右阴影
	*/
	nameLabe.shadowOffset = CGSizeMake(2, 0)
	
>一些其他设置

	//设置折行方式，换行模式
	nameLabe.lineBreakMode = .ByCharWrapping//保留整个字符
	nameLabe.lineBreakMode = .ByClipping//简单剪裁，到界为止
	nameLabe.lineBreakMode = .ByTruncatingHead//按照...WXYZ显示
	nameLabe.lineBreakMode = .ByTruncatingMiddle//按照abcd...显示
	nameLabe.lineBreakMode = .ByTruncatingTail//按照ab..yz显示
	nameLabe.lineBreakMode = .ByWordWrapping//以空格为界限保留单词，默认
	
	//是否能与用户交互，默认是false
	nameLabe.userInteractionEnabled = false
	
	//文本是否可变，默认是false
	nameLabe.enabled = false
	
	//文本行数，等于0时没有行数限制
	nameLabe.numberOfLines = 1
	
	//最小字体，行数为1时有效，默认0.0
	nameLabe.minimumScaleFactor = 10.0
	
	//文本内容自动适应标签大小，默认false
	nameLabe.adjustsFontSizeToFitWidth = true
	
	//设置文本高亮
	nameLabe.highlighted = true
	
	//设置文本高亮颜色
	nameLabe.highlightedTextColor = UIColor.greenColor()

>富文本设置

	//富文本设置
	let attributeString = NSMutableAttributedString(string: "1234567890123")
	
	//文本0开始5个字符字体HelveticaNeue-Bold,16号
	attributeString.addAttribute(NSFontAttributeName, value: UIFont(name: "HelveticaNeue-Bold", size: 16)!, range: NSMakeRange(0, 5))
	
	//设置文字颜色,从0开始，3个单位
	attributeString.addAttribute(NSForegroundColorAttributeName, value: UIColor.whiteColor(), range: NSMakeRange(0, 3))
	
	//设置文字背景颜色，从3开始，3个单位
	attributeString.addAttribute(NSBackgroundColorAttributeName, value: UIColor.greenColor(), range: NSMakeRange(3, 3))
	
	//最后改label
	nameLabe.attributedText = attributeString
	
	/*设置文本基线位置，有以下三种，只有文本行数等于1时有效*/
	nameLabe.baselineAdjustment = .AlignBaselines//默认值为文本最上端与label中线对齐
	nameLabe.baselineAdjustment = .None//文本最底端与label中线对齐
	nameLabe.baselineAdjustment = .AlignCenters//文本中线与label中线对齐

>UILabel有两个接口专门用来重写，可以自定义label

	func textRectForBounds(bounds: CGRect,limitedToNumberOfLines numberOfLines: Int) -> CGRect
	
	func drawTextInRect(rect: CGRect)
	//上面这两个方法不能调用，只适合被UILabel子类重写
	
	func textRectForBounds(bounds: CGRect, limitedToNumberOfLines numberOfLines: Int) -> CGRect
	//用来改变UILabel里面文字展示窗口的大小，可以根据文字的多少，来计算窗口的大小
	
	func drawTextInRect(rect: CGRect)
	/*在绘图环境实现文字的绘制，这个方法里面已经配置好了绘图环境，使用方法如下
	1.直接获得当前绘图上下文。2.接着更改绘图环境设置。3.调用super方法来绘制即可*/

