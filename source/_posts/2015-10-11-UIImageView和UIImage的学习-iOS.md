---
layout: post
date: 2015-10-11
title: UIImageView和UIImage的学习
categories: iOS
---

>创建

	let imageView = UIImageView(frame: CGRectMake(20, 10, 450, 250))
	let imageView2 = UIImageView(image: UIImage(named: "72017.jpg"))
	
	imageView.image = UIImage(named: "1033382.jpg")
	self.view.addSubview(imageView)
	
	imageView2.frame = CGRectMake(20, 300, 450, 100)
	self.view.addSubview(imageView2)
	
>获取图片的各种方法

	//1.给图像视图设置图片时，需要用到image属性来设一个UIImage对象，创建图片对象，可以通过图片的名字来创建图片对象
	let image = UIImage(named: "1033382.jpg")
	
	//2.如果工程中也加入了@2x和@3x的图片，我们在使用的时候，可以不指定图片的后缀，只使用图片的名字，这样系统会自动选择使用哪一个尺寸
	let image2 = UIImage(named: "1033382")

>从文件中获取，这里有些问题

	 //3.从文件目录中获取图片，这里还存在一些问题
	 let path = NSBundle.mainBundle().pathForResource("1033382", ofType: "jpg")
	 //解包
	 if let sValue = path
	 {
	     let newImage = UIImage(contentsOfFile: sValue)
	     let imageView3 = UIImageView(frame: CGRectMake(20, 560, 450, 250))
	     imageView3.image = newImage
	     self.view.addSubview(imageView3)
	     print("aaa")
	 }
	 
>从网络中获取，这里iOS9要调整

iOS9让所有的HTTP默认使用了HTTPS，原来的HTTP协议传输都改成TLS1.2协议进行传输。直接造成的情况就是App发请求的时候弹出网络无法连接。解决办法就是在项目的info.plist 文件里加上如下节点：

NSAppTransportSecurity - NSAllowsArbitraryLoads
	 
这个子节点的意思是：是否允许任性的加载？！ 设为YES的话就将禁用了AppTransportSecurity转而使用用户自定义的设置，这个问题就解决了。具体见http://www.cnblogs.com/dsxniubility/p/4821184.html

![UIImageView和UIImage的学习](http://cxray.github.io/images/UIImageView和UIImage的学习.png)

	 //4.从网络地址获取图片
	 //定义一个地址字符串常量
	 let imageUrlString = "http://www.stayreal.wang/images/intro.jpg"
	 //通过String类型，转换成NSURL对象
	 let url = NSURL(string: imageUrlString)
	 //从网络获取数据流
	 let data = NSData(contentsOfURL: url!)
	 //通过数据流初始化图片
	 if data != nil
	 {
	     let newImage2 = UIImage(data: data!)
	     let imageView4 = UIImageView(image: newImage2)
	     imageView4.frame = CGRectMake(20, 560, 450, 250)
	     self.view.addSubview(imageView4)
	 }

>图片显示风格

	//为了使图片在显示时候满足各种需求，系统提供了多种显示模式，UIViewContentMode一共提供了13种显示风格
	/*
	public enum UIViewContentMode : Int {
	
	case ScaleToFill        //完全填充在frame中
	case ScaleAspectFit     //保持比例，都在frame内
	case ScaleAspectFill    //保持比例，但在frame外也有
	case Redraw             //调用setNeedsDisplay方法时，重绘边界变化
	case Center             //image的中心与frame的中心重合
	case Top                //上边缘对齐
	case Bottom             //下边缘对齐
	case Left               //左侧边缘对齐
	case Right              //右侧边缘对齐
	case TopLeft            //上部和左侧对齐
	case TopRight           //上部和右侧对齐
	case BottomLeft         //下部和左侧对齐
	case BottomRight        //下部和右侧对齐
	}
	*/
	
	imageView2.contentMode = UIViewContentMode.ScaleAspectFit
	
>利用UIImageView实现幻灯片效果

	//定义图片名数组
	let imageArray = [
	    UIImage(named: "1033382.jpg")!,
	    UIImage(named: "72017.jpg")!
	]
	//设置animationImages属性
	imageView2.animationImages = imageArray
	//设置动画时间
	imageView2.animationDuration = 1
	//设置播放次数
	imageView2.animationRepeatCount = 10
	//开始播放动画
	imageView2.startAnimating()
	
