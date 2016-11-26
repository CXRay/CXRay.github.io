---
layout: post
date: 2016-02-15
title: TableView实现搜索，分节和索引，NavigationController
categories: iOS
---

实现搜索，分节和索引
---
ViewController.h中,添加数据源UITableViewDataSource

```obj-c
	#import <UIKit/UIKit.h>
	
	@interface ViewController : UIViewController<UITableViewDataSource>
	
	
	@end
```

ViewController.m中
```obj-c

	#import "ViewController.h"
	#import "SearchResultController.h"
	static NSString *SectionTableIdentifier = @"SectionTableIdentifier";
	
	@interface ViewController ()
	
	@property (copy,nonatomic) NSDictionary *names;
	@property (copy,nonatomic) NSArray *keys;
	@property (weak, nonatomic) IBOutlet UITableView *tableView;
	@property (strong,nonatomic) UISearchController *searchController;
	
	@end
	
	@implementation ViewController
	
	- (void)viewDidLoad {
	    [super viewDidLoad];
	    // Do any additional setup after loading the view, typically from a nib.
	    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:SectionTableIdentifier];
	    
	    //引用sortednames.plist中数据
	    NSString *path = [[NSBundle mainBundle]pathForResource:@"sortednames" ofType:@"plist"];
	    self.names = [NSDictionary dictionaryWithContentsOfFile:path];
	    self.keys = [[self.names allKeys] sortedArrayUsingSelector:@selector(compare:)];
	    
	    //首先创建结果控制器，使用SearchResultController.m文件中实现的初始化函数。然后创建UISearchController，并将它的引用传递给结果控制器，UISearchController在显示搜索结果时会出现这个视图控制器
	    SearchResultController *resultController = [[SearchResultController alloc]initWithNames:self.names keys:self.keys];
	    self.searchController = [[UISearchController alloc]initWithSearchResultsController:resultController];
	    //配置UISearchBar
	    //默认是没有范围按钮的
	    UISearchBar *searchBar = self.searchController.searchBar;
	    searchBar.scopeButtonTitles = @[@"All",@"Short",@"Long"];
	    searchBar.placeholder = @"Enter a search term";
	    //获取搜索栏并将其作为主视图控制器中表视图的顶部视图
	    [searchBar sizeToFit];
	    self.tableView.tableHeaderView = searchBar;
	    self.searchController.searchResultsUpdater = resultController;
	    //分区索引改变颜色
	    self.tableView.sectionIndexBackgroundColor = [UIColor blackColor];
	    self.tableView.sectionIndexTrackingBackgroundColor = [UIColor darkGrayColor];
	    self.tableView.sectionIndexColor = [UIColor whiteColor];
	}
	
	- (void)didReceiveMemoryWarning {
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	//Data Source
	//返回几节
	-(NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
	{
	    return [self.keys count];
	}
	
	-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
	{
	    NSString *key = self.keys[section];
	    NSArray *nameSection = self.names[key];
	    return [nameSection count];
	}
	
	//为每个分区指定一个可选的标题
	-(NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section
	{
	    return self.keys[section];
	}
	
	-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
	    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:SectionTableIdentifier forIndexPath:indexPath];
	    NSString *key = self.keys[indexPath.section];
	    NSArray *nameSection = self.names[key];
	    
	    cell.textLabel.text = nameSection[indexPath.row];
	    return cell;
	}
	
	-(NSArray<NSString *> *)sectionIndexTitlesForTableView:(UITableView *)tableView
	{
	    return self.keys;
	}
	
	@end
```

SearchResultController.h中
```obj-c

	#import <UIKit/UIKit.h>
	//遵循UISearchResultsUpdating协议
	@interface SearchResultController : UITableViewController<UISearchResultsUpdating>
	
	-(instancetype)initWithNames:(NSDictionary *)names keys:(NSArray *)keys;
	
	@end
```

