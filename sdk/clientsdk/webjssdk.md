# Web JS SDK

## JavaScript SDK 快速接入

### 事件设计表

事件设计表一般是由数据分析工作台的分析师和你们对接的同事，针对具体业务需求一起梳理的需要做埋点的 Excel 表。 数据分析工作台宏观上有两张表，事件表（events）用于记录用户的行为事件，比如 Web 浏览页面、Web 元素点击；用户表（users）用于保存用户相关的一些信息，比如个人资料。

| 事件和用户属性设计（示例）                   |                        |                    |
| ------------------------------- | ---------------------- | ------------------ |
| 事件表                             | 用户表                    |                    |
| 事件名（带 $ 符号的为深度数据预置事件，开启全埋点自动采集） | 事件属性                   | 用户属性               |
| 页面浏览事件( $pageview )             | 预置事件属性                 | 预置的用户属性（开启全埋点自动采集） |
| 元素点击事件( $WebClick )             | 预置事件属性                 | 邮箱( email )        |
| 视区停留事件( $WebStay )              | 预置事件属性                 |                    |
| 搜索事件( search )                  | 搜索关键词( searchKeyWord ) |                    |

### 集成SDK

数据分析工作台 SDK 提供了[全埋点](../../technicalguidelines/basicknowledge/preset/clientsdkpreset/webjssdkpreset.md)的功能，可以帮您采集三个事件：

| 事件显示名    | 事件名       | 事件描述                                                                                                                                          |
| -------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Web 浏览页面 | $pageview | 调用 zall.quick('autoTrack'); 方法触发                                                                                                              |
| Web 元素点击 | $WebClick | a button input 被点击时触发，heatmap:{clickmap:'default'} 参数控制（//是否开启点击图，默认 default 表示开启，可以设置 'not_collect' 表示关闭 //需要 JSSDK 版本号大于 1.7）               |
| Web 视区停留 | $WebStay  | window 的 scroll 滚动时触发，heatmap:{scroll_notice_map:'default'} 参数控制（//是否开启触达注意力图，默认 default 表示开启，可以设置 'not_collect' 表示关闭 //需要 JSSDK 版本号大于 1.9.1） |

直接引入初始化代码

CommonJS 方式

AMD 方式

需要采集数据的页面都需要引入这段代码，来实现全埋点的功能，默认采集三个事件即 （页面浏览事件）、​WebClick（元素点击事件，点击图功能）、$WebStay（视区停留事件，触达图），贴异步加载的代码片段。

```
 ​
 ​
 <script>
 ​
 (function(para) {
 ​
  var p = para.sdk_url, n = [para.name](http://para.name/), w = window, d = document, s = 'script',x = null,y = null;
 ​
  w['zallDataAnalytic201505'] = n;
 ​
  w[n] = w[n] || function(a) {return function() {(w[n]._q = w[n]._q || []).push([a, arguments]);}};
 ​
  var ifs = ['track','quick','register','registerPage','registerOnce','clearAllRegister','trackSignup', 'trackAbtest', 'setProfile','setOnceProfile','appendProfile', 'incrementProfile', 'deleteProfile', 'unsetProfile', 'identify','login','logout','trackLink','clearAllRegister'];
 ​
  for (var i = 0; i < ifs.length; i++) {
 ​
   w[n][ifs[i]] = w[n].call(null, ifs[i]);
 ​
  }
 ​
  if (!w[n]._t) {
 ​
   x = d.createElement(s), y = d.getElementsByTagName(s)[0];
 ​
   x.async = 1;
 ​
   x.src = p;
 ​
   w[n].para = para;
 ​
   y.parentNode.insertBefore(x, y);
 ​
  }
 ​
 })({
 ​
    sdk_url: '在 github 下载新版本的 zalldata.min.js ',
 ​
    name: 'zall',
 ​
    server_url:'数据接收地址',
 ​
    heatmap: {
 ​
 •     //是否开启点击图，默认 default 表示开启，自动采集 $WebClick 事件，可以设置 'not_collect' 表示关闭
 ​
 •     clickmap:'default',
 ​
 •     //是否开启触达注意力图，默认 default 表示开启，自动采集 $WebStay 事件，可以设置 'not_collect' 表示关闭
 ​
 •     scroll_notice_map:'not_collect'
 ​
    }
 ​
   }); 
 ​
   zall.quick('autoTrack'); 
 ​
 </script>  
```

将以上代码放入 html 的 head 里面,一般最好放在稍微靠前点的位置。 您在 zall.track() 时，只要保证写在上面引用的代码的下面就可以，不需要等 window.onload 后再执行。

## 数据接收地址server_url获取方式

### 埋点示例

#### **追踪事件**

数据分析工作台 SDK 初始化成功后，即可以通过 zall.track(event_name, properties)记录事件（触发的事件会存储到数据分析工作台系统的 events 表中）：

> **event_name**: string，必选。表示要追踪的事件名。
>
> **properties**: object，可选。表示这个事件的属性。
>
> **callback**: function，可选。表示已经发送完数据之后的回调。

