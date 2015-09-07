---
layout: post
date: 2015-9-4
title: Swift一些UI基础
categories: Swift
---
>出处是斯坦福公开课：iOS 8开发，http://www.swiftv.cn/course/i7ahl5gn

数组的一些方法 Array<T> Methods

	var a = [a,b,c]
	append(T)
	insert(T,atIndex: Int)
	splice(Array<T>,atIndex: Int)//连接一个数组
	removeAtIndex(Int) //a.removeAtIndex(1), a = [a,c]
	removeRange(Range) //a.removeRange(0..<2), a = [c]
	replaceRange(Range, [T]) //a.replaceRange(0...1,with: [x,y,z]), a = [x,y,z,c]
	sort(isOrderedBefore: (T, T) -> Bool)可以传人闭包（$0<$1）
	
	filter(includeElement: (T) -> Bool) -> [T] 
	//可以去除一些不满足条件的数？
	map(transform: (T) -> U) -> [u]
	//let stringified: [String] = [1,2,3].map {"\($0)"} 
	reduce(initial: U, combine:(U, T) -> U) ->U
	//let sum: Int = [1,2,3].reduce(0) { $0 + $1 } **这三行我还不懂，没试过**

下面关于String.Index的内容

	var s = "hello"
	let index = advance(s.startIndex, 1)//index是一个String.Index类
	s.splice("abc", atIndex: index)//在s中插入abc，现在s为habcello
	let startIndex = advance(s.startIndex, 1)
	let endIndex = advance(s.startIndex, 6)
	let substring = s[startIndex ..< endIndex]//截取字符串，substring为abcel,如果改成s[1 ..< 6]会报错
	
rangOfString 返回一个Optional Range<String.Index>,用if let

	let num = "56.25"
	if let decimalRange = num.rangeOfString("."){
		print(decimalRange)		//输出2..<3
		let wholeNumberPart = num[num.startIndex ..< decimalRange.endIndex]//wholeNumberPart = "56."
	}

removeRange & replaceRange

	s.removeRange([s.startIndex ..< s.endIndex])
	//replaceRange(Range, String)
	s = "Hello"
	s.replaceRange(s.startIndex ..< advance(s.startIndex, 3), with: "aa")

其他一些String方法
	
	description -> String
	endIndex -> String.Index
	hasPrefix(String) -> Bool
	hsSuffix(String) -> Bool
	toInt() -> Int?
	capitalizedString -> String
	lowercaseString -> String
	uppercaseString -> String
	join(Array) -> String	//",".join(["1","2","3"]) ＝ “1,2,3”
	componentsSeparateByString(String) -> [String] //“1,2,3”.csbs(",") = ["1","2","3"]与join反一下
	
断言Assertions

	asseret(() -> Bool. "message")

其他一些函数，数组，字典，字符串中都可以使用

	let count = countElements(aCollection) //how many elements in the collection
	let sub = dropFirst(aSliceable) //drops the first thing in the sliceable
	let sub = dropLast(aSliceable) //drops the last thing in the sliceable
	let first = first(aCollection) //the first element in the collection(or nil)
	let last = last(aCollection) //the last element in the collection(or nil)
	let prefix = prefix(aSliceable, X: Int) //returns the first x things
	let suffix = suffix(aSliceable, X: Int) //return the last X things
	let reversed: Array = reverse(aCollection) //remember that String is a collection
	let backwardsString = String(reverse(s)) //so this is possible
	
显示类型转换

	let d: Double = 37.5
	let f: Float = 37.5
	let x = Int(d)
	let xd = Double(x)
	let cgf = CGFloat(d)
	
	let a = Array("abc")
	let s = String("a","b","c")
	
CGFloat用于UIView上代替Double or Float，可以转换let cfg = CGFloat(aDouble)

CGPiont是一个简单的结构，由两个CGFloat x,y组成
	
	var point = CGPoint = CGPiont(x: 37.0, y: 55.2)
	point.y -= 30
	point.x += 20.0
	
CGSize也是一个简单的结构，由CGFloat width,height组成

	var size = CGSize(width: 100.0, height: 50.0)
	size.width += 42.5
	size.height += 75

CGRect是一个坐标，由CGPiont和CGSize组成的一个结构体

	struct CGRect {
		var origin: CGPiont
		var size: CGSize
	}
	intersects(CGRect) -> Bool
	intersect(CGRect)
	contains(CGPoint) ->Bool

画图Drowing

* 当用户转置了手机，不应该重新画图而是重新摆放位置

		UIViewContentMode
		//don't scale the view, just place it somewhere
		.Left/.Right/.Top/.Bottom/.TopRight/.TopLeft/.BottomRight/.BottomLeft/.Center
		//Scale the "bits" of the view...
		.ScaleToFill/.ScaleAspectFill/.ScaleAspectFit// .ScaleToFill is defaule
		Redraw by calling drawRect again
		.Redraw


* 常见图形

		let roundRect = UIBezierPath(roundedRect: aCGRect, cornerRadius: aCGFloat)
		let oval = UIBezierPath(ovalInRect: aCGRect)

* 修剪图形

		addClip()

* 碰撞检测
		
		func containsPoint(CGPoint) -> Bool
		
* 定义路径 Defining a Path
		
			//Create a UIBezierPath
		let path = UIBezierPath()
			//Move around, add lines or arcs to the path
		path.moveToPoint(CGPoint(80,50))
			//close the path(if you want)
		path.addLineToPoint(CGPoint(140,150))
		path.addLineToPoint(CGPoint(10,150))
		path.closePath()
		UIColor.greenColor().setFill()
		UIColor.redColor().setFill()
			//注意是UIColor中的一个方法
		path.lineWidth = 3.0
		path.fill()
		path.stroke()
			//画出图形

* UIColor透明度

		let transparenYellow = UIColor.yellowColor().colorWithAlphaComponent(0.5)
		//0.0透明，1.0不透明

字体，系统有推荐字体
	
	class func preferredFontForTextStyly(UIFontTextStyle) -> UIFont
	UIFontTextStyle.Headline
	UIFontTextStyle.Body
	UIFontTextStyle.Footnote
	//系统字体
	class func systemFontOfSize(pointSize: CGFloat) -> UIFont
	class func boldSystemFontOfSize(pointSize: CGFloat) -> UIFont

手势Gestures

	UIPinchGestureRecognizer//捏合
	UIRotationGestureRecognizer//旋转
	UISwipeGestureRecognizer//滑动
	UITapGestureRecongnizer//轻触
	
密文显示

	secuieTextEntry