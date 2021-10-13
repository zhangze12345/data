# 小程序SDK

## 微信小程序 SDK 快速接入

### 事件设计表

事件设计表一般是由数据分析工作台分析师和你们对接的同事，针对具体业务需求一起梳理的需要做埋点的 Excel 表。

| 事件和用户属性设计                          |                        |                    |
| ---------------------------------- | ---------------------- | ------------------ |
| 事件表                                | 用户表                    |                    |
| 事件名（带 $ 符号的为数据分析工作台预置事件，开启全埋点自动采集） | 事件属性                   | 用户属性               |
| 小程序启动事件( $MPLaunch )               | 预置事件属性                 | 预置的用户属性（开启全埋点自动采集） |
| 小程序显示事件( $MPShow )                 | 预置事件属性                 | 邮箱( email )        |
| 小程序进入后台事件( $MPHide )               | 预置事件属性                 |                    |
| 小程序页面浏览事件( $MPViewScreen )         | 预置事件属性                 |                    |
| 小程序分享事件( $MPShare )                | 预置事件属性                 |                    |
| 搜索事件( search )                     | 搜索关键词( searchKeyWord ) |                    |

### 引入 SDK

#### **下载文件**

从 Github 上下载微信小程序SDK，zalldata.min.js 。

#### **引入并配置参数**

把文件放在小程序项目中，然后在 app.js 中通过 require() 引入文件 ; 调用 setPara() 方法设置初始化参数

{% hint style="danger" %}
注意：必须在 require() 之后，立即调用 setPara() 方法设置 小程序 app.js 的顶部引入如下代码：
{% endhint %}

```
 ------app.js
 ​
   var sa = require('./utils/zalldata.min.js');
 ​
   sa.setPara({
 ​
 •    name: 'zall',
 ​
 •    server_url: '数据接收地址'
 ​
   });
```

#### **标志初始化完成**

如果异步的获取 openid 等操作已经完成，此时调用 init() 方法标志已完成初始化

{% hint style="danger" %}
注意: init() 方法必须调用，否则不会发数据
{% endhint %}

```
 ------app.js
 ​
   var sa = require('./utils/zalldata.min.js');
 ​
   sa.setPara({
 ​
 •    name: 'zall',
 ​
 •    server_url: '数据接收地址'
 ​
   });
 ​
   sa.init();
```

#### **小程序页面中采集事件**

现在在其他 Page 里就可以通过 getApp() 来使用数据分析工作台的追踪了

```
 var app = getApp();
 app.zall.track(eventName, properties) // 第一个参数事件名 字符串类型，第二个参数 属性值 对象类型
```

## 自定义事件追踪

SDK 初始化成功后，即可以通过 app.zall.track(event_name,properties) 记录事件：

> event_name:string，必选。表示要追踪的事件名。 properties:object，可选。表示这个事件的属性。

例如：埋点 “ViewProduct” 事件，事件属性有商品名称，姓名等。

```
 // 追踪浏览商品事件。
 ​
 var app = getApp();
 ​
 app.zall.track('ViewProduct', {
 ​
   ProductName: "MacBook Pro",
 ​
   ProductPrice: 125.55
 ​
 });
```

### 设置用户属性

SDK 初始化成功后，即可以通过 app.zall.setProfile(properties) 设置用户属性，如果之前存在同名属性则覆盖：

> properties:object，必选。表示要设置的用户的属性。

```
 var app = getApp();
 ​
 app.zall.setProfile({
 ​
   name: 'xxx',
 ​
   gender:'male'
 ​
 });
```

## 用户标识

在进行任何埋点之前，都应当先确定如何标识用户。**distinct_id** 是数据分析工作台用来标识用户的一段唯一的字符串。

在小程序中，会有下面 3 种 id

1、默认情况下，我们会生成一个随机数 uuid ，保存在 weixin storage 中，我们暂时称这个为 **uuid**

2、用户打开小程序，我们可以获得用户的 weixin openid ，我们暂时称这个为 **openid**

3、客户用户账号体系中产生或保存的真实用户 id 。我们暂时称为 "**你们服务端分配给用户具体的登录 ID**"

### **使用 openid 作为匿名 ID**

如果不做任何操作，小程序会使用 uuid 作为 distinct_id ，但是这个 uuid 换了设备，或者删除小程序，会重新生成。 所以一般情况下，我们建议使用 openid 作为当前的 distinct_id。 因为获取 openid 是一个异步的操作，但是冷启动事件等会先发生，这时候这个冷启动事件的 distinct_id 就不对了。 所以我们会把先发生的操作，暂存起来，等获取到 openid 后再调用 zall.init() 才会发数据。 下面是常见的两种获取 openid 的初始化代码。

```
 -------app.js 
  
 var zall = require('zalldata.min.js');
 ​
 zall.setPara({
 ​
   name: '',
 ​
   server_url: '数据接收地址'
 ​
 });
```

```
 //此处代码仅供参考
 ​
 wx.request({
 ​
   url: '获取 openid 的后端请求接口',
 ​
   success: function(data){
 ​
 •    // 如果你们能获取到openid
 ​
 •    zall.setOpenid(openid);
 ​
   },
 ​
   complete: function(){
 ​
 •    zall.init();
 ​
   }
 ​
 });
```

{% hint style="danger" %}
注意：init() 方法不调用就不会发送数据，只会将数据保存到队列中，确保获取 openid 成功和失败的时候都调用 init() 方法
{% endhint %}

```
 -------app.js 
 ​
  
 ​
 var zall = require('zalldata.min.js');
 ​
 // 如果后端做了 appid 和 appsecret 的配置，以及 zalldata_conf 里有 appid 的配置
 ​
 zall.setPara({
 ​
   appid:'',
 ​
   name: '',
 ​
   server_url: '数据接收地址'
 ​
 });
 ​
 zall.initWithOpenid();
```

### **匿名 ID 和用户 ID 关联**

默认情况下 ，SDK 会分配一个匿名 ID (uuid) 来标识游客。当用户注册成功或登录成功时调用 login 方法，传入对应的用户 ID ；匿名 ID 会与对应的用户 ID 进行关联，关联成功之后视为同一个用户。 调用时机：注册成功、登录成功 、初始化 SDK（如果能获取到用户 ID）都需要调用 login 方法传入用户 ID。

```
-------app.js 

 

var zall = require('zalldata.min.js');

zall.setPara({

  name: '',

  server_url: '数据接收地址'

});

// 如果能获取到"你们服务端分配给用户具体的登录 ID"，需要做用户关联情况下

zall.login("你们服务端分配给用户具体的登录 ID");

zall.init();
```

数据分析工作台分析模块查看具体的数据。

## 注意事项

{% hint style="danger" %}
各端埋点时事件名、事件属性类型要保持一致。
{% endhint %}

{% hint style="danger" %}
小程序中数据接收地址 server_url 需要配置到服务器域名中，且必须为 https 协议的
{% endhint %}