SearchResultController.m中
```obj-c

	#import "SearchResultController.h"
	
	static NSString *SectionTableIdentifier = @"SectionTableIdentifier";
	
	static const NSUInteger longNameSize = 6;
	static const NSInteger shortNamesButtonIndex = 1;
	static const NSInteger longNamesButtonIndex = 2;
	
	@interface SearchResultController ()
	
	@property(strong,nonatomic)NSDictionary *names;
	@property(strong,nonatomic)NSArray *keys;
	@property(strong,nonatomic)NSMutableArray *filteredNames;
	
	@end
	
	@implementation SearchResultController
	
	-(instancetype)initWithNames:(NSDictionary *)names keys:(NSArray *)keys
	{
	    if (self = [super initWithStyle:UITableViewStylePlain]) {
	        self.names = names;
	        self.keys = keys;
	        self.filteredNames = [[NSMutableArray alloc]init];
	    }
	    return self;
	}
	
	-(void)viewDidLoad
	{
	    [super viewDidLoad];
	    
	    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:SectionTableIdentifier];
	}
	//用户
	-(void)updateSearchResultsForSearchController:(UISearchController *)searchController
	{
	    NSString *searchString = searchController.searchBar.text;
	    //获取索引值
	    NSInteger buttonIndex = searchController.searchBar.selectedScopeButtonIndex;
	    [self.filteredNames removeAllObjects];
	    if (searchString.length > 0) {
	        //NSPredicate是一种对象，用于对输入的值进行测试，它们会对names字典中的每个名字进行调用，如果成功，返回YES
	        NSPredicate *preditc = [NSPredicate predicateWithBlock:^BOOL(NSString *name, NSDictionary *b) {
	            //根据所选择的范围按钮可以筛选名称的长度
	            NSUInteger nameLength = name.length;
	            if ((buttonIndex == shortNamesButtonIndex && nameLength >= longNameSize)||(buttonIndex == longNamesButtonIndex && nameLength < longNameSize)) {
	                return NO;
	            }
	            NSRange range = [name rangeOfString:searchString options:NSCaseInsensitiveSearch];
	            return range.location != NSNotFound;
	        }];
	        //对names字典中所有的键进行迭代
	        for (NSString *key in self.keys) {
	            NSArray *matches = [self.names[key] filteredArrayUsingPredicate:preditc];
	            [self.filteredNames addObjectsFromArray:matches];
	        }
	    }
	    [self.tableView reloadData];
	    
	}
	
	-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
	{
	    return [self.filteredNames count];
	}
	
	-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
	    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:SectionTableIdentifier forIndexPath:indexPath];
	    cell.textLabel.text = self.filteredNames[indexPath.row];
	    return cell;
	}
	
	@end

```


NavigationController
---

RootViewController.h中

```obj-c

	#import <UIKit/UIKit.h>
	
	@interface RootViewController : UITableViewController
	
	@end

```

RootViewController.m中

