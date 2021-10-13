# Android SDK

## 事件设计表

事件设计表一般是由数据分析工作台分析师和你们对接的同事，针对具体业务需求一起梳理的需要做埋点的 Excel 表，数据分析工作台分析分析系统宏观上有两张表，事件表（events）用于记录用户的行为事件，比如 App 启动，App 浏览页面；用户表（users）用于保存用户相关的一些信息，比如个人资料。

| 事件和用户属性设计（示例）                |                        |             |
| ---------------------------- | ---------------------- | ----------- |
| 事件表（events）                  | 用户表（users）             |             |
| 事件名（带 $ 符号的为预置事件，开启全埋点自动采集）  | 事件属性                   | 用户属性        |
| App 启动事件( $AppStart )        | 预置事件属性                 | 邮箱( email ) |
| App 退出事件( $AppEnd )          | 预置事件属性                 |             |
| App 浏览页面事件( $AppViewScreen ) | 预置事件属性                 |             |
| App 元素点击事件( $AppClick )      | 预置事件属性                 |             |
| 搜索事件( search )               | 搜索关键词( searchKeyWord ) |             |

## 集成 SDK

### 在AndroidStudio中接入SDK

**第一步：**在 project 级别的 build.gradle 文件中添加 zall Analytics android-gradle-plugin 依赖：

```
 buildscript {
    repositories {
    jcenter()
 }
 dependencies {
    classpath 'com.android.tools.build:gradle:2.2.3'
   //添加数据分析 android-gradle-plugin 依赖
   classpath 'com.zalldata.analytics.android:android-gradle-plugin:1.0.0'
 }
 ​
 allprojects {
   repositories {
   jcenter()
 }
```

**第二步：**在主module的build.gradle文件中添加插件、SDK依赖：

```
 apply plugin: 'com.android.application'
 //添加插件依赖
 apply plugin: 'com.zalldata.analytics.android'
 ​
 dependencies {
        //添加 SDK 依赖
       implementation 'com.zalldata.analytics.android:zallAnalyticsSDK:1.0.0'
 }
 ​
 Android SDK 要求最低版本API21(android5.0)
```

**第三步：**在AndroidManifest.xml中加入权限

```
 <!-- 同步数据需要网络权限 -->
 <uses-permission android:name="android.permission.INTERNET" />
 <!-- 获取网络状态 -->
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
 <!-- 获取运营商信息 -->
 <uses-permission android:name="android.permission.READ_PHONE_STATE" />
 <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
 ​
```

| 权限                   | 用途            |
| -------------------- | ------------- |
| INTERNET             | 允许应用发送统计数据    |
| ACCESS_NETWORK_STATE | 允许应用检测网络状态    |
| READ_PHONE_STATE     | 允许应用获取设备 IMEI |
| ACCESS_WIFI_STATE    | 允许应用获取 MAC 地址 |

* 注意： 如果使用了 `MultiDex` ，请确保 Android SDK 的代码都指定到主 DEX 中。可以通过在 multiDexKeepProguard 里添加如下配置：

```
 -keep class com.zalldata.analytics.android.sdk.** { *; }
```

### 初始化SDK

在Application的onCreate()方法中初始化SDK

```
 private void initzallDataAPI() {
  SDConfigOptions sdConfigOptions = new SDConfigOptions(SA_SERVER_URL);
  sdConfigOptions.enableTrackAppCrash();
  zallDataAPI.sharedInstance(this, sdConfigOptions);
 ​
  // 打开自动采集, 并指定追踪哪些 AutoTrack 事件
  List<zallDataAPI.AutoTrackEventType> eventTypeList = new ArrayList<>();
  // $AppStart
  eventTypeList.add(zallDataAPI.AutoTrackEventType.APP_START);
  // $AppEnd
  eventTypeList.add(zallDataAPI.AutoTrackEventType.APP_END);
  // $AppViewScreen
  eventTypeList.add(zallDataAPI.AutoTrackEventType.APP_VIEW_SCREEN);
  // $AppClick
  eventTypeList.add(zallDataAPI.AutoTrackEventType.APP_CLICK);
  zallDataAPI.sharedInstance(this).enableAutoTrack(eventTypeList);
 }
```

