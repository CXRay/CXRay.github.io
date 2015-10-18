---
layout: post
date: 2015-10-18
title: UIWebView学习
categories: iOS
---


网页视图UIWebView可以让我们实现应用里内嵌浏览器,UIWebView除了可以显示网页外，还可以显示HTML语音，CSS图像，Word，Excel，Keynote，Pages，PDF，Powerpoint等类型文件，不要忘了.Delegate ＝ 某某某，就是因为这个，我调Bug调了一会

>初始化

	var aWebView=UIWebView(frame:UIScreen.mainScreen().applicationFrame)
	self.view.addSubview(aWebView)
	
>这里iOS9涉及到http://会报错，处理方法见UIImageView和UIImage的学习

	//网页字符串
	let webString = "http://stayreal.wang"
	
	//通过string类型初始化NSURL对象
	let url = NSURL(string: webString)
	
	//通过NSURL对象初始化NSURLRequest
	let request = NSURLRequest(URL: url!)
	
	//WebView加载网页
	aWebView.loadRequest(request)
	
        
>网页很大，手机屏幕有限，我们只看了网页的一部分，可以通过scalesPageToFit 属性来设置网页压缩实现全屏显示,但是我试了一下没区别啊，我去

	aWebView.scalesPageToFit = true
	
>除了加载网页内容，也可以使用HTML代码编写特殊的网页内容，WebView可以支持加载HTML语言，使用loadHTMLString方法，baseURL赋值一个css的路径，可以用网络路径使用网络上的css

	aWebView.loadHTMLString("aaa", baseURL: nil)
	
>利用UIWebView加载本地文件，以Word文档为例，这个没有成功，原因时pathForResource没有找到文件

	//定义一个常量路径
	let wordPath = NSBundle.mainBundle().pathForResource("实验报告格式", ofType: "docx")
	//通过String类型初始化NSURL对象
	let wordURL = NSURL(string: wordPath!)
	//通过NSURL对象初始化NSURLRequest
	let wordRequest = NSURLRequest(URL: wordURL!)
	//加载
	aWebView.loadRequest(wordRequest)
	//这里搜不到这个文件，发现wordPath居然是nil

>网页视图加载内容时，是需要等待一段时间，在这个加载过程中为了让用户能够感受到变化，可以使用活动指示器UIActivityIndicatorView来体现真正加载。查看有没有加载成功，可以通过WebView的代理方法获得当前加载的情况

	//创建活动指示器UIActivityIndicatorView
	var activityIndicator = UIActivityIndicatorView(frame: CGRectMake(UIScreen.mainScreen().applicationFrame.midX, UIScreen.mainScreen().applicationFrame.midY, 20, 20))
	activityIndicator.activityIndicatorViewStyle = .Gray
	aWebView.addSubview(activityIndicator)
	
>实现代理UIWebViewDelegate

	//设置aWebView的代理,这个注意不要忘记
	aWebView.delegate = self
	
	
	//实现UIWebViewDelegate
	
	//开始加载
	func webViewDidStartLoad(webView: UIWebView) {
	    activityIndicator.startAnimating()
	    print("开始加载")
	}
	
	//加载成功结束
	func webViewDidFinishLoad(webView: UIWebView) {
	    activityIndicator.stopAnimating()
	    print("加载成功结束")
	}
	
	func webView(webView: UIWebView, didFailLoadWithError error: NSError?) {
	    print("加载失败")
	}
	
	//当WebView显示内容后，浏览页面内容时可以通过触摸方式来触发一些事件，通过WebView代理方法可以知道触摸事件的类型
	func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {
	    /*
	    UIWebViewNavigationType有以下几种类型
	    public enum UIWebViewNavigationType : Int {
	    
	    case LinkClicked    //链接被触发
	    case FormSubmitted  //提交信息时触发
	    case BackForward    //页面前后跳转时触发
	    case Reload         //页面重新加载时触发
	    case FormResubmitted//重复提交时触发
	    case Other          //使用loadRequest方法读取内容时触发
	    }
	    */
	    return true
	}
	