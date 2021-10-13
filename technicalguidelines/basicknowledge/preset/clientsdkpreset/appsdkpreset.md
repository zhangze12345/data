# App SDK

## App启动

{% hint style="info" %}
事件ID：$AppStart

事件显示名：App启动

触发条件：启动App或从后台切换进入App时触发
{% endhint %}

### 携带属性

| 属性ID                    | 属性显示名   | 属性说明                       | 数据类型   |
| ----------------------- | ------- | -------------------------- | ------ |
| $is_first_time          | 是否首次触发  | 表示是否是首次启动App               | BOOL   |
| $resume_from_background | 是否从后台唤醒 | 是否从后台唤醒                    | BOOL   |
| $screen_name            | 页面名称    | Activity的包名.类名（仅Android端有） | STRING |
| $title                  | 页面标题    | Activity的标题（仅Android端有）    | STRING |

## App退出

{% hint style="info" %}
事件ID：$AppEnd

事件显示名：App退出

触发条件：退出App或App进入后台时触发
{% endhint %}

### 携带属性

| 属性ID            | 属性显示名 | 属性说明                       | 数据类型   |
| --------------- | ----- | -------------------------- | ------ |
| $event_duration | 停留时长  | 表示单次从启动到关闭的时长，单位为秒         | NUMBER |
| $screen_name    | 页面名称  | Activity的包名.类名（仅Android端有） | STRING |
| $title          | 页面标题  | Activity的标题（仅Android端有）    | STRING |

## App浏览页面

{% hint style="info" %}
事件ID：$AppViewScreen

事件显示名：App浏览页面

触发条件：打开一个Activity/ViewController页面时触发
{% endhint %}

### 携带属性

| 属性ID         | 属性显示名 | 属性说明                       | 数据类型   |
| ------------ | ----- | -------------------------- | ------ |
| $screen_name | 页面名称  | Activity的包名.类名（仅Android端有） | STRING |
| $title       | 页面标题  | Activity的标题（仅Android端有）    | STRING |
| $url         | 页面地址  | 自动采集                       | STRING |
| $referrer    | 前向地址  | 自动采集                       | STRING |

### 注意事项

{% hint style="danger" %}
对于Android Fragment默认不会触发，如需采集，需要单独开启
{% endhint %}

## App元素点击

{% hint style="info" %}
事件ID：$AppClick

事件显示名：App元素点击

触发条件：点击控件时触发
{% endhint %}

### 携带属性

| 属性ID              | 属性显示名 | 属性说明                       | 数据类型   |
| ----------------- | ----- | -------------------------- | ------ |
| $screen_name      | 页面名称  | Activity的包名.类名（仅Android端有） | STRING |
| $title            | 页面标题  | Activity的标题（仅Android端有）    | STRING |
| $element_position | 元素位置  | 元素在模块中的位置                  | STRING |
| $element_id       | 元素ID  | Android端默认获取，iOS端需手动设置     | STRING |
| $element_content  | 元素内容  | 控件代码中设置的元素内容               | STRING |
| $element_type     | 元素类型  | 控件的类型                      | STRING |
| $element_selector | 元素选择器 | 记录一个按钮在App中的位置             | STRING |
| $element_path     | 元素路径  | 需要手动开启                     | STRING |

### 控件示例

{% hint style="info" %}
Android端：CheckBox、RadioButton、button、SwitchCompat、Spinner、TextView、ImageView、ImageButton、SeekBar、RatingBar、RadioGroup、MenuItem、ExpandableListView、Dialog、ListView、GridView、TabHost等

iOS端：UIButton、UIBarButtonItem、UISwitch、UISlider、UISegment、UIPageControl、UIStepper、UILabel+手势、UIImageView+手势、UIAlertController、UIMenu等
{% endhint %}

## App激活

{% hint style="info" %}
事件ID：$AppInstall

事件显示名：App激活

触发条件：App 安装后首次打开触发，第二次打开不会再触发
{% endhint %}

### 携带属性

| 属性ID                           | 属性显示名     | 属性说明 | 数据类型   |
| ------------------------------ | --------- | ---- | ------ |
| $browser                       | 浏览器名      |      | STRING |
| $utm_source                    | 广告系列来源    |      | STRING |
| $utm_medium                    | 广告系列媒介    |      | STRING |
| $utm_term                      | 广告系列字词    |      | STRING |
| $utm_content                   | 广告系列内容    |      | STRING |
| $utm_campaign                  | 广告系列名称    |      | STRING |
| $ios_install_source            |           |      | STRING |
| $utm_matching_type             | 渠道追踪匹配模式  |      | STRING |
| $matched_key                   | 渠道匹配关键词   |      | STRING |
| $matching_key_list             | 渠道匹配关键词列表 |      | STRING |
| $channel_active_period_day     | 有效激活窗口期   |      | NUMBER |
| $channel_attribute_period_hour | 激活归因窗口期   |      | NUMBER |

