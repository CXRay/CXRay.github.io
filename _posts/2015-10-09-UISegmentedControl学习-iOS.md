---
layout: post
date: 2015-10-9
title: UISegmentedControl学习
categories: iOS
---

这个分段控件也不难，只是一些属性记记住就好了

>创建数组和segment

	var appsArray = ["StayReal","CXRay","aaa","bbb","ccc"]
	var segment: UISegmentedControl!

>在ViewDidLoad中初始化，并添加到视图
	
	//创建分段控件
	segment = UISegmentedControl(items: appsArray)
	//设置大小
	segment.frame = CGRectMake(20, 100, 320, 40)
	//添加到视图
	self.view.addSubview(segment)
	
>分段控件常用属性和方法

	//默认选中下标为1的
	segment.selectedSegmentIndex = 1
	
	//获取选中的坐标,这个属性可读可写
	let index = segment.selectedSegmentIndex
	print(index)
	
	//获取segment的数量
	let count = segment.numberOfSegments
	print(count)
	
	//设置下标等于2的标题
	segment.setTitle("ddd", forSegmentAtIndex: 2)
	
	//设置下标等于3的图片，图片和标题不能共存，设置了图片之后，原来的标题就不显示了,可是这个图片有要求，这里有些问题
	segment.setImage(UIImage(named: "1033382.png"), forSegmentAtIndex: 3)
	
	//在坐标为2的位置，插入一个分段标题
	segment.insertSegmentWithTitle("insert", atIndex: 2, animated: true)
	//同理可插入图片
	segment.insertSegmentWithImage(image: UIImage?, atIndex: Int, animated: Bool)
	
	//获取某一下标下的标题
	let title = segment.titleForSegmentAtIndex(0)
	//获取某一下标下的图片
	let image = segment.imageForSegmentAtIndex(3)
	
	//momentary默认为NO，点击之后一直处于选中状态，除非点击其它分段，才恢复，当设置成YES时，点击选中，但一会儿就恢复到正常状态,一般设置成false吧，除非特殊要求
	segment.momentary = false
	
	//设置某一分段是否可用
	segment.setEnabled(false, forSegmentAtIndex: 1)
	
	//设置下标为0的segment的宽度
	segment.setWidth(100, forSegmentAtIndex: 0)
	
	//获取某一下标的segment的宽度,如果是默认会输出0.0
	let width = segment.widthForSegmentAtIndex(2)
	print(width)
	
	//设置内容偏移
	segment.setContentOffset(CGSizeMake(10, 10), forSegmentAtIndex: 2)
	
	//获取某一个下标segment的内容偏移
	let size = segment.contentOffsetForSegmentAtIndex(3)
	print(size)
	
	//是否根据segment的内容改变其宽度
	segment.apportionsSegmentWidthsByContent = true
	
	//设置标题颜色
	segment.tintColor = UIColor.redColor()
	
	//获取标题颜色,颜色可读可写
	let segmentColor = segment.tintColor
	print(segmentColor)
	
	//移除下标为2的segment
	segment.removeSegmentAtIndex(4, animated: true)
	
	//移除所有segment
	//segment.removeAllSegments()
	
>添加事件，当segment改变时，触发

	segment.addTarget(self, action: "segmentChange:", forControlEvents: UIControlEvents.ValueChanged)
	
	func segmentChange(sender: AnyObject)
	{
	    let segment: UISegmentedControl = sender as! UISegmentedControl
	    switch segment.selectedSegmentIndex
	    {
	    case 0:
	        print("0")
	    case 1:
	        print("1")
	    case 2:
	        print("2")
	    case 3:
	        print("3")
	    default:
	        print("defaule")
	    }
	}