### 埋点示例

#### 追踪事件

可以通过 `track()` 方法追踪用户行为事件，并为事件添加自定义属性（触发的事件会存储到分析系统的 events 表中）。

```
     try {
         JSONObject properties = new JSONObject();
         properties.put("searchKeyWord", "数据分析工作台");// 搜索关键字
         // 记录搜索（search ）事件
         zallDataAPI.sharedInstance().track("search", properties);
     } catch (JSONException e) {
         e.printStackTrace();
     }
```

#### 设置用户属性

为了更准确地提供针对人群的分析服务，可以使用数据分析工作台 SDK 的 profileSet() 等方法设置用户属性，如年龄、性别等。用户可以在留存分析、分布分析等功能中，使用用户属性作为过滤条件，精确分析特定人群的指标。 （设置的用户属性会存储到数据分析工作台系统的 users 表中）

```
     try {
         JSONObject properties = new JSONObject();
         properties.put("email", "xxx@xxx.xx");
         // 设置用户的 email
         zallDataAPI.sharedInstance().profileSet(properties);
     } catch (JSONException e) {
         e.printStackTrace();
     }
```

#### 匿名 ID 和登录 ID 关联

成功关联设备 ID 和登录 ID 之后，用户在该设备 ID 上或该登录 ID 下的行为就会贯通，被认为是一个内部 ID 发生的。在进行事件、漏斗、留存等用户相关分析时也会算作一个用户。

关联设备 ID 和登录 ID 的方法虽然实现了更准确的用户追踪，但是也会增加埋点接入的复杂度。所以一般来说，我们建议只有当同时满足以下条件时，才考虑进行 ID 关联：

1、需要贯通一个用户在一个设备上注册前后的行为。

2、需要贯通一个注册用户在不同设备上登录之后的行为

用户在登录前 ，SDK 会分配一个匿名 ID 来标识游客。当用户注册成功或登录成功时调用 login 方法，传入对应的登录 ID ；匿名 ID 会与对应的登录 ID 进行关联，关联成功之后视为同一个用户。 调用时机：注册成功、登录成功 、初始化 SDK（如果能获取到登录 ID）都需要调用 login 方法传入登录 ID。

* 注意：登录 ID 是指可以唯一标识一个用户的 ID，通常是业务数据库里的主键或其它唯一标识

```
 //注册成功、登录成功、初始化SDK后  调用 login 传入登录 ID
 zallDataAPI.sharedInstance().login("你们服务端分配给用户具体的登录 ID");
```

* 注意：如果服务端做了埋点，需在用户注册/登录的时候将匿名 ID 传给服务端做用户 ID 关联。 可以通过 `getAnonymousId` 方法 获取 SDK 分配的 匿名 ID

```
//在用户注册或登录时，获取当前用户的匿名 ID 传给服务端
String AnonymousId=zallDataAPI.sharedInstance().getAnonymousId();
```

#### 查看本地日志

开启调试模式后，也可以通过 Android Studio Logcat 查看输出的 log（或者通过 enableLog 方法开启 SDK 日志），或用 adb logcat 查看日志。过滤日志关键字为：`SD.`，一个事件有两条日志。 日志中如果出现 `track event` 字段， 说明此事件已触发，如果出现 `valid message` 字段，说明数据已同步到服务端。

## 数据分析工作台分析 Android SDK AutoTrack 使用说明

1、自定义页面信息

2、开启 Fragment 页面浏览事件的自动采集

3、开启部分 Fragment 页面浏览事件的自动采集

4、忽略部分 Fragment 页面浏览事件的自动采集

5、通过代码 track 浏览页面事件

6、通过代码 track 控件点击事件

7、AlertDialog

8、忽略某个页面或某些页面的事件

9、忽略某个类型控件的点击事件

10、忽略某个View的点击事件

11、设置元素ID

12、自定义元素属性

13、注解 @zallDataIgnoreTrackAppViewScreen

14、注解 @zallDataTrackViewOnClick