```
 <input type="text" placeholder="输入搜索关键词" id="keyWord"/>
 ​
 <button type="button" id="search">搜索</button>
 ​
 <script>
 ​
 document.getElementById("search").onclick = function(){
 ​
 •    //记录搜索（search）事件
 ​
 •    zall.track("search",{"searchKeyWord":$('searchKeyWord').val()
 ​
   });
 ​
 }
 ​
 </script>
```

#### **设置用户属性**

为了更准确地提供针对人群的分析服务，可以使用数据分析工作台 SDK 的 setProfile() 等方法设置用户属性，如年龄、性别等。用户可以在留存分析、分布分析等功能中，使用用户属性作为过滤条件，精确分析特定人群的指标。 （设置的用户属性会存储到数据分析工作台系统的 users 表中）

```
 zall.setProfile({email:'xxx@xxx.xx'});
```

#### **匿名ID和登录ID关联**

成功关联**设备** **ID** 和**登录** **ID** 之后，用户在该**设备** **ID** 上或该**登录** **ID** 下的行为就会贯通，被认为是一个**内部数据** **ID** 发生的。在进行事件、漏斗、留存等用户相关分析时也会算作一个用户。

关联**设备** **ID** 和**登录** **ID** 的方法虽然实现了更准确的用户追踪，但是也会增加埋点接入的复杂度。所以一般来说，我们建议只有当同时满足以下条件时，才考虑进行 ID 关联：

1、需要贯通一个用户在一个设备上注册前后的行为。

2、需要贯通一个注册用户在不同设备上登录之后的行为。

通过调用 login 方法，传入对应的登录 ID 之后，匿名 ID 与对应的登录 ID 进行关联，关联成功之后可视为同一个用户。 调用时机：注册成功、登录成功 、初始化SDK(如果能获取到 登录 ID )、都需要调用 login 方法传入 用户登录 ID。

zall.login('userID');

获取 Cookie 中的 **distinct_id** ：zall.store.getDistinctId();

由于调用 login 接口之后，distinct_id 会由匿名 id 变成真实 id，所以我们又提供了一个获取 Cookie 中的 **匿名** **id** 的方法： **前端方式：** 增加了获取匿名ID的方法 zall.quick('getAnonymousID'); 返回匿名 id （SDK 版本 1.13.4 及以上支持），调用这个方法时，可能 SDK 还未初始化成功，建议将此方法放在下面代码中。

```
   zall.quick('isReady',function(){
 ​
 •    …… 
 ​
 •    zall.quick('getAnonymousID');
 ​
 •    ……
 ​
   });
```

如果服务端同时使用了数据分析工作台的 SDK，例如 Java SDK，那么需要注意 Java SDK 也提供了用户关联的方法 trackSignUp()，对同一个用户，trackSignUp() 一般情况下建议只调用一次（通常在用户 注册 时调用），用户 登录 前后的行为的关联建议在业务端实现。 当同一个用户的 Distinct Id 发生变化时（一般情况为匿名用户注册行为），可以通过 trackSignUp() 将旧的 Distinct Id 和新的 Distinct Id 关联，以保证用户分析的准确性。例如：

```
   // 匿名 ID 由前端传过来，
 ​
   String anonymousId = "9771C579-71F0-4650-8EE8-8999FA717761";
 ​
   String registerId = "0012345678";
 ​
   //Java 端，在用户注册/登录时，将用户登录 ID 与 匿名 ID 尝试做关联
 ​
   zall.trackSignUp(registerId, anonymousId);
```

后端获取前端 JavaScript SDK 生成的匿名 id 的方式： 可以在 Cookie 里面找到 key 为 zalldata2015jssdkcross 的 value 值然后进行 decodeURIComponent 解码，最后通过 JSON.parse 方法得到一个对象，对象里面的 distinct_id 的值即为所需要的 ID (注意，如果前端已经调用过 login 方法，那么此时 distinct_id 为登录 ID，所以需要先获取 first_id 字段的值，如果获取不到，就去获取 distinct_id 的值)。

## **如何确认数据发送成功**

### **客户有测试项目**

（project=default）的情况下，优先建议客户使用测试项目的数据接收地址向测试项目发数据。

* 页面集成 SDK 初始化代码且开启全埋点，验证数据接收地址，
* 浏览器开发者工具 console 会打印采集的是数据，json 格式，
* 浏览器开发者工具 network 中有 sa.gif 的图片请求，且状态码为 200，
* 数据分析工作台界面中，打开导入中的数据，在浏览器刷新页面，有数据进入，
* 数据分析工作台界面中，打开埋点管理查看，已入库 1 条，证明数据采集成功，
* 数据分析工作台界面中，事件分析查看数据。

### **客户没有测试项目**

客户没有测试项目或者不方便在控制台查看数据的情况，那么需要使用**Debug**模式

* 初始化集成 SDK 时需要添加两个参数，且均为 true
* 打开测试页面，触发埋点事件，会有弹窗。
* 数据分析工作台页面，导入实时查看，Debug 数据中显示的数据为不真实入库的数据，在埋点关联中查询不到。