```obj-c

	#import "RootViewController.h"
	#import "FavoritesList.h"
	#import "FontListViewController.h"
	
	@interface RootViewController ()
	
	@property (copy,nonatomic) NSArray *familyNames;
	@property (assign,nonatomic) CGFloat cellPointSize;
	@property (strong,nonatomic) FavoritesList *favoritesList;
	
	@end
	
	@implementation RootViewController
	
	- (void)viewDidLoad {
	    [super viewDidLoad];
	    
	    self.familyNames = [[UIFont familyNames]sortedArrayUsingSelector:@selector(compare:)];
	    //familyNames中放置了从UIFont类获取的所有已知字体名称，并对结果数组进行排序然后再次使用UIFont获取在标题处使用的字体
	    UIFont *preferredTableViewFont = [UIFont preferredFontForTextStyle:UIFontTextStyleHeadline];
	    //使用字体的pointSize属性来建立一个在视图控制器中普遍采用的标准字体大小
	    self.cellPointSize = preferredTableViewFont.pointSize;
	    //获取收藏列表对象的单例
	    self.favoritesList = [FavoritesList sharedFavoritesList];
	}
	
	-(void)viewDidAppear:(BOOL)animated
	{
	    [super viewDidAppear:animated];
	    [self.tableView reloadData];
	}
	
	-(UIFont *)fontForDisplayAtIndexPath:(NSIndexPath *)indexPath
	{
	    //这个方法使用了UIFont类，先根据已有的字体系列名称找到所有的字体名称，然后获取系列中首个字体的名称。
	    if (indexPath.section == 0) {
	        NSString *familyName = self.familyNames[indexPath.row];
	        NSString *fontName = [[UIFont fontNamesForFamilyName:familyName]firstObject];
	        return [UIFont fontWithName:fontName size:self.cellPointSize];
	    }
	    else
	    {
	        return nil;
	    }
	}
	
	-(NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
	{
	    if ([self.favoritesList.favorites count] > 0) {
	        return 2;
	    }
	    
	    else
	    {
	        return 1;
	    }
	}
	
	-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
	{
	    if (section == 0) {
	        return [self.familyNames count];
	    }
	    else
	    {
	        return 1;
	    }
	}
	
	//指定每个分区的标题
	-(NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section
	{
	    if (section == 0) {
	        return @"All Font Families";
	    }
	    else
	    {
	        return @"My Favorite Fonts";
	    }
	}
	
	-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
	    static NSString *FamilyNameCell = @"FamilyName";
	    static NSString *FavoriteCell = @"Favorites";
	    UITableViewCell *cell = nil;
	    //配置单元表，根据分区索引来显示哪个表单元
	    if (indexPath.section == 0) {
	        cell = [tableView dequeueReusableCellWithIdentifier:FamilyNameCell forIndexPath:indexPath];
	        
	        cell.textLabel.font = [self fontForDisplayAtIndexPath:indexPath];
	        cell.textLabel.text = self.familyNames[indexPath.row];
	        cell.detailTextLabel.text = self.familyNames[indexPath.row];
	    }
	    else
	    {
	        cell = [tableView dequeueReusableCellWithIdentifier:FamilyNameCell forIndexPath:indexPath];
	    }
	    return cell;
	}
	
	//新视图控制器实际的准备工作是由以下方法来执行的，Segue转场
	-(void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
	{
	    //用sugue destinationViewController获取新的视图控制器
	    //将选定对象传入新的视图控制器
	    NSIndexPath *indexPath = [self.tableView indexPathForCell:sender];
	    FontListViewController *listVC = segue.destinationViewController;
	    //destination目的地，终点
	    if (indexPath.section == 0) {
	        NSString *familyName = self.familyNames[indexPath.row];
	        listVC.fontNames = [[UIFont fontNamesForFamilyName:familyName]sortedArrayUsingSelector:@selector(compare:)];
	        listVC.navigationItem.title = familyName;
	        listVC.showsFavorites = NO;
	    }
	    else
	    {
	        listVC.fontNames = self.favoritesList.favorites;
	        listVC.navigationItem.title = @"Favorites";
	        listVC.showsFavorites = YES;
	    }
	    //这个方法通过sender来判断哪一行被点击，并向segue请求它的destinationViewController，即要显示的FontListViewController实例，然后根据用户轻点的是字体系列还是收藏系列（根据section判断）直接向新的视图控制器传入一些值。
	}
	
	@end

```

FavoritesList.h

```obj-c

	#import <Foundation/Foundation.h>
	
	@interface FavoritesList : NSObject
	
	+(instancetype)sharedFavoritesList;
	-(NSArray *)favorites;
	
	-(void)addFavorite:(id)item;
	-(void)removeFavorite:(id)item;
	
	-(void)moveItemAtIndex:(NSInteger)from toIndex:(NSInteger)to;
	@end

```

FavoritesList.m