15、注解 @zallDataTrackEvent

16、开启 React Native 页面控件的自动采集

### 自定义页面信息(`$AppViewScreen`)

对于 App 中的核心页面（Activity、Fragment），我们提供了一个 接口 `ScreenAutoTracker`：

```
public interface ScreenAutoTracker {
    /** * 返回当前页面的Url * 用作下个页面的referrer * @return String */
    String getScreenUrl();

    /** * 返回自定义属性集合 * 我们内置了一个属性:$screen_name,代表当前页面名称, 默认情况下,该属性会采集当前Activity的CanonicalName,即: * activity.getClass().getCanonicalName(), 如果想自定义页面名称, 可以在Map里put该key进行覆盖。 * 注意:screen_name的前面必须要要加"$"符号 * * @return JSONObject * @throws JSONException JSONException */
    JSONObject getTrackProperties() throws JSONException;
}
```

当用户实现该接口时，SDK 会将 `getTrackProperties` 返回的属性（JSONObject类型）加入 `$AppViewScreen` 事件的属性中，作为用户访问该页面时的事件属性；SDK 会将 `getScreenUrl` 返回的字符串作为页面的 Url Scheme，记录在 `$AppViewScreen` 事件的 `$url` 属性中，并且当用户切换页面时，将前一个页面中的 Url Schema 作为当前页面的 `$AppViewScreen` 事件的 `$referrer` 属性。

例如：

```
public class OrderDetailActivity extends Activity implements ScreenAutoTracker {
    @Override
    public String getScreenUrl() {
        return "zalldata://page/order/detail?orderId=888888";
    }

    @Override
    public JSONObject getTrackProperties() throws JSONException {
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("orderId", "888888");
        jsonObject.put("manufacturer", "zalldata");
        return jsonObject;
    }
}
```

### 开启 Fragment 页面浏览事件的自动采集(`$AppViewScreen`)

从 1.7.10 可以使用 `trackFragmentAppViewScreen` 方法开启 Fragment 页面浏览事件的自动采集：

```
 //初始化 SDK 之后，开启自动采集 Fragment 页面浏览事件
zallDataAPI.sharedInstance().trackFragmentAppViewScreen();
```

开启了 Fragment 浏览页面的自动采集 ，可以通过注解 @zallDataIgnoreTrackAppViewScreen 忽略 Fragment 所在 Activity 的页面浏览事件；如果某些 Fragment 的页面浏览事件也不想采集，也可以通过此注解来忽略掉。

如果不想使用自动采集的 Fragment 浏览页面，可以通过代码埋点在 tab 切换 Fragment 时触发页面浏览事件：

```
 //通过代码触发 Fragment 页面浏览事件
zallDataAPI.sharedInstance().trackViewScreen(fragment);
```

### 开启部分 Fragment 页面浏览事件的自动采集(`$AppViewScreen`)

通过 `enableAutoTrackFragment` 方法，指定只采集某些 Fragment 页面的信息

```
//初始化 SDK 之后，开启自动采集 Fragment 页面浏览事件
zallDataAPI.sharedInstance().trackFragmentAppViewScreen();
//只采集 HomeFragment 页面的浏览事件
zallDataAPI.sharedInstance().enableAutoTrackFragment(HomeFragment.class);
```

### 忽略部分 Fragment 页面浏览事件的自动采集(`$AppViewScreen`)

从 v3.2.6 开始可以通过如下方法，指定不采集某些 Fragment 页面的信息

```
//忽略单个 Fragment
zallDataAPI.sharedInstance().ignoreAutoTrackFragment(Class<?> fragment);
//忽略多个 Fragment
zallDataAPI.sharedInstance().ignoreAutoTrackFragments(List<Class<?>> fragmentList);
```

从 v3.2.6 开始可以通过如下方法，恢复被忽略的 Fragment 页面浏览事件的自动采集

```
//忽略单个 Fragment
zallDataAPI.sharedInstance().resumeIgnoredAutoTrackFragment(Class<?> fragment);
//忽略多个 Fragment
zallDataAPI.sharedInstance().resumeIgnoredAutoTrackFragments(List<Class<?>> fragmentList);
```

