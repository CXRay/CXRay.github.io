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

###基本集合操作

下面的插图描述了两个集合-a和b-以及通过阴影部分的区域显示集合各种操作的结果。

![setVennDiagram](http://cxray.github.io/public/images/setVennDiagram_2x.png)

* 使用intersect(:)方法根据两个集合中都包含的值创建的一个新的集合。
* 使用exclusiveOr(:)方法根据值在一个集合中但不在两个集合中的值创建一个新的集合。
* 使用union(:)方法根据两个集合的值创建一个新的集合。
* 使用subtract(:)方法根据不在该集合中的值创建一个新的集合。

		let oddDigits: Set = [1, 3, 5, 7, 9]
		let evenDigits: Set = [0, 2, 4, 6, 8]
		let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]
		oddDigits.union(evenDigits).sort()
		// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
		oddDigits.intersect(evenDigits).sort()
		// []
		oddDigits.subtract(singleDigitPrimeNumbers).sort()
		// [1, 9]
		oddDigits.exclusiveOr(singleDigitPrimeNumbers).sort()
		// [1, 2, 9]

集合成员关系和相等

下面的插图描述了三个集合-a,b和c,以及通过悬浮区域表述集合间共享的元素。Set a是Setb的父集合，因为a包含了b中所有的元素，相反的，Set b是a的子集合，因为属于b的元素也被a包含。Set b和Set c彼此不关联，因为它们之间没有共同的元素。

![setEulerDiagram](http://cxray.github.io/public/images/setEulerDiagram_2x.png)

* 使用“是否等”运算符(==)来判断两个集合是否包含全部相同的值。
* 使用isSubsetOf(_:)方法来判断一个集合中的值是否也被包含在另外一个集合中。
* 使用isSupersetOf(_:)方法来判断一个集合中包含的值是另一个集合中所有的值。
* 使用isStrictSubsetOf(:)或者isStrictSupersetOf(:)方法来判断一个集合是否是另外一个集合的子集合或者父集合并且和特定集合不相等。
* 使用isDisjointWith(_:)方法来判断两个结合是否不含有相同的值。

		let houseAnimals: Set = ["🐶", "🐱"]
		let farmAnimals: Set = ["🐮", "🐔", "🐑", "🐶", "🐱"]
		let cityAnimals: Set = ["🐦", "🐭"]
		houseAnimals.isSubsetOf(farmAnimals)
		// true
		farmAnimals.isSupersetOf(houseAnimals)
		// true
		farmAnimals.isDisjointWith(cityAnimals)
		// true
		
###检测API是否可用

Swift 有内置支持去检查接口的可用性的，这可以确保我们不会不小心地使用对于当前部署目标不可用的API。

编译器使用SDK中的可用信息来验证在我们在可用部署目标指定项目的代码中所有的API调用。如果我们尝试使用一个不可用的API，Swift会在编译期报错。

我们使用一个可用性条件在一个if或guard语句中去有条件的执行一段代码，这取决于我们想要使用的API是否在运行时是可用的。编译器使用从可用性条件语句中获取的信息，这时它会去验证在代码块中调用的API是否都可用。

	if #available(iOS 9, OSX 10.10, *) {
    	// 在 iOS 使用 iOS 9 APIs , 并且在 OS X 使用 OS X v10.10 APIs
	} else {
    	// 回滚至早前 iOS and OS X 的API
	}
	
以上可用性条件指定在iOS，if段的代码仅仅在iOS9及更高可运行；在OS X，仅在OS X v10.10及更高可运行。最后一个参数，*，是必须的并且指定在任何其他平台上，if段的代码在最小可用部署目标指定项目中执行。

在它普遍的形式中，可用性条件获取了平台名字和版本的清单。平台名字可以是iOS，OSX或watchOS。除了特定的主板本号像iOS8，我们可以指定较小的版本号像iOS8.3以及 OS X v10.10.3。

	if #available(`platform name` `version`, `...`, *) {
    	`statements to execute if the APIs are available`
	} else {
    	`fallback statements to execute if the APIs are unavailable`
	}
	
###闭包
闭包表达式语法有如下一般形式：

	{ (parameters) -> returnType in
    statements
	}
闭包表达式语法可以使用常量、变量和inout类型作为参数，不提供默认值。 也可以在参数列表的最后使用可变参数。 元组也可以作为参数和返回值。

