---
layout: post
date: 2015-10-5
title: UITableView学习
categories: Swift
---

UITableView还真是有点多，一个个代理也还没有全都实现，其中有个按表格跳转还没有实现，还有按表格输出名字不知道莫名其妙的会延迟，就是嗯下一个的时候才输出上一个，还有时候会崩溃，可能上optional没有用好，有一张1033382.jpg图片就不放上来了，随便找一张就ok，在ViewController敲了这么多代码，意识到MVC的重要性了


UITableView显示列表数据，是通过代理实现的，要添加数据源，协议 UITableViewDataSource,UITableViewDelegate

>初始化TableView,定义数组

	//初始化TableView，样式有Plain不分组表视图和Grouped分组表视图
	//Plain不分组表视图，如果不设置组间距的话，默认组与组之间没有间距
	//Grouped分组表视图,默认组与组之间有一定间距
	//var listTableView = UITableView(frame: CGRectMake(0, 20, 470, 600), style: UITableViewStyle.Plain)
	
	var listTableView = UITableView(frame: UIScreen.mainScreen().applicationFrame, style: UITableViewStyle.Plain)
	
	//定义数组
	var item:[String] = [
	    "UITableView高级使用",
	    "自定义UITableViewCell",
	    "创建分组表",
	    "UITextView",
	    "UISegmentedControl",
	    "UISlider",
	    "UISwitch和UISetpper",
	    "UIActivityIndicatorView",
	    "UIProgressView",
	    "UIAlertView",
	    "UIActionSheet",
	    "UIImageView",
	    "UIScrollView和UIPageControl",
	    "UIWebView",
	    "UIDataPickerView",
	    "UIToolBar和UISearchBar",
	    "UINavigationController",
	    "UITabBarController",
	    "读写应用程序数据",
	    "多点触摸和手势识别",
	    "多媒体应用",
	    "动画",
	    "网络通信",
	    "检测设备朝向和移动",
	    "定位处理与地图",
	    "地址簿，短信，邮件",
	    "iOS应用本地化"
	]

>必须实现以下四个协议

	//1.设置表视图的节数，也就是分组列表中对应的组，每一组就是一节，在一个分组表中，最开始
	的节称为0节，如果TableView只有一节，也可以不用设置此代理方法，系统默认就是一节不分组表视图
	//tableView数据源：返回几节（组）
	func numberOfSectionsInTableView(tableView: UITableView) -> Int {
	    return 1
	}
	
	//2.设置表视图的行数，需要返回每一节具有多少行数据，可以根据section值，分别返回不同的条数，代理方法实现代码如下
	func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	    return item.count   //返回了上面item数组中的数量
	}
	
	//3.设置单元格
	func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
	    //设置单元格UITableViewCell,创建单元格需要注意，一定要先定义一个标示符，指定给cell，这样可以通过这个标示符来获取未使用的cell，实现重用机制
	    //创建标示符语句
	    let cellIdentifier = "cellIdentifier"
	    
	    //可以通过这个常量标示符来获取未使用的cell,有可能获取不到，所以cell为optional
	    var cell = tableView.dequeueReusableCellWithIdentifier(cellIdentifier)
	    //判断cell是否存在，如果不存在，才需要创建新的cell
	    if cell == nil
	    {
	        //创建新的cell，cell样式UITableViewCellStyle.Default，cell标示符cellIdentifier
	        /*UITableViewCellStyle这个有四种样式可选，Default,Subtitle,Value1,Value2,具体样式可以再查Goole*/
	        cell = UITableViewCell(style: UITableViewCellStyle.Default, reuseIdentifier: cellIdentifier)
	        
	        //设置字体
	        cell?.textLabel?.font = UIFont.systemFontOfSize(14)
	        //设置选中cell样式
	        cell?.selectionStyle = .Gray
	        //设置cell后面箭头样式,有5种样式，None,DisclosureIndicator,DetailDisclosureButton,
	        //Cherkmark,DetailButton
	        cell?.accessoryType = .DisclosureIndicator
	    }
	    
	    //从数组中取对应值给cell赋值
	    cell?.textLabel?.text = item[indexPath.row]
	    //设置cell图片
	    cell?.imageView?.image = UIImage(named: "1033382.jpg")
	    //因为样式，所以要detailTextLabel
	    cell?.detailTextLabel?.text = "详细信息介绍"
	    
	    return cell!
	}
	
	
	//4.tableView数据源，每一行高度
	func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
	    //tableView来指定表视图，indexPath来指定哪一节哪一行需要返回的高度，指定表视图的哪一行
	    return 50
	}
	
>UITableViewCell选择事件,当选择了这个cell会发生什么，这里输出text，可以进行跳转，但是这里有一些小问题，输出text也会崩溃，按当前cell会输出上一个cell的text

	//UITableViewCell选择事件，当在TableView中点击一个cell时，会调用此方法，tableView代表当前触发手势的TableView对象，indexPath参数代表
	//当前点击的位置，需要注意一点，当选中一个cell时，应取消前一个cell的选择状态，具体如下
	//tableView代理，点击一行，可以不实现
	func tableView(tableView: UITableView, didDeselectRowAtIndexPath indexPath: NSIndexPath) {
	    //释放选中效果
	    tableView.deselectRowAtIndexPath(indexPath, animated: true)
	    
	    //获得点击的cell
	    let cell:UITableViewCell! = tableView.cellForRowAtIndexPath(indexPath)
	    print(cell!.textLabel!.text!)
	    
	    //判断cell内容，根据内容不同打开不同界面
	    var viewCtrl: UIViewController!
	    if cell.textLabel!.text! == "UITableView高级使用"
	    {
	        let newCtrl = UITableViewController(nibName: "UITableViewControllerAF", bundle: NSBundle.mainBundle())      //这个有点问题
	        newCtrl.title = cell.textLabel!.text!
	        viewCtrl = newCtrl
	    }
	
	}
	