### 通过代码 track 浏览页面事件(`$AppViewScreen`)

```
public void trackViewScreen(String url, JSONObject properties) 
```

此方法用于 Activity 内 切换页面的时候调用，用于记录 $AppViewScreen 事件.

> **url ：**页面的 url， 记录到 $url 字段中(如果不需要此属性，可以传 null )。
>
> **properties ：**页面的属性。

* 注意：为保证记录到的 `$AppViewScreen` 事件和 Auto Track 采集的一致，页面的属性中 需要传入 `$title`（页面的title） 、`$screen_name` (页面的名称，即 包名.类名)字段。

```
  try {
       JSONObject properties = new JSONObject();
       //记录打开 首页Fragment 的 $AppViewSceen 事件。
       properties.put("$title", "首页")
                 .put("$screen_name", "cn.zalldata.demo.HomeFragment");
       zallDataAPI.sharedInstance().trackViewScreen(null, properties);
  } catch (Exception e) {
       e.printStackTrace();
  }
```

从 v1.7.9 开始也可以使用下面方法来触发浏览页面事件：

```
//触发 Activity 的浏览页面事件
public void trackViewScreen(Activity activity) //触发 Fragment 的浏览页面事件 public void trackViewScreen(Fragment fragment) 
```

### 通过代码 track 控件点击事件

从 v3.2.6 开始可以使用以下方法来手动采集 `$AppClick` 事件，并且该接口不受忽略等条件的限制：

```
//触发该 View 的点击事件
public void trackViewAppClick(View view) //触发该 View 的点击事件，并加上自定义属性 public void trackViewAppClick(View view, JSONObject properties) 
```

### AlertDialog

对于 `AlertDialog`（ `android.app.AlertDialog`、`android.support.v7.app.AlertDialog`），需要给 `Dialog` 设置 `OwnerActivity`，才能采集到 `AlertDialog` 所属页面 (Activity) 的信息（属性：`$screen_name`、`$title`）。

如果是调用dialog.show()显示dialog：

```
dialog.setOwnerActivity(activity);
```

如果是调用builder.show()显示dialog

```
builder.show().setOwnerActivity(activity);
```

### 忽略某个页面或某些页面的事件

如果想忽略某个或某些页面的事件（`$AppClick`、`$AppViewScreen`），可以使用如下方法：

```
//忽略单个页面
zallDataAPI.sharedInstance().ignoreAutoTrackActivity(MainActivity.class);

//忽略多个页面
List<Class<?>> classList = new ArrayList<>();
classList.add(MainActivity.class);
zallDataAPI.sharedInstance().ignoreAutoTrackActivities(classList);
```

### 忽略某个类型控件的点击事件

如果想忽略某个控件类型及其子类控件的点击事件(`$AppClick`)，可以使用如下方法：

```
zallDataAPI.sharedInstance().ignoreViewType(Class viewType);
```

viewType 可以是 Android 常见的控件类型及自定义类型，如：`CheckBox`、`RadioButton`、`Button`、`SwitchCompat`、`Spinner`、`TextView`、`ImageView`、`ImageButton`、`SeekBar`、`RatingBar`、`RadioGroup`、`ExpandableListView`、`Dialog`、`ListView`、`GridView`、`TabHost` 等。

### 忽略某个View的点击事件

如果要忽略某个 view 的点击事件（`$AppClick`），比如某个 view 的点击事件对整个业务分析完全没有任何作用，或者自定义的密码输入键盘等，可以使用如下的方法忽略点击事件：

```
zallDataAPI.sharedInstance().ignoreView(View view);
```

### 设置元素ID

为了有效的区分页面元素 (view)，SDK 默认使用 `android:id` 的值作为点击事件的 `$element_id` 属性，如果元素没有设置 `android:id` 属性，或者设置的不符合要求，可以使用如下方法设置元素 ID：

```
zallDataAPI.sharedInstance().setViewID(View view, String viewID);
```

对于 Dialog，可以使用如下方法：

```
zallDataAPI.sharedInstance().setViewID(android.app.Dialog view, String viewID);
```