```obj-c
	
	#import "FavoritesList.h"
	
	@interface FavoritesList ()
	
	@property (strong,nonatomic) NSMutableArray *favorites;
	
	@end
	
	@implementation FavoritesList
	
	+(instancetype)sharedFavoritesList
	{
	    //创建一个累的实例，并将其返回
	    static FavoritesList *shared = nil;
	    //dispatch_once()函数的代码块中包含了用来创建代码的部分，这样做可以确保这些代码只会运行一次
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        shared = [[self alloc]init];
	    });
	    return shared;
	}
	
	//这个方法使用NSUserDefaults类来判断偏好设置里是否已经有收藏的内容，如果有，就将收藏的内容复制成一个可变副本并赋给favorite属性，否则赋给它一个空的可变数组。
	-(instancetype)init
	{
	    self = [super init];
	    if (self)
	    {
	        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
	        NSArray *storedFavorites = [defaults objectForKey:@"favorites"];
	        if (storedFavorites) {
	            self.favorites = [storedFavorites mutableCopy];
	        }
	        else
	        {
	            self.favorites = [NSMutableArray array];
	        }
	    }
	    return self;
	}
	
	-(void) addFavorite:(id)item
	{
	    [_favorites insertObject:item atIndex:0];
	    [self saveFavorites];
	}
	
	-(void)removeFavorite:(id)item
	{
	    [_favorites removeObject:item];
	    [self saveFavorites];
	}
	
	-(void)saveFavorites
	{
	    //永久性字典
	    NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
	    [defaults setObject:self.favorites forKey:@"favorites"];
	    [defaults synchronize];
	}
	
	-(void)moveItemAtIndex:(NSInteger)from toIndex:(NSInteger)to
	{
	    id item = _favorites[from];
	    [_favorites removeObjectAtIndex:from];
	    [_favorites insertObject:item atIndex:to];
	    [self saveFavorites];
	}
	@end

```

FontListViewController.h

```obj-c

	#import <UIKit/UIKit.h>
	
	@interface FontListViewController : UITableViewController
	
	@property (copy,nonatomic)NSArray *fontNames;
	@property (assign,nonatomic)BOOL showsFavorites;
	
	@end

```

FontListViewController.m

```obj-c
	
	#import "FontListViewController.h"
	#import "FavoritesList.h"
	#import "FontSizeViewController.h"
	@interface FontListViewController ()
	
	@property (assign,nonatomic) CGFloat cellPointSize;
	
	@end
	
	
	@implementation FontListViewController
	
	- (void)viewDidLoad {
	    [super viewDidLoad];
	    
	    UIFont *preferredTableViewFont = [UIFont preferredFontForTextStyle:UIFontTextStyleHeadline];
	    //cellPointSize属性变量储存每个字体的优先显示尺寸
	    self.cellPointSize = preferredTableViewFont.pointSize;
	    
	    if (self.showsFavorites) {
	        self.navigationItem.rightBarButtonItem = self.editButtonItem;
	    }
	}
	
	-(UIFont *)fontForDisplayAtIndexPath:(NSIndexPath *)indexPath
	{
	    NSString *fontName = self.fontNames[indexPath.row];
	    return [UIFont fontWithName:fontName size:self.cellPointSize];
	}
	
	-(void)viewDidAppear:(BOOL)animated
	{
	    [super viewDidAppear:animated];
	    if (self.showsFavorites) {
	        self.fontNames = [FavoritesList sharedFavoritesList].favorites;
	        [self.tableView reloadData];
	    }
	}
	
	-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
	{
	    return [self.fontNames count];
	}
	
	-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
	    static NSString *CellIdentifier = @"FontName";
	    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier forIndexPath:indexPath];
	    //配置表单元
	    cell.textLabel.font = [self fontForDisplayAtIndexPath:indexPath];//上面那个方法
	    cell.textLabel.text = self.fontNames[indexPath.row];
	    cell.detailTextLabel.text = self.fontNames[indexPath.row];
	    return cell;
	}
	
	-(void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
	{
	    NSIndexPath *indexPath = [self.tableView indexPathForCell:sender];
	    UIFont *font = [self fontForDisplayAtIndexPath:indexPath];
	    [segue.destinationViewController navigationItem].title = font.fontName;
	    FontSizeViewController *sizeVC = segue.destinationViewController;
	    sizeVC.font = font;
	}
	
	-(BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath
	{
	    //编辑功能只会在显示收藏列表时启用
	    return self.showsFavorites;
	}
	//删除行
	-(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath
	{
	    if (!self.showsFavorites) {
	        return;
	    }
	    
	    if (editingStyle == UITableViewCellEditingStyleDelete) {
	        //从数据源中删除该行
	        NSString *favorite = self.fontNames[indexPath.row];
	        [[FavoritesList sharedFavoritesList]removeFavorite:favorite];
	        self.fontNames = [FavoritesList sharedFavoritesList].favorites;
	        [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
	    }
	}
	//手动排序
	-(void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath *)destinationIndexPath
	{
	    [[FavoritesList sharedFavoritesList] moveItemAtIndex:sourceIndexPath.row toIndex:destinationIndexPath.row];
	    self.fontNames = [FavoritesList sharedFavoritesList].favorites;
	}
	@end

```

