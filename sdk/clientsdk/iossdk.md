# iOS SDK

在使用前，请先阅读数据模型的介绍。更多参数接口信息介绍可前往 iOS SDK 使用说明。

## 事件设计表

事件设计表一般是你们对接的同事，针对具体业务需求一起梳理的需要做埋点的 Excel 表。 数据分析工作台分析系统宏观上有两张表，事件表（events）用于记录用户的行为事件，比如 App 启动，App 浏览页面；用户表（users）用于保存用户相关的一些信息，比如个人资料。

| 事件和用户属性设计(示例)                      |                        |             |
| ---------------------------------- | ---------------------- | ----------- |
| 事件表（events）                        | 用户表（users）             |             |
| 事件名（带 $ 符号的为数据分析工作台预置事件，开启全埋点自动采集） | 事件属性                   | 用户属性        |
| App 启动事件( $AppStart )              | 预置事件属性                 | 邮箱( email ) |
| App 退出事件( $AppEnd )                | 预置事件属性                 |             |
| App 浏览页面事件( $AppViewScreen )       | 预置事件属性                 |             |
| App 元素点击事件( $AppClick )            | 预置事件属性                 |             |
| 搜索事件( search )                     | 搜索关键词( searchKeyWord ) |             |

## 集成 SDK

数据分析工作台 SDK 提供了**全埋点**的功能，可以帮您采集四类事件：

| 事件显示名      | 事件名            | 事件描述                          |
| ---------- | -------------- | ----------------------------- |
| App 启动事件   | $AppStart      | App 启动时触发                     |
| App 退出事件   | $AppEnd        | App 退到后台时触发                   |
| App 浏览页面事件 | $AppViewScreen | Controller viewViewAppear 时触发 |
| App 元素点击事件 | $AppClick      | 控件被点击时触发                      |

1、将解压目录下 zallDataSDK 文件夹拖拽到 Xcode 工程内并请勾选 Copy items if needed 选项；

2、项目设置 Build Phase —> Link Binary With Libraries 添加依赖库：libicucore、libsqlite3 和 libz；

3、将zallDataSDK加入动态库链接；

在程序的入口 AppDelegate.m 中引入 `zallDataSDK.h`，并在 `- application:didFinishLaunchingWithOptions:launchOptions` 中调用 `startWithConfigOptions:` 在主线程中初始化 SDK。

```
 #import "zallDataSDK.h"
 ​
 #ifdef DEBUG
 #define SD_SERVER_URL @"<#【测试项目】数据接收地址#>"
 #else
 #define SD_SERVER_URL @"<#【正式项目】数据接收地址#>"
 #endif
 ​
 - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
 ​
     [self initzallDataWithLaunchOptions:launchOptions];
     return YES;
 }
 ​
 - (void)initzallDataWithLaunchOptions:(NSDictionary *)launchOptions {
     // 初始化 SDK，并指定追踪哪些 AutoTrack 事件
     SAConfigOptions *options = [[SAConfigOptions alloc] initWithServerURL:SD_SERVER_URL launchOptions:launchOptions];
     options.autoTrackEventType = zallDataEventTypeAppStart | zallDataEventTypeAppEnd | zallDataEventTypeAppClick | zallDataEventTypeAppViewScreen;
     [zallDataSDK startWithConfigOptions:options];
 ​
     // 设置公共属性
     [[zallDataSDK sharedInstance] registerSuperProperties:@{@"appName": @"HelloSensorsAnalytics"}];
     // 追踪激活事件
     [[zallDataSDK sharedInstance] trackInstallation:@"AppInstall"];
 ​
     // 打通 App 与 H5
     [[zallDataSDK sharedInstance] addWebViewUserAgentSensorsDataFlag];
 }
```

## 埋点示例

### 追踪事件

初始化 SDK 之后，可以在相应业务逻辑处通过 track: 方法追踪用户行为事件，并为事件添加自定义属性（触发的事件会存储到数据分析工作台分析系统的 events 表中）。

```
     // 记录搜索（search ）事件
     [[zallDataSDK sharedInstance] track:@"ViewProduct"
                                  withProperties:@{@"searchKeyWord" : @"数据分析工作台"}];
```

### 设置用户属性

为了更准确地提供针对人群的分析服务，可以使用数据分析工作台分析 SDK 的 set: 等方法设置用户属性，如年龄、性别等。用户可以在留存分析、分布分析等功能中，使用用户属性作为过滤条件，精确分析特定人群的指标。 （设置的用户属性会存储到数据分析工作台分析系统的 users 表中）

```
 // 设置用户的 email
 [[zallDataSDK sharedInstance] set:@{@"email": @"xxx@xxx.xx"}];
```

## 匿名 ID 和登录 ID 关联

### **如何准确的标识用户**

成功关联设备 ID 和登录 ID 之后，用户在该设备 ID 上或该登录 ID 下的行为就会贯通，被认为是一个数据分析工作台 ID 发生的。在进行事件、漏斗、留存等用户相关分析时也会算作一个用户。