或

```
zallDataAPI.sharedInstance().setViewID(android.support.v7.app.AlertDialog view, String viewID);
```

注意 `setViewID` 的优先级高于 `android:id`。

### 自定义元素属性

点击 view 时，如果在发送 `$AppClick` 事件时还需要添加其它属性，可以通过如下方法进行扩展：

```
zallDataAPI.sharedInstance(context).setViewProperties(View view, JSONObject properties);
```

当点击 view 后，发送 `$AppClick` 事件时，会把 properties 的内容带上。

对于 `ExpandableListView`，可以通过 `setViewProperties` 方法对 item 所在的 view 设置扩展属性，也可以通过 Adapter 实现 `zallExpandableListViewItemTrackProperties` 接口来扩展属性。

```
package com.zalldata.analytics.android.sdk;

public interface zallExpandableListViewItemTrackProperties {
    /** * 点击 groupPosition、childPosition 处 item 的扩展属性 * @param groupPosition * @param childPosition * @return * @throws JSONException */
    JSONObject getzallChildItemTrackProperties(int groupPosition, int childPosition) throws JSONException;

    /** * 点击 groupPosition 处 item 的扩展属性 * @param groupPosition * @return * @throws JSONException */
    JSONObject getzallGroupItemTrackProperties(int groupPosition) throws JSONException;
}
```

对于 `ListView`、`GridView`，可以通过 `setViewProperties` 方法对 item 所在的 view 设置扩展属性，也可以通过 Adapter 实现 `zallAdapterViewItemTrackProperties` 接口来扩展属性。

```
package com.zalldata.analytics.android.sdk;

public interface zallAdapterViewItemTrackProperties {
    /** * 点击 position 处 item 的扩展属性 * @param position * @return * @throws JSONException */
    JSONObject getzallItemTrackProperties(int position) throws JSONException;
}
```

### 注解 @zallDataIgnoreTrackAppViewScreen

忽略某个 Activity 或 Fragment 的页面浏览事件(`$AppViewScreen`)

例如：忽略 `DemoActivity` 的页面浏览事件

```
//忽略 DemoActivity 的页面浏览事件
@zallDataIgnoreTrackAppViewScreen
public class DemoActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_demo);
    }
}
```

### 注解 @zallDataTrackViewOnClick

如果你是使用 `android:onclick` 属性给 View 添加点击处理函数，此时你可以在处理函数上使用 `@zallDataTrackViewOnClick` 注解，在该函数执行时，SDK 会发 `$AppClick` 事件。

例如：

XML:

```
<Button android:id="@+id/button" android:layout_width="match_parent" android:layout_height="wrap_content" android:text="普通Button" android:onClick="buttonOnClick" android:textAllCaps="false" />
```

处理函数：

```
import com.zalldata.analytics.android.sdk.zallDataTrackViewOnClick;

@zallDataTrackViewOnClick
public void buttonOnClick(View v) {

}
```

### 注解 @zallDataTrackEvent

对于一些简单的场景，比如当某一个方法执行时就 track 一个事件，可以在该方法上使用 `@zallDataTrackEvent` 注解。当该方法执行时，SDK 会 track 一个指定的事件。

*   注意：被注解的方法一定需要 `public` 的才可以

    例如：

```
@zallDataTrackEvent(eventName = "someEventName", properties = "{\"provider\":\"数据分析工作台\",\"number\":100,\"isLogin\":true}")
public void someMethod() {

}
```

这样，每当函数 `someMethod()` 执行时，SDK 就会 track 一个事件，其中事件名称为 `someEventName`，属性有:

```
"provider":"数据分析工作台",
"number":100,
"isLogin":true
```

### 开启 React Native 页面控件的自动采集（`$AppClick`）

1.7.14 及以后的版本， 支持在初始化 SDK 之后，通过 `enableReactNativeAutoTrack()` 方法开启 RN 页面控件点击事件的自动采集。

```
//初始化SDK后，开启 RN 页面控件点击事件的自动采集
zallDataAPI.sharedInstance().enableReactNativeAutoTrack();
```