>实现表视图的编辑操作，必须实现下面三个代理方法

	//1.指定单元格是否具有编辑功能
	func tableView(tableView: UITableView, canEditRowAtIndexPath indexPath: NSIndexPath) -> Bool {
	    return true
	}
	//2.指定单元格每一行的编辑类型,返回每一行操作类型
	func tableView(tableView: UITableView, editingStyleForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCellEditingStyle {
	    if indexPath.row == item.count - 1
	    {
	        return UITableViewCellEditingStyle.Insert//最后一行允许插入
	    }
	    return UITableViewCellEditingStyle.Delete// 允许删除
	    //编辑类型有三种，Insert具有插入功能，Delete具有删除功能，None不具有功能
	}
	//3.指定单元格是否允许拖动，编辑状态下，是否允许拖动
	func tableView(tableView: UITableView, canMoveRowAtIndexPath indexPath: NSIndexPath) -> Bool {
	    return true
	}

>设置一个编辑／完成按钮，最后三条语句仅仅只是为了测试，要删除

	var button = UIButton(frame: CGRectMake(300, 0, 115, 60))
	@IBAction func editButtonClick()
	{
	    if button.titleForState(UIControlState.Normal) == "编辑"
	    {
	        //如果按钮标题为编辑，则将表视图设置成可编辑的状态，并修改button标题为完成
	        self.listTableView.setEditing(true, animated: true)
	        button.setTitle("完成", forState: .Normal)
	    }
	    else
	    {
	        //如果按钮标题为完成，则将表视图设置成不可编辑，并修改button标题为编辑
	        self.listTableView.setEditing(false, animated: true)
	        button.setTitle("编辑", forState: .Normal)
	    }
	    
	    //也可以自定义一个按钮来添加新单元格，这里直接用编辑的按钮了
	    //数组添加新数据
	    item.insert("新城市\(item.count)", atIndex: 0)
	    //初始化一个NSIndexPath对象，指定要添加单元格位置
	    let indexPath = NSIndexPath(forItem: 0, inSection: 0)
	    //在指定位置上添加一个新的单元格
	    self.listTableView.insertRowsAtIndexPaths([indexPath], withRowAnimation: .Automatic)
	}

>实现表视图的删除插入操作

	/*UITableViewRowAnimation,Fade单元格淡出,Right单元格从右侧划出,Left单元格从左侧划出,
	Top单元格滑动到相邻单元格之上,Bottom单元格滑动到相邻单元格之下,None无动画,Middle始终居中,
	Automatic自动效果*/
	
	func tableView(tableView: UITableView, commitEditingStyle editingStyle: UITableViewCellEditingStyle, forRowAtIndexPath indexPath: NSIndexPath) {
	    //如果是删除操作
	    if editingStyle == UITableViewCellEditingStyle.Delete
	    {
	        //数据源数组删除对应行数 数据,经常忘了这个.row......
	        item.removeAtIndex(indexPath.row)
	        //table表删除该行,注意[indexPath]
	        tableView.deleteRowsAtIndexPaths([indexPath], withRowAnimation: UITableViewRowAnimation.Left)
	    }
	    else if editingStyle == UITableViewCellEditingStyle.Insert
	    {
	        //数组添加一条新数据
	        item.append("新城市\(item.count)")
	        //表视图插入一条单元格
	        tableView.insertRowsAtIndexPaths([indexPath], withRowAnimation: UITableViewRowAnimation.Middle)
	    }
	}

>移动cell事件&行缩进，行缩进item.count仅仅只是为了演示

	//移动cell事件，sourceIndexPath: NSIndexPath代表移动初始位置，toIndexPath destinationIndexPath: NSIndexPath代表移动的目标位置
	func tableView(tableView: UITableView, moveRowAtIndexPath sourceIndexPath: NSIndexPath, toIndexPath destinationIndexPath: NSIndexPath) {
	    if sourceIndexPath != destinationIndexPath
	    {
	        //获取移动行对应的值
	        let itemValue = item[sourceIndexPath.row]
	        //删除移动行的值
	        item.removeAtIndex(sourceIndexPath.row)
	        //如果移动区域大于现有行数，直接在最后添加移动的值
	        if destinationIndexPath.row > item.count
	        {
	            item.append(itemValue)
	        }
	        else
	        {
	            item.insert(itemValue, atIndex: destinationIndexPath.row)
	        }
	    }
	}
	
	//行缩进
	func tableView(tableView: UITableView, indentationLevelForRowAtIndexPath indexPath: NSIndexPath) -> Int {
	    //每行按其索引缩进
	    return indexPath.row
	}

>viewDidLoad()中的代码

	override func viewDidLoad() {
	    super.viewDidLoad()
	    // Do any additional setup after loading the view, typically from a nib.
	    
	    //指定TableView的数据源和代理
	    self.listTableView.delegate = self
	    self.listTableView.dataSource = self
	    self.view.addSubview(listTableView)
	    
	    button.backgroundColor = UIColor.orangeColor()
	    button.setTitle("编辑", forState: .Normal)
	    button.addTarget(self, action: "editButtonClick", forControlEvents: .TouchUpInside)
	    self.listTableView.addSubview(button)
	    
	}