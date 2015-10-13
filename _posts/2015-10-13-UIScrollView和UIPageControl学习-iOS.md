---
layout: post
date: 2015-10-13
title: UIScrollView和UIPageControl学习
categories: iOS
---

#UIScrollView

UIScrollView可滚动视图，允许一个屏幕上显示很多内容，利用addSubview方法它把所有的内容都添加到ScrollView，可以给滚动视图添加放置很多视图但问题是这样会造成严重的内存泄漏，所以在考虑用ScrollView时一定要考虑这个问题，当某些视图滚动出可见范围时，应该进行内存回收或者重利用UIScrollView是UITableView和UITextView的超类，支持显示比屏幕更大的应用窗口的内容，它通过挥动手势，能够使用户滚动内容，并且通过捏合手势缩放部分内容
>初始化scrollView和3个view

	var scrollView = UIScrollView(frame: CGRectMake(20, 100, 300, 400))
	let view1 = UIView(frame: CGRectMake(0, 0, 300, 400))
	let view2 = UIView(frame: CGRectMake(300, 0, 300, 400))
	let view3 = UIView(frame: CGRectMake(600, 0, 300, 400))
	
	//设置背景色，添加到视图
	scrollView.backgroundColor = UIColor.grayColor()
	self.view.addSubview(scrollView)
	        
>滚动视图添加view

	//给滚动视图添加一些View
	
	view1.backgroundColor = UIColor.redColor()
	scrollView.addSubview(view1)
	
	view2.backgroundColor = UIColor.greenColor()
	scrollView.addSubview(view2)
	
	view3.backgroundColor = UIColor.blueColor()
	scrollView.addSubview(view3)
	
>注意要设置scrollView的容器大小

	//这样scrollView并不能滚动，要设置scrollView容器大小
	scrollView.contentSize = CGSizeMake(900, 500)
	
>滚动条的风格

	//设置滚动条风格,Default，白边界上绘制黑色滚动条，大多数背景下适用，Black黑色滚动条，用于白背景下，White白色滚动条，用于黑色背景下
	scrollView.indicatorStyle = UIScrollViewIndicatorStyle.Default
	//如果不想显示滚动条，可以隐藏，水平，垂直方向滚动条是否可见
	scrollView.showsHorizontalScrollIndicator = false
	scrollView.showsVerticalScrollIndicator = false
	
	
>实现分页滚动

	//上面的ScrollView是可以连续滚动的，如果想实现分页滚动，可以通过pagingEnable属性设置来实现
	//设置成翻页滚动
	scrollView.pagingEnabled = false
	
>UIScrollViewDelegate代理的实现

	//获取ScollView滚动的相关信息，可以给ScrollView设置委托，委托方法会在特定时刻收到通知
	scrollView.delegate = self
	
	//获取当前滚动视图移动的x,y坐标,可以用.x来获取x坐标
	let offsetX = scrollView.contentOffset
	print(offsetX)
	
	//开启缩放功能需要设置视图的两个属性，分别时maximumZoomScale 和 minmumZoomScale，这样就可以允许用户使用捏合手势调整内容大小
	//允许放大2倍，允许缩小到0.5倍
	scrollView.maximumZoomScale = 2.0
	scrollView.minimumZoomScale = 0.5
	//接下来实现一个viewForZoomingInScrollView方法来做出响应，这个方法会返回进行缩放时使用的UIView对象	
	
	
	 //实现下面的代理方法，就可以知道滚动视图的运动状态
    
    //视图滚动中一直触发
    func scrollViewDidScroll(scrollView: UIScrollView) {
        print("视图滚动中一直触发")
    }
    
    //刚开始拖动视图时触发一次
    func scrollViewWillBeginDragging(scrollView: UIScrollView) {
        print("刚开始拖动视图时触发一次")
    }
    
    //将要结束拖动触发一次
    func scrollViewWillEndDragging(scrollView: UIScrollView, withVelocity velocity: CGPoint, targetContentOffset: UnsafeMutablePointer<CGPoint>) {
        print("将要结束拖动触发一次")
    }
    
    //拖动结束，触发一次
    func scrollViewDidEndDragging(scrollView: UIScrollView, willDecelerate decelerate: Bool) {
        print("拖动结束，触发一次")
    }
    
    //拖动结束后，开始减速
    func scrollViewWillBeginDecelerating(scrollView: UIScrollView) {
        print("拖动结束后，开始减速")
    }
    
    //减速完毕，恢复到静止状态
    func scrollViewDidEndDecelerating(scrollView: UIScrollView) {
        print("减速完毕，恢复到静止状态")
        
        //获取当前滚动视图移动的x,y坐标,可以用.x来获取x坐标
        let offsetX = scrollView.contentOffset
        print(offsetX)
        
        //获取scrollView视图滚动的x坐标
        let offX = scrollView.contentOffset.x
        
        //计算当前是第几页
        let index = Int(offX / 300)
        
        //设置分页指示器currentPage值
        pageCtrl.currentPage = index
        
    }
    
    //这个还要涉及到手势识别然后处理图像大小
    func viewForZoomingInScrollView(scrollView: UIScrollView) -> UIView? {
        //获取当前滚动视图移动的x坐标
        let offsetX = scrollView.contentOffset.x
        print(offsetX)
        
	//        //如果没有滚动，返回view1
	//        if offsetX == 0 {
	//            return view1
	//        }
	//        //滚动300，返回view2
	//        else if offsetX == 300 {
	//            return view2
	//        }
	//        //滚动600，返回view3
	//        else if offsetX == 600{
	//            return view3
	//        }
        return scrollView;
        
    }
    
    //监听缩放动作，也是通过实现滚动视图的代理来处理一些事件
    //缩放过程中一直调用
    func scrollViewDidZoom(scrollView: UIScrollView) {
        print("缩放过程中一直调用")
    }
    
    //开始缩放，调用一次
    func scrollViewWillBeginZooming(scrollView: UIScrollView, withView view: UIView?) {
        print("开始缩放，调用一次")
    }
    
    //结束缩放，调用一次
    func scrollViewDidEndZooming(scrollView: UIScrollView, withView view: UIView?, atScale scale: CGFloat) {
        print("结束缩放，调用一次")
    }

#UIPageControl

就是页面下面几个点，代表你在第几个视图

>初始化

	//UIPageControl分页控制器，
	var pageCtrl = UIPageControl(frame: CGRectMake(20, 540, 300, 40))
	
>一些属性

	self.view.addSubview(pageCtrl)
	pageCtrl.backgroundColor = UIColor.greenColor()
	//设置分页控制器总页数
	pageCtrl.numberOfPages = 3
	//设置当前显示页位置
	pageCtrl.currentPage = 0
	
	//分页控制添加一个UIControlEvents.ValueChanged事件后，点击分页控制器时 Value值改变会触发此事件
	//添加事件
	pageCtrl.addTarget(self, action: "pageTurn:", forControlEvents: UIControlEvents.ValueChanged)
	
	
	
	//如果想实现ScrollView位置改变，也改变PageControl的Value值，只需要在ScrollView的scrollViewDidEndDecelerating方法里判断滚动位置，来设置Page Control的值
	
	 //事件实现代码,分页控制器value改变事件
    func pageTurn(sender:AnyObject)
    {
        //获取分页控制器
        let newPageCtrl = sender as! UIPageControl
        //获取分页控制器当前页
        let index = newPageCtrl.currentPage
        
        //设置ScrollView的x移动距离
        scrollView.contentOffset = CGPointMake(CGFloat(300 * index), 0)
    }
    