### 注意事项

{% hint style="danger" %}
需要调用 trackInstallation / trackAppInstall 接口采集此事件
{% endhint %}

## App崩溃

{% hint style="info" %}
事件ID：$AppCrashed

事件显示名：App崩溃

触发条件：App崩溃时采集
{% endhint %}

### 携带属性

| 属性ID                | 属性显示名 | 属性说明 | 数据类型   |
| ------------------- | ----- | ---- | ------ |
| $app_crashed_reason | 崩溃原因  |      | STRING |

### 注意事项

{% hint style="danger" %}
只有在开启崩溃采集时才会采集
{% endhint %}

## App被动启动

{% hint style="info" %}
事件ID：$AppStartPassively

事件显示名：App被动启动

触发条件：iOS 端App被系统调起
{% endhint %}

### 携带属性

| 属性ID                    | 属性显示名   | 属性说明                  | 数据类型   |
| ----------------------- | ------- | --------------------- | ------ |
| $app_state              | App状态   | iOS端默认获取，Android端不会获取 | STRING |
| $resume_from_background | 是否从后台唤醒 | 是否从后台唤醒               | BOOL   |
| $is_first_time          | 是否首次触发  | 表示是否是首次启动 App         | BOOL   |

### 注意事项

{% hint style="danger" %}
iOS端App有此事件，Android端无此事件
{% endhint %}

## 远程控制配置变化

{% hint style="info" %}
事件ID：$AppRemoteConfigChanged

事件显示名：远程控制配置变化

触发条件：SDK获取到远程配置触发
{% endhint %}

### 携带属性

| 属性ID               | 属性显示名    | 属性说明              | 数据类型   |
| ------------------ | -------- | ----------------- | ------ |
| $app_remote_config | 远程控制配置信息 | SDK 加载配置生效后，采集该事件 | STRING |

## 深度链接唤醒App

{% hint style="info" %}
事件ID：$AppDeepLinkLaunch

事件显示名：深度链接唤醒App

触发条件：App被DeepLink唤醒时触发
{% endhint %}

### 携带属性

| 属性ID                 | 属性显示名      | 属性说明                 | 数据类型   |
| -------------------- | ---------- | -------------------- | ------ |
| $deeplink_url        | 深度链接地址     | 唤醒 App 的 DeepLink 链接 | STRING |
| $utm_source          | 广告系列来源     |                      | STRING |
| $utm_medium          | 广告系列媒介     |                      | STRING |
| $utm_term            | 广告系列字词     |                      | STRING |
| $utm_content         | 广告系列内容     |                      | STRING |
| $utm_campaign        | 广告系列名称     |                      | STRING |
| $latest_utm_source   | 最近一次广告系列来源 |                      | STRING |
| $latest_utm_medium   | 最近一次广告系列媒介 |                      | STRING |
| $latest_utm_term     | 最近一次广告系列字词 |                      | STRING |
| $latest_utm_content  | 最近一次广告系列内容 |                      | STRING |
| $latest_utm_campaign | 最近一次广告系列名称 |                      | STRING |

## 深度链接匹配结果

{% hint style="info" %}
事件ID：$AppDeeplinkMatchedResult

事件显示名：深度链接匹配结果

触发条件：SDK解析完成DeepLink的配置时触发
{% endhint %}

### 携带属性

| 属性ID                        | 属性显示名      | 属性说明                 | 数据类型   |
| --------------------------- | ---------- | -------------------- | ------ |
| $deeplink_url               | 深度链接地址     | 唤醒 App 的 DeepLink 链接 | STRING |
| $utm_source                 | 广告系列来源     |                      | STRING |
| $utm_medium                 | 广告系列媒介     |                      | STRING |
| $utm_term                   | 广告系列字词     |                      | STRING |
| $utm_content                | 广告系列内容     |                      | STRING |
| $utm_campaign               | 广告系列名称     |                      | STRING |
| $event_duration             | 事件时长       |                      | STRING |
| $deeplink_options           | 深度链接参数     | 网页端配置的深度链接参数         | STRING |
| $deeplink_match_fail_reason | 深度链接匹配失败原因 |                      | STRING |