关联设备 ID 和登录 ID 的方法虽然实现了更准确的用户追踪，但是也会增加埋点接入的复杂度。所以一般来说，我们建议只有当同时满足以下条件时，才考虑进行 ID 关联：

1、需要贯通一个用户在一个设备上注册前后的行为。

2、需要贯通一个注册用户在不同设备上登录之后的行为

用户在登录前 ，SDK 会分配一个匿名 ID 来标识游客。当用户注册成功或登录成功时调用 login 方法，传入对应的登录 ID ；匿名 ID 会与对应的登录 ID 进行关联，关联成功之后视为同一个用户。 调用时机：注册成功、登录成功 、初始化 SDK（如果能获取到登录 ID）都需要调用 login 方法传入登录 ID。

* 注意：登录 ID 是指可以唯一标识一个用户的 ID，通常是业务数据库里的主键或其它唯一标识

```
 //注册成功、登录成功、初始化SDK后 调用 login 传入登录 ID
 [[SensorsAnalyticsSDK sharedInstance] login:@"你们服务端分配给用户具体的登录 ID"];
```

* 注意: 如果服务端做了埋点，需在用户注册/登录的时候将匿名 ID 传给服务端做用户 ID 关联。 可以通过 `anonymousId` 方法可获取数据分析工作台分析 iOS SDK 分配的 匿名 ID

```
   //在用户注册或登录时，获取当前用户的匿名 ID 传给服务端
   NSString *anonymousId = [[zallDataSDK sharedInstance] anonymousId];
```

### 调试查看埋点数据

**1.3.4.1 查看本地日志**

开启调试模式后，也可以通过 Xcode 控制台查看输出的日志。过滤日志关键字为：`SDLog`，一个事件有两条日志。 日志中如果出现 `【track event】` 字段， 说明此事件已触发，如果出现 `【valid message】` 字段，说明数据已同步到服务端。

## iOS SDK AutoTrack 使用说明

1、自定义页面信息

2、通过代码 track 浏览页面事件

3、通过代码 track 控件点击事件

4、忽略某个页面或某些页面的事件

5、忽略某个类型控件的点击事件

6、忽略某个View的点击事件

7、设置元素ID

8、自定义元素属性

9、开启 React Native 页面控件的自动采集

### 自定义页面信息(`$AppViewScreen`)

对于 App 中的核心页面（ViewController），我们提供了一个 Protocol \`\`：

```
 /** * @abstract * 自动追踪(AutoTrack)中，实现该 Protocal 的 Controller 对象可以通过接口向自动采集的事件中加入属性 * * @discussion * 属性的约束请参考 <code>track:withProperties:</code> */
 @protocol SDAutoTracker
 @required
 -(NSDictionary *)getTrackProperties;
 @end
 ​
 @protocol SDScreenAutoTracker<SAAutoTracker>
 @required
 -(NSString *) getScreenUrl;
 @end
```

当开发者的 Class 继承自 `UIViewController` 并且实现 Protocol `` ` 时，SDK 会将 ``getTrackProperties:`返回的属性（NSDictionary*类型）加入对应页面的`$AppViewScreen`事件的属性中，作为用户访问该页面时的事件属性。例如，用户可以在`getTrackProperties:\` 中返回页面的中文名称:

```
 // 定义 ProductDetailController 实现 Protocol <SDScreenAutoTracker>
 @interface ProductDetailController : UITableViewController<SDScreenAutoTracker>
 @end
 ​
 // 实现 ProductDetailController
 @implementation ProductDetailController
 ​
 // 在页面属性中，增加页面名称(PageName)和商品ID(ProductId)
 - (NSDictionary *)getTrackProperties {
     return @{@"PageName" : @"商品详情页", @"ProductId" : @12345};
 }
 ​
 // 返回页面 UrlSchema
 - (NSString *)getScreenUrl {
     return @"samall://page/product_detail";
 }
 ​
 @end
```

同样地，SDK 会将 `getScreenUrl:` 返回的字符串作为页面的 Url Schema，记录在 `$AppViewScreen` 事件的 `$url` 属性中，并且当用户切换页面时，将前一个页面中的 Url Schema 作为当前页面的 `$AppViewScreen` 事件的 `$referrer` 属性。如前文中样例，用户访问 `ProductDetailController` 对应页面时，事件的 `$url` 属性的值为 `@"samall://page/product_detail"`，记录页面的 Url；当用户离开 `ProductDetailController` 进入下一个页面时，会在新页面的事件属性 `$referrer` 中记录前序页面的 Url `@"samall://page/product_detail"`。

### 通过代码 track 浏览页面事件(`$AppViewScreen`)

开启 AutoTrack 后，SDK 默认会采集 `$AppViewScreen` 事件，如果想自己手动添加该事件，可通过忽略一个或多个默认采集的事件方式忽略该事件，然后调用 `-trackViewScreen:` 接口手动添加。例如：

```
   [[zallDataSDK sharedInstance] trackViewScreen:self.childViewControllers[0]];
