---
layout: post
date: 2015-9-3
title: Swift笔记
categories: Swift
---
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
	