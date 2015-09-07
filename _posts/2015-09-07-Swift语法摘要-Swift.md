---
layout: post
date: 2015-9-7
title: Swift语法摘要
categories: Swift
---
来源：http://wiki.jikexueyuan.com/project/swift/
###空合运算符(Nil Coalescing Operator)
空合运算符(a ?? b)将对可选类型a进行空判断，如果a包含一个值就进行解封，否则就返回一个默认值b.这个运算符有两个条件:

* 表达式a必须是Optional类型

* 默认值b的类型必须要和a存储值的类型保持一致

空合并运算符是对以下代码的简短表达方法

	a != nil ? a! : b

上述代码使用了三目运算符。当可选类型a的值不为空时，进行强制解封(a!)访问a中值，反之当a中值为空时，返回默认值b。无疑空合运算符(??)提供了一种更为优雅的方式去封装条件判断和解封两种行为，显得简洁以及更具可读性。

注意： 如果a为非空值(non-nil),那么值b将不会被估值。这也就是所谓的短路求值。
下文例子采用空合并运算符，实现了在默认颜色名和可选自定义颜色名之间抉择：

	let defaultColorName = "red"
	var userDefinedColorName: String?   //默认值为 nil
	var colorNameToUse = userDefinedColorName ?? defaultColorName
	// userDefinedColorName 的值为空，所以 colorNameToUse 的值为 "red"

userDefinedColorName变量被定义为一个可选String类型，默认值为nil。由于userDefinedColorName是一个可选类型，我们可以使用空合运算符去判断其值。在上一个例子中，通过空合运算符为一个名为colorNameToUse的变量赋予一个字符串类型初始值。 由于userDefinedColorName值为空，因此表达式userDefinedColorName ?? defaultColorName返回defaultColorName的值，即red。

另一种情况，分配一个非空值(non-nil)给userDefinedColorName，再次执行空合运算，运算结果为封包在userDefaultColorName中的值，而非默认值。

	userDefinedColorName = "green"
	colorNameToUse = userDefinedColorName ?? defaultColorName
	// userDefinedColorName 非空，因此 colorNameToUse 的值为 "green"

a = b ?? c 简而言之，如果b有值，那a就等于b，如果b为optional，则为c

###计算字符数量 (Counting Characters)
如果想要获得一个字符串中Character值的数量，可以使用字符串的characters属性的count属性，这个比较不一样，

	var word = "cafe"
	word.characters.count

###字符串索引 (String Indices)

每一个String值都有一个关联的索引(index)类型，String.Index，它对应着字符串中的每一个Character的位置。

前面提到，不同的字符可能会占用不同数量的内存空间，所以要知道Character的确定位置，就必须从String开头遍历每一个 Unicode 标量直到结尾。因此，Swift 的字符串不能用整数(integer)做索引。

使用startIndex属性可以获取一个String的第一个Character的索引。使用endIndex属性可以获取最后一个Character的后一个位置的索引。因此，endIndex属性不能作为一个字符串的有效下标。如果String是空串，startIndex和endIndex是相等的。

通过调用String.Index的predecessor()方法，可以立即得到前面一个索引，调用successor()方法可以立即得到后面一个索引。任何一个String的索引都可以通过锁链作用的这些方法来获取另一个索引，也可以调用advancedBy(_:)方法来获取。但如果尝试获取出界的字符串索引，就会抛出一个运行时错误。

可以使用下标语法来访问String特定索引的Character。

	let greeting = "Guten Tag!"
	greeting[greeting.startIndex]
	// G
	greeting[greeting.endIndex.predecessor()]
	// !
	greeting[greeting.startIndex.successor()]
	// u
	let index = greeting.startIndex.advancedBy(7)
	greeting[index]
	// a

试图获取越界索引对应的Character，将引发一个运行时错误。

	greeting[greeting.endIndex] // error
	greeting.endIndex.successor() // error

使用characters属性的indices属性会创建一个包含全部索引的范围(Range)，用来在一个字符串中访问单个字符。

	for index in greeting.characters.indices {
		print("\(greeting[index]) ", terminator: "")
	}
	// 打印输出 "G u t e n   T a g !"

###插入和删除 (Inserting and Removing)

调用insert(_:atIndex:)方法可以在一个字符串的指定索引插入一个字符。

	var welcome = "hello"
	welcome.insert("!", atIndex: welcome.endIndex)
	// welcome now 现在等于 "hello!"
调用insertContentsOf(_:at:)方法可以在一个字符串的指定索引插入一个字符串。

	welcome.insertContentsOf(" there".characters, at: 	welcome.endIndex.predecessor())
	// welcome 现在等于 "hello there!"
调用removeAtIndex(_:)方法可以在一个字符串的指定索引删除一个字符。

	welcome.removeAtIndex(welcome.endIndex.predecessor())
	// welcome 现在等于 "hello there"
调用removeRange(_:)方法可以在一个字符串的指定索引删除一个子字符串。

	let range = welcome.endIndex.advancedBy(-6)..<welcome.endIndex
	welcome.removeRange(range)
	// welcome 现在等于 "hello"

###前缀/后缀相等 (Prefix and Suffix Equality)

通过调用字符串的hasPrefix(:) / hasSuffix(_:)方法来检查字符串是否拥有特定前缀/后缀，两个方法均接收一个String类型的参数，并返回一个布尔值。

下面的例子以一个字符串数组表示莎士比亚话剧《罗密欧与朱丽叶》中前两场的场景位置：

	let romeoAndJuliet = [
    	"Act 1 Scene 1: Verona, A public place",
    	"Act 1 Scene 2: Capulet's mansion",
    	"Act 1 Scene 3: A room in Capulet's mansion",
    	"Act 1 Scene 4: A street outside Capulet's mansion",
    	"Act 1 Scene 5: The Great Hall in Capulet's mansion",
    	"Act 2 Scene 1: Outside Capulet's mansion",
    	"Act 2 Scene 2: Capulet's orchard",
    	"Act 2 Scene 3: Outside Friar Lawrence's cell",
    	"Act 2 Scene 4: A street in Verona",
    	"Act 2 Scene 5: Capulet's mansion",
    	"Act 2 Scene 6: Friar Lawrence's cell"
	]
您可以调用hasPrefix(_:)方法来计算话剧中第一幕的场景数：

	var act1SceneCount = 0
	for scene in romeoAndJuliet {
		if scene.hasPrefix("Act 1 ") {
       	++act1SceneCount
    	}
	}
	print("There are \(act1SceneCount) scenes in Act 1")
	// 打印输出 "There are 5 scenes in Act 1"
相似地，您可以用hasSuffix(_:)方法来计算发生在不同地方的场景数：

	var mansionCount = 0
	var cellCount = 0
	for scene in romeoAndJuliet {
    	if scene.hasSuffix("Capulet's mansion") {
        	++mansionCount
    	} else if scene.hasSuffix("Friar Lawrence's cell") {
       	 ++cellCount
    	}
	}
	print("\(mansionCount) mansion scenes; \(cellCount) cell scenes")
	// 打印输出 "6 mansion scenes; 2 cell scenes"