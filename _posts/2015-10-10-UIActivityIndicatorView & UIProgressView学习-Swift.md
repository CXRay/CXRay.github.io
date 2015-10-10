---
layout: post
date: 2015-10-10
title: UIActivityIndicatorView & UIProgressView学习
categories: Swift
---

#活动指示器

>活动指示器初始化，添加到视图

	//活动指示器显示一个标准的转动轮，默认大小为20*20，显示得很清楚，如果代码创建大于此尺寸的话，显示效果就会变得模糊
	var activityView = UIActivityIndicatorView(frame: CGRectMake(100, 100, 20, 20))
	
	self.view.addSubview(activityView)

	//可以通过活动指示器的activityIndicatorViewStyle属性来设置显示风格
	//.Gray标准尺寸的灰色指示器  White标准尺寸的白色指示器  WhiteLarge较大的白色指示器
	activityView.activityIndicatorViewStyle = .Gray

>活动指示器启动停止，自动隐藏

	//如果希望指示器停止后自动隐藏，只需要设置hidesWhenStopped属性为true,默认为true，设置为false停止后指示器仍会显示
	activityView.hidesWhenStopped = false
	
	//启动活动指示器
	activityView.stopAnimating()
	//停止活动指示器
	activityView.startAnimating()
	
>对于iOS左上角任务栏Wi-Fi右侧活动显示器的控制

	//我们经常看到应用连接网络时，iOS设备状态栏左上角wifi旁边会出现一个活动指示器，意在告诉用户当前在使用网络
	//这个功能可以通过修改UIApplication的networkActivityIndicatorVisible属性来设置显示或隐藏，设置网络指示器代码如下
	//获取应用单利
	let app:UIApplication = UIApplication.sharedApplication()
	app.networkActivityIndicatorVisible = true
	
#进度控制条
	
>进度控制条初始化，添加到视图

	//进度控制条，和活动指示器相似，只不过进度控制条提供了一个接口可以设置一个值来表示当前的进度，这样能让用户知道当前操作完成了多少
	var progressView = UIProgressView(frame: CGRectMake(20, 200, 200, 20))
	
	self.view.addSubview(progressView)
	
	//进度控制器有两种风格，Default正常的进度条，Bar一般用于toolBar中,有无灰色为完成部分的区别
	progressView.progressViewStyle = UIProgressViewStyle.Default


>进度条的值设定

	//进度控制器的值范围是0.0～1.0之间的浮点数，可以对其progress属性设置，来控制显示进度
	progressView.progress = 0.5
	//或
	progressView.setProgress(0.8, animated: true)
	
>可以设置背景颜色或者图片，以及进度的颜色或者图片

	//当前进度的颜色
	progressView.progressTintColor = UIColor.greenColor()
	//背景颜色,未完成部分
	progressView.trackTintColor = UIColor.blueColor()
	
	//也可以用到图片,设置进度的图片
	//progressView.progressImage = UIImage(named: String)
	//设置背景图片，未完成部分
	//progressView.trackImage = UIImage(named: String)