尾随闭包（Trailing Closures）
如果您需要将一个很长的闭包表达式作为最后一个参数传递给函数，可以使用尾随闭包来增强函数的可读性。 尾随闭包是一个书写在函数括号之后的闭包表达式，函数支持将其作为最后一个参数调用。

	func someFunctionThatTakesAClosure(closure: () -> Void) {
    // 函数体部分
	}

	// 以下是不使用尾随闭包进行函数调用
	someFunctionThatTakesAClosure({
    // 闭包主体部分
	})

	// 以下是使用尾随闭包进行函数调用
	someFunctionThatTakesAClosure() {
	// 闭包主体部分
	}
	
函数是闭包的一种，闭包简化但让我难以理解，闭包可以在其定义的上下文中捕获常量或变量。 即使定义这些常量和变量的原域已经不存在，闭包仍然可以在闭包函数体内引用和修改这些值。注意避免强引用。

###枚举
枚举定义了一个通用类型的一组相关值

	enum CompassPoint {
	case North
	case South
	case East
	case West
	}
	
用switch来匹配

	directionToHead = .South
	switch directionToHead {
	case .North:
    	print("Lots of planets have a north")
	case .South:
    	print("Watch out for penguins")
	case .East:
    	print("Where the sun rises")
	case .West:
    	print("Where the skies are blue")
	}

用switch必须穷举所有情况，否则用default

可以有原始值（Raw Values），这些原始值具有相同的类型。

	enum ASCIIControlCharacter: Character {
    	case Tab = "\t"
    	case LineFeed = "\n"
    	case CarriageReturn = "\r"
	}
	
递归枚举，这个我难以理解

在对操作符进行描述的时候，使用枚举类型来对数据建模很方便，因为需要考虑的情况固定可枚举。操作符可以将两个由数字组成的算数表达式连接起来，例如，将5连接成复杂一些的表达式5+4.

算数表达式的一个重要特性是，表达式可以嵌套使用。例如，表达式(5 + 4) * 2乘号右边是一个数字，左边则是另一个表达式。因为数据是嵌套的，因而用来存储数据的枚举类型也许要支持这种嵌套————这表示枚举类型需要支持递归。

递归枚举（recursive enumeration）是一种枚举类型，表示它的枚举中，有一个或多个枚举成员拥有该枚举的其他成员作为相关值。使用递归枚举时，编译器会插入一个中间层。你可以在枚举成员前加上indirect来表示这成员可递归。

例如，下面的例子中，枚举类型存储了简单的算数表达式：

	enum ArithmeticExpression {
    	case Number(Int)
    	indirect case Addition(ArithmeticExpression, ArithmeticExpression)
    	indirect case Multiplication(ArithmeticExpression, ArithmeticExpression)
}
你也可以在枚举类型开头加上indirect关键字来表示它的所有成员都是可递归的：

	indirect enum ArithmeticExpression {
    	case Number(Int)
    	case Addition(ArithmeticExpression, ArithmeticExpression)
    	case Multiplication(ArithmeticExpression, ArithmeticExpression)
	}
上面定义的枚举类型可以存储三种算数表达式：纯数字、两个表达式的相加、两个表达式相乘。Addition 和 Multiplication成员的相关值也是算数表达式————这些相关值使得嵌套表达式成为可能。

递归函数可以很直观地使用具有递归性质的数据结构。例如，下面是一个计算算数表达式的函数：

	func evaluate(expression: ArithmeticExpression) -> Int {
    	switch expression {
    	case .Number(let value):
			return value
    	case .Addition(let left, let right):
			return evaluate(left) + evaluate(right)
    	case .Multiplication(let left, let right):
			return evaluate(left) * evaluate(right)
    	}
	}

	// 计算 (5 + 4) * 2
	let five = ArithmeticExpression.Number(5)
	let four = ArithmeticExpression.Number(4)
	let sum = ArithmeticExpression.Addition(five, four)
	let product = ArithmeticExpression.Multiplication(sum, 	ArithmeticExpression.Number(2))
	print(evaluate(product))
	// 输出 "18"
该函数如果遇到纯数字，就直接返回该数字的值。如果遇到的是加法或乘法元算，则分别计算左边表达式和右边表达式的值，然后相加或相乘。