```

### 通过代码 track 控件点击事件(`$AppClick`)

开启 AutoTrack 后，SDK 默认会采集 `$AppClick` 事件，如果想自己手动添加该事件，可以调用 `-trackViewAppClick:` 接口手动添加。例如：

```
- (void)buttonAction:(UIButton *)sender {
    [[zallDataSDK sharedInstance] trackViewAppClick:sender];
}
```

### 忽略某个页面或某些页面的事件

如果想忽略某个或某些页面的事件（`$AppClick`、`$AppViewScreen`），可以使用如下方法：

```
NSMutableArray *array = [[NSMutableArray alloc] init];
[array addObject:@"TestViewController"];
[[zallDataSDK sharedInstance] ignoreAutoTrackViewControllers:array];
```

### 忽略某个类型控件的点击事件

如果想忽略某个控件类型及其子类控件的点击事件(`$AppClick`)，可以使用如果方法：

```
[[zallDataSDK sharedInstance] ignoreViewType:[UIButton class]];
```

viewType 可以是 iOS 常见的控件类型及自定义类型，如：`UISwitch`、`UISegmentedControl`、`UIButton`、`UISlider` `UITabBar` 、`UITableView` 、`UICollectionView` 等。

### 忽略某个View的点击事件

如果要忽略某个 view 的点击事件（`$AppClick`），可以使用如下的方法：

```
// UIView+SensorsAnalytics.h

#import <UIKit/UIKit.h>

@interface UIView (zallData)
- (UIViewController *)viewController;

//viewID
@property (assign,nonatomic) NSString* zallDataViewID;

//AutoTrack 时，是否忽略该 View
@property (nonatomic,assign) BOOL zallDataIgnoreView;

//AutoTrack 时，View 的扩展属性
@property (assign,nonatomic) NSDictionary* zallDataProperties;
@end
self.button1.zallDataIgnoreView = YES;
```

### 设置元素ID

为了有效的区分页面元素(view)，建议使用如下方法设置元素 ID：

```
self.button1.zallDataViewID = @"gotoPayButton";
```

## 自定义元素属性

### **基本控件添加自定义属性**

点击view时，如果在发送 `$AppClick` 事件时还需要添加其它属性，可以通过如下方法进行扩展：

```
NSMutableDictionary* p = [[NSMutableDictionary alloc] init];
    [p setValue:@"testValue" forKey:@"testKey"];
    self.button1.zallDataViewProperties = p;
```

当点击 view 后，发送 `$AppClick` 事件时，会把 properties 的内容带上。

### **其他控件添加自定义属性**

对于一些特殊的控件，如 `UITableView` 、`UICollectionView` 我们提供了一个 Protocol \`\`：

```
@protocol SDUIViewAutoTrackDelegate

//UITableView
@optional
-(NSDictionary *) zallData_tableView:(UITableView *)tableView autoTrackPropertiesAtIndexPath:(NSIndexPath *)indexPath;
//UICollectionView
@optional
-(NSDictionary *) zallData_collectionView:(UICollectionView *)collectionView autoTrackPropertiesAtIndexPath:(NSIndexPath *)indexPath;
@end
```

当开发者的 Class 继承自 `UIViewController` 并且添加 Protocol `` ` 后，设置控件的代理 ``sensorsAnalyticsDelegate`,并实现对应的代理方法。当发送`AppClick\` 事件时，会把方法中传入的自定义属性添加上。

以 `UITableView` 为例，添加自定义属性

```
#import "DHPHomeViewController.h"
#import "SensorsAnalyticsSDK.h"


@interface DHPHomeViewController ()<SDUIViewAutoTrackDelegate,UITableViewDelegate,UITableViewDataSource>
@property (nonatomic) UITableView *myTableView;
@end

@implementation DHPHomeViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.

    self.myTableView = [[UITableView alloc]initWithFrame:CGRectMake(0, 0, CGRectGetWidth(self.view.frame), CGRectGetHeight(self.view.frame)) style:UITableViewStylePlain];
    self.myTableView.dataSource = self;
    self.myTableView.delegate = self;
    self.myTableView.zallDataDelegate = self; //设置代理
    [self.view addSubview:self.myTableView];

}
//实现协议中的方法
-(NSDictionary *) zallData_tableView:(UITableView *)tableView autoTrackPropertiesAtIndexPath:(NSIndexPath *)indexPath
{
    return @{@"customProperty":@"ceshi"};
}
```

为了防止内存泄漏，请在 `viewWillDisappear`方法中将 `sensorsAnalyticsDelegate` 设置为 `nil`,代码示例如下：

```
-(void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    self.myTableView.zallDataDelegate = nil;
}
```

设置成功之后，当点击 `UITableView` 中的 `cell` 时，会触发 `$AppClick` 事件，并且会将对应的自定义属性 `customProperty` 添加上。

## 开启 React Native 页面控件的自动采集（`$AppClick`）

1.7.15 及以后版本，iOS SDK 支持对 React Native 页面控件的自动采集，具体操作如下：

对于直接集成源代码的开发者，可以在编译选项 Preprocessor Macros 中定义选项 zall_ANALYTICS_REACT_NATIVE=1 开启