FontSizeViewController.h

```obj-c

	#import <UIKit/UIKit.h>
	
	@interface FontSizeViewController : UITableViewController
	
	@property (strong,nonatomic)UIFont *font;
	
	@end

```

FontSizeViewController.m

```obj-c

	#import "FontSizeViewController.h"
	
	@interface FontSizeViewController ()
	
	@end
	
	@implementation FontSizeViewController
	
	-(NSArray *)pointSizes
	{
	    static NSArray *pointSizes = nil;
	    //保证这段代码只运行一次
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        pointSizes = @[@9,@10,@11,@12,@13,@14,@18,@24,@36,@48,@64,@72,@96,@144];
	    });
	    return pointSizes;
	}
	
	-(UIFont *)fontForDisplayAtIndexPath:(NSIndexPath *)indexPath
	{
	    NSNumber *pointSize = self.pointSizes[indexPath.row];
	    return [self.font fontWithSize:pointSize.floatValue];
	}
	
	-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
	{
	    return [self.pointSizes count];
	}
	
	-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
	    static NSString *CellIdentifier = @"FontNameAndSize";
	    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier forIndexPath:indexPath];
	    
	    cell.textLabel.font = [self fontForDisplayAtIndexPath:indexPath];
	    cell.textLabel.text = self.font.fontName;
	    cell.detailTextLabel.text = [NSString stringWithFormat:@"%@ Point",self.pointSizes[indexPath.row]];
	
	    return cell;
	    
	}
	
	@end

```

FontInfoViewController.h

```obj-c

	#import <UIKit/UIKit.h>
	
	@interface FontInfoViewController : UIViewController
	
	@property (strong,nonatomic) UIFont *font;
	@property (assign,nonatomic) BOOL favorite;
	
	@end

```

FontInfoViewController.m

```obj-c

	#import "FontInfoViewController.h"
	#import "FavoritesList.h"
	
	@interface FontInfoViewController ()
	
	@property (weak,nonatomic) IBOutlet UILabel *fontSampleLabel;
	@property (weak,nonatomic) IBOutlet UISlider *fontSizeSlider;
	@property (weak,nonatomic) IBOutlet UILabel *fontSizeLabel;
	@property (weak,nonatomic) IBOutlet UISwitch *favoriteSwitch;
	
	@end
	
	@implementation FontInfoViewController
	
	- (void)viewDidLoad {
	    [super viewDidLoad];
	    // Do any additional setup after loading the view.
	    self.fontSampleLabel.font = self.font;
	    self.fontSampleLabel.text = @"AaBbCcDdEeFfGgHhIiJjKkLlMmNnOoPpQqRrSsTtUuVvWwXxYyZz 1234567890";
	    
	    self.fontSizeSlider.value = self.font.pointSize;
	    //显示字体大小
	    self.fontSizeLabel.text = [NSString stringWithFormat:@"%.0f",self.font.pointSize];
	    self.favoriteSwitch.on = self.favorite;
	}
	
	-(IBAction)sliderFontSize:(UISlider *)slider
	{
	    float newSize = roundf(slider.value);
	    self.fontSampleLabel.font = [self.font fontWithSize:newSize];
	    self.fontSizeLabel.text = [NSString stringWithFormat:@"%.0f",newSize];
	}
	
	//toggle切换
	-(IBAction)toggleFavorite:(UISwitch *)sender
	{
	    FavoritesList *favoriteList = [FavoritesList sharedFavoritesList];
	    if (sender.on) {
	        [favoriteList addFavorite:self.font.fontName];
	    }
	    else
	    {
	        [favoriteList removeFavorite:self.font.fontName];
	    }
	}
	
	@end

```