---
layout: post
date: 2015-10-10
title: UISlider学习
categories: Swift
---

UISlider只是设置左右图片和圆点的值，还有最大最小值，获取当前值和设定值，属性和方法比较少

>初始化

	//初始化，UISlider虽然设置了高度，但是将会被忽略
	var slider = UISlider(frame: CGRectMake(20, 100, 200, 60))
	
	//ViewDidLoad中，添加到视图
	self.view.addSubview(slider)

>滑块的最大最小值，设定，获取当前值

	//可以对滑块设置范围和默认值，如果没有设置，默认使用0.0和1.0之间的值
	//UISlider提供了两个属性，mininumValue和maxinumValue来设置范围，代码如下
	//设置最小值
	slider.minimumValue = 10
	//设置最大值
	slider.maximumValue = 80
	//设置默认值
	slider.value = 30

	//读取当前滑块控件的值
	let value = slider.value
	print(value)
	
>设置滑块左右图片和圆点的图片

	//设置滑块右端图片,可以根据滑块的各种不同状态显示不同图像，下面是几个可用状态
	/*UIControlState.Normal
	UIControlState.Disabled
	UIControlState.Highlighted
	UIControlState.Reserved
	UIControlState.Selected*/
	
	slider.setMaximumTrackImage(UIImage(named: "1127074.jpg"), forState: UIControlState.Normal)
	
	//设置滑块左端图片
	slider.setMinimumTrackImage(UIImage(named: "990589.jpg"), forState: UIControlState.Normal)
	
	//设置滑块圆点的图片
	//slider.setThumbImage(image: UIImage?, forState: UIControlState)
	
>添加通知,这两个ValueChanged和TouchUpInside在这里差不多,要注意action那里方法名一定要加个:啊，不然会引发bug

	//要想在滑块值改变时收到通知，可以用UIControl类的addTarget方法为UIControlEvents.ValueChanged事件添加一个动作
	slider.addTarget(self, action: "sliderValueChange:"
	,forControlEvents: UIControlEvents.ValueChanged)
	
	//滑块拖动后的事件
	slider.addTarget(self, action: "sliderTouchUpInside:", forControlEvents: UIControlEvents.TouchUpInside)
	
	func sliderValueChange(sender: AnyObject)
    {
        let newSlider = sender as! UISlider
        let value = newSlider.value
        print(value)
    }
    
    func sliderTouchUpInside(sender: AnyObject)
    {
        let newSlider = sender as! UISlider
        let value = newSlider.value
        print(value)
    }

>不一直触发以上方法，放倒ViewDidLoad中

	//在拖动时，上面方法一直在触发，如果想要滑块停止拖动才触发slierValueChange方法，我们只需要设置滑块的continuous属性值为false即可实现
	slider.continuous = false