## App推送点击

{% hint style="info" %}
事件ID：$AppPushClick

事件显示名：App推送点击

触发条件：推送被点击时触发
{% endhint %}

### 携带属性

| 属性ID                   | 属性显示名    | 属性说明 | 数据类型   |
| ---------------------- | -------- | ---- | ------ |
| $app_push_msg_title    | 推送消息标题   |      | STRING |
| $app_push_msg_content  | 推送消息内容   |      | STRING |
| $app_push_service_name | 第三方推送服务商 |      | STRING |
| $app_push_channel      | App推送通道  |      | STRING |

## 所有事件都有的预置属性

| 属性名                    | 属性显示名   | 说明                                                         | 数据类型   |
| ---------------------- | ------- | ---------------------------------------------------------- | ------ |
| $app_id                | 应用唯一标识  | App 的唯一标识                                                  | STRING |
| $app_name              | 应用名称    | 应用的名称                                                      | STRING |
| $app_version           | 应用版本    | APP 的应用版本                                                  | STRING |
| $lib                   | SDK类型   | SDK 类型，比如 Android/iOS                                      | STRING |
| $lib_version           | SDK版本   | SDK 版本                                                     | STRING |
| $manufacturer          | 设备制造商   | 设备制造商                                                      | STRING |
| $brand                 | 设备品牌    | 设备品牌                                                       | STRING |
| $model                 | 设备型号    | 设备型号                                                       | STRING |
| $os                    | 操作系统    | 操作系统                                                       | STRING |
| $os_version            | 操作系统版本  | 操作系统版本                                                     | STRING |
| $referrer_title        | 前向页面标题  | 前一个页面标题                                                    | STRING |
| $screen_height         | 屏幕高度    | 屏幕高度                                                       | NUMBER |
| $screen_width          | 屏幕宽度    | 屏幕宽度                                                       | NUMBER |
| $wifi                  | 是否 WiFi | 事件触发时是否为 WiFi                                              | BOOL   |
| $carrier               | 运营商名称   | 事件触发时设备 SIM 卡的运营商名称                                        | STRING |
| $network_type          | 网络类型    | 事件触发时的网络类型                                                 | STRING |
| $timezone_offset       | 时区偏移量   | App 或系统的时区                                                 | NUMBER |
| $is_first_day          | 是否首日访问  | 是否是首日触发                                                    | BOOL   |
| $is_login_id           | 是否登录 ID | 数据入库时判断添加                                                  | BOOL   |
| $ip                    | IP      | 后端通过解析 HTTP 请求而得到                                          | STRING |
| $country               | 国家      | 由 IP 解析得到                                                  | STRING |
| $province              | 省份      | 由 IP 解析得到                                                  | STRING |
| $city                  | 城市      | 由 IP 解析得到                                                  | STRING |
| $device_id             | 设备ID    | Android 端主要取 Android ID ，iOS 端尝试获取 IDFA                    | STRING |
| $screen_orientation    | 屏幕方向    | 只有在开启 enableTrackScreenOrientation: 时才会采集                  | STRING |
| $latitude              | GPS信息   | <p>纬度*106</p><p>只有在开启 enableTrackGPSLocation: 时才会采集</p>    | NUMBER |
| $longitude             | GPS信息   | <p>经度*106   </p><p>只有在开启 enableTrackGPSLocation: 时才会采集</p> | NUMBER |
| $geo_coordinate_system | 坐标系     | 只有在开启 enableTrackGPSLocation: 时才会采集                        | STRING |

## 预置用户属性

| 属性名                | 属性显示名     | 说明                   | 数据类型     |
| ------------------ | --------- | -------------------- | -------- |
| $first_visit_time  | 首次访问时间    |  新用户首次启动App， 会给此属性赋值 | DATETIME |
| $utm_source        | 首次广告系列来源  |                      | STRING   |
| $utm_medium        | 首次广告系列媒介  |                      | STRING   |
| $utm_term          | 首次广告系列字词  |                      | STRING   |
| $utm_content       | 首次广告系列内容  |                      | STRING   |
| $utm_campaign      | 首次广告系列名称  |                      | STRING   |
| $utm_matching_type | 渠道追踪匹配模式  |                      | STRING   |
| $matched_key       | 渠道匹配关键字   |                      | STRING   |
| $matching_key_list | 渠道匹配关键字列表 | 渠道匹配关键字列表            | STRING   |
