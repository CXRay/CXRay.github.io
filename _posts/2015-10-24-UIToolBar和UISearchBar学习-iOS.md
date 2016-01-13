---
layout: post
date: 2015-10-24
title: UIToolBar和UISearchBar学习
categories: iOS
---

UIToolBar(工具栏)是一个实心条栏，一般位于屏幕的顶部或者底部。UISearchBar(搜索条)主要实现搜索框效果，经常和UITableView结合使用，从大量的信息中搜索需要的内容

#UIToolBar

工具栏的实现和分段控件UISegmentControl类似，但是工具栏中的所有控件都是完全独立的。UIToolBar实例只是提供了一个选择器控件，让用户来选择执行某个功能

>创建UIToolBar实例,代码创建默认是没有UIBarButtonItem对象的，不过可以自己创建UIBarButtonItem实例添加到toolBar上，来实现一些功能

	var toolBar = UIToolbar(frame: CGRectMake(0,0,UIScreen.mainScreen().bounds.size.width,65))
	self.view.addSubview(toolBar)
	
>常用的一些方法，代码如下

	//设置工具栏样式，有黑色，黑色透明，黑色不透明，默认
	toolBar.barStyle = UIBarStyle.Default
	
	//设置工具栏按钮文字颜色
	toolBar.tintColor = UIColor.greenColor()
	
	//设置工具栏颜色
	toolBar.backgroundColor = UIColor.redColor()
	
>设置间距和按钮

	//创建一个间距
	var flexibleSpace = UIBarButtonItem(barButtonSystemItem: UIBarButtonSystemItem.FlexibleSpace, target: "barButtonItemClicked:", action: nil)
	
	//创建一个返回按钮
	var backItem = UIBarButtonItem(title: "返回", style: UIBarButtonItemStyle.Plain, target: self, action: "backItemClicked:")
	
	//继续创建按钮
	var barBtnItem1 = UIBarButtonItem(title: "功能1", style: UIBarButtonItemStyle.Plain, target: self, action: "barBtnItemClicked:")
	var barBtnItem2 = UIBarButtonItem(title: "功能2", style: UIBarButtonItemStyle.Plain, target: self, action: "barBtnItemClicked:")
	
>最后需要靠items来添加

	toolBar.items = [flexibleSpace,backItem,flexibleSpace,barBtnItem1,flexibleSpace,barBtnItem2,flexibleSpace]
	
#UISearchBar

>UISearchBar,创建

	var searchBar = UISearchBar(frame: CGRectMake(0,100,UIScreen.mainScreen().bounds.size.width,45))
	
	self.view.addSubview(searchBar)
	
>常用的属性和方法

	//设置UISearchBar样式
	searchBar.barStyle = UIBarStyle.Default
	
	//设置UISearchBar的代理
	searchBar.delegate = self
	
	//设置UISearchBar上面显示的文字
	//searchBar.text = "这里可以选择显示文字"
	
	//设置在顶部的单行文字，通常作为一个提示行
	//searchBar.prompt = "这里也可以选择显示文字"
	
	//搜索框占位符号，半透明的提示文字，输入搜索内容消失
	searchBar.placeholder = "搜索框占位符号"
	
	//控件右端是否显示一个书的按钮
	searchBar.showsBookmarkButton = false
	
	//控件右端是否显示cancel按钮
	searchBar.showsCancelButton = true
	
	//控件右端是否显示搜索结果按钮
	searchBar.showsSearchResultsButton = true
	
	//搜索结果按钮是否被选中
	searchBar.searchResultsButtonSelected = false
	
	//bar的颜色
	searchBar.tintColor = UIColor.redColor()
	
	//指定UISearchBar是否会有透视效果
	searchBar.translucent = true
	
	//设置在什么情况下大写
	searchBar.autocapitalizationType = UITextAutocapitalizationType.AllCharacters
	
	//对于文本对象自动校正风格
	searchBar.autocorrectionType = UITextAutocorrectionType.Default
	
	//键盘样式
	searchBar.keyboardType = UIKeyboardType.Default
	
	//UISearchBar下部的选择栏，数组里面的内容是按钮的标题
	searchBar.scopeButtonTitles = ["1","2"]
	
	//UISearchBar下部的索引范围按钮的标题阵列，默认值为0，如果超出范围的忽视
	searchBar.selectedScopeButtonIndex = 2
	
	//控制UISearchBar下部的选择栏是否显示出来
	searchBar.showsScopeBar = true

>实现UISearchBarDelegate

    //搜索框开始时是否能编辑
    func searchBarShouldBeginEditing(searchBar: UISearchBar) -> Bool {
        return true         //false不能编辑
    }
    
    //搜索框开始输入内容
    func searchBarTextDidBeginEditing(searchBar: UISearchBar) {
        print("搜索框开始输入内容")
    }
    
    //搜索框结束时是否能编辑
    func searchBarShouldEndEditing(searchBar: UISearchBar) -> Bool {
        return true
    }
    
    //结束文本输入
    func searchBarTextDidEndEditing(searchBar: UISearchBar) {
        print("结束文本输入")
    }
    
    //根据searchText进行搜索
    func searchBar(searchBar: UISearchBar, textDidChange searchText: String) {
        print(searchText)
    }
    
    //文本改变时触发
    func searchBar(searchBar: UISearchBar, shouldChangeTextInRange range: NSRange, replacementText text: String) -> Bool {
        print("文本改变")
        return true
    }
    
    //右侧类似书的按钮事件
    func searchBarBookmarkButtonClicked(searchBar: UISearchBar) {
        print("右侧类似书的按钮被点击")
    }
    
    //右侧搜索按钮事件
    func searchBarSearchButtonClicked(searchBar: UISearchBar) {
        print("右侧搜索按钮被点击")
    }
    
    //右侧取消按钮事件
    func searchBarCancelButtonClicked(searchBar: UISearchBar) {
        print("取消按钮被点击")
    }
    
    //右侧结果列表按钮事件
    func searchBarResultsListButtonClicked(searchBar: UISearchBar) {
        print("右侧结果列表按钮事件")
    }
    
    //UISearchBar下面栏目选择改变触发事件
    func searchBar(searchBar: UISearchBar, selectedScopeButtonIndexDidChange selectedScope: Int) {
        print("UISearchBar下面栏目选择改变")
    }

