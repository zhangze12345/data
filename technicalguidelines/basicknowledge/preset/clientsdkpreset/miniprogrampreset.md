# 微信小程序

## 小程序启动

{% hint style="info" %}
事件ID：$MPLaunch

事件显示名：小程序启动

触发条件：小程序初始化完成时触发或者小程序进入后台一定时间后被微信杀死进程后再次启动小程序时触发。
{% endhint %}

### 携带属性

| 属性ID               | 属性显示名           | 属性说明                                                                                            | 数据类型   |
| ------------------ | --------------- | ----------------------------------------------------------------------------------------------- | ------ |
| $scene             | 启动场景            | 小程序启动场景，[微信小程序官方介绍](https://developers.weixin.qq.com/miniprogram/dev/reference/scene-list.html) | STRING |
| $url_query         | 页面参数            | 例：pages/index/index?props=a，$url_query 是 props=a                                                | STRING |
| $url_path          | 页面路径            | 例：pages/index/index?props=a，$url_path 是 pages/index/index                                       | STRING |
| $utm_campaign      | 广告系列名称          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_source        | 广告系列来源          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_medium        | 广告系列媒介          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_term          | 广告系列字词          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_content       | 广告系列内容          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $is_first_time     | 是否首次触发          | 表示是否首次启动小程序                                                                                     | BOOL   |
| $share_depth       | 分享时的层级          |                                                                                                 | NUMBER |
| $share_distinct_id | 分享时的distinct_id |                                                                                                 | STRING |
| $share_url_path    | 分享时的页面路径        |                                                                                                 | STRING |
| $share_method      | 分享时途径           | 例如：朋友圈分享、转发消息卡片                                                                                 | STRING |

## 小程序展示

{% hint style="info" %}
事件ID：$MPShow

事件显示名：小程序展示

触发条件：小程序启动时，或从后台切换到前台时触发
{% endhint %}

### 携带属性

| 属性ID               | 属性显示名           | 属性说明                                                                                            | 数据类型   |
| ------------------ | --------------- | ----------------------------------------------------------------------------------------------- | ------ |
| $scene             | 启动场景            | 小程序启动场景，[微信小程序官方介绍](https://developers.weixin.qq.com/miniprogram/dev/reference/scene-list.html) | STRING |
| $url_query         | 页面参数            | 例：pages/index/index?props=a，$url_query 是 props=a                                                | STRING |
| $url_path          | 页面路径            | 例：pages/index/index?props=a，$url_path 是 pages/index/index                                       | STRING |
| $utm_campaign      | 广告系列名称          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_source        | 广告系列来源          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_medium        | 广告系列媒介          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_term          | 广告系列字词          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $utm_content       | 广告系列内容          | 微信小程序启动参数中有 utm\_参数就采集                                                                          | STRING |
| $is_first_time     | 是否首次触发          | 表示是否首次启动小程序                                                                                     | BOOL   |
| $share_depth       | 分享时的层级          |                                                                                                 | NUMBER |
| $share_distinct_id | 分享时的distinct_id |                                                                                                 | STRING |
| $share_url_path    | 分享时的页面路径        |                                                                                                 | STRING |
| $share_method      | 分享时途径           | 例如：朋友圈分享、转发消息卡片                                                                                 | STRING |

## 小程序进入后台

{% hint style="info" %}
事件ID：$MPHide

事件显示名：小程序进入后台

触发条件：小程序从前台进入后台时触发
{% endhint %}

### 携带属性

| 属性ID            | 属性显示名 | 属性说明             | 数据类型   |
| --------------- | ----- | ---------------- | ------ |
| $event_duration | 停留时长  | 小程序单次从显示到进入后台的时间 | NUMBER |
| $url_path       | 页面路径  |                  | STRING |

## 小程序页面浏览

{% hint style="info" %}
事件ID：$MPViewScreen

事件显示名：小程序页面浏览

触发条件：打开一个小程序页面时触发
{% endhint %}

### 携带属性

| 属性ID           | 属性显示名  | 属性说明                                                      | 数据类型   |
| -------------- | ------ | --------------------------------------------------------- | ------ |
| $url_query     | 页面参数   | 例：pages/index/index?props=a，$url_query 是 props=a          | STRING |
| $url_path      | 页面路径   | 例：pages/index/index?props=a，$url_path 是 pages/index/index | STRING |
| $utm_campaign  | 广告系列名称 | 微信小程序启动参数中有 utm\_参数就采集                                    | STRING |
| $utm_source    | 广告系列来源 | 微信小程序启动参数中有 utm\_参数就采集                                    | STRING |
| $utm_medium    | 广告系列媒介 | 微信小程序启动参数中有 utm\_参数就采集                                    | STRING |
| $utm_term      | 广告系列字词 | 微信小程序启动参数中有 utm\_参数就采集                                    | STRING |
| $utm_content   | 广告系列内容 | 微信小程序启动参数中有 utm\_参数就采集                                    | STRING |
| $is_first_time | 是否首次触发 | 表示是否首次启动小程序                                               | BOOL   |
| $referrer      | 前向地址   |                                                           | STRING |
| $title         | 页面标题   |                                                           | STRING |

## 小程序分享

{% hint style="info" %}
事件ID：$MPShare

事件显示名：小程序分享

触发条件：小程序用户点击“发送给朋友”时触发
{% endhint %}

### 携带属性

| 属性ID          | 属性显示名  | 属性说明            | 数据类型   |
| ------------- | ------ | --------------- | ------ |
| $share_depth  | 分享时的层级 |                 | NUMBER |
| $url_path     | 页面路径   |                 | STRING |
| $share_method | 分享途径   | 例如：朋友圈分享、转发消息卡片 | STRING |

## 小程序元素点击

{% hint style="info" %}
事件ID：$MPClick

事件显示名：小程序元素点击

触发条件：当 Page 中定义的事件处理函数被触发时上报
{% endhint %}

### 携带属性

| 属性ID             | 属性显示名 | 属性说明                   | 数据类型   |
| ---------------- | ----- | ---------------------- | ------ |
| $element_content | 元素内容  | 需要在组件上设置data-content属性 | STRING |
| $element_id      | 元素ID  | 需要在组件上设置id属性           | STRING |
| $element_name    | 元素名称  | 需要在组件上设置data-name属性    | STRING |
| $element_type    | 元素类型  | 需要在组件上设置data-type属性    | STRING |
| $url_path        | 页面路径  |                        | STRING |

## 小程序收藏

{% hint style="info" %}
事件ID：$MPAddFavorites

事件显示名：小程序收藏

触发条件：对小程序进行收藏动作时触发
{% endhint %}

### 携带属性

| 属性ID      | 属性显示名 | 属性说明 | 数据类型   |
| --------- | ----- | ---- | ------ |
| $url_path | 页面路径  |      | STRING |

### 注意事项

{% hint style="danger" %}
此事件仅有Android端有
{% endhint %}

## 所有事件都有的预置属性

| 属性名                       | 属性显示名           | 属性说明                                                                                                          | 数据类型   |
| ------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------- | ------ |
| $lib                      | SDK 类型          | SDK 类型                                                                                                        | STRING |
| $lib_version              | SDK 版本          | SDK 版本                                                                                                        | STRING |
| $app_id                   | 应用唯一标识          | 微信小程序的 APPID                                                                                                  | STRING |
| $screen_height            | 小程序屏幕高度         | 小程序屏幕高度                                                                                                       | NUMBER |
| $screen_width             | 小程序屏幕宽度         | 小程序屏幕宽度                                                                                                       | NUMBER |
| $model                    | 设备型号            | 不从 UA 解析，直接获取对应设备的值                                                                                           | STRING |
| $manufacturer             | 设备制造商           |                                                                                                               | STRING |
| $os                       | 操作系统            |                                                                                                               | STRING |
| $os_version               | 操作系统版本          |                                                                                                               | STRING |
| $is_first_day             | 是否首日访问          | 从新用户第一次访问到当天的凌晨十二点之间的值都为真，之后都为假                                                                               | BOOL   |
| $is_login_id              | 是否是登录 ID        | 数据入库时判断添加                                                                                                     | BOOL   |
| $longitude                | 经度              | GPS 信息，经度\*106，纬度\*106                                                                                        | NUMBER |
| $latitude                 | 纬度              |                                                                                                               | NUMBER |
| $ip                       | IP              | 后端通过解析 HTTP 请求而得到                                                                                             | STRING |
| $timezone_offset          | 时区偏移量           | 系统的时区                                                                                                         | NUMBER |
| $country                  | 国家              | 由 IP 解析得到                                                                                                     | STRING |
| $province                 | 省份              |                                                                                                               | STRING |
| $city                     | 城市              |                                                                                                               | STRING |
| $network_type             | 网络类型            |                                                                                                               | STRING |
| $browser                  | 浏览器名称，由 UA 解析得到 |                                                                                                               | STRING |
| $browser_version          | 浏览器版本，由 UA 解析得到 |                                                                                                               | STRING |
| $latest_utm_source        | 最近一次广告系列来源      | 启动参数中有 utm_source 参数就采集                                                                                       | STRING |
| $latest_utm_medium        | 最近一次广告系列媒介      | 启动参数中有 utm_medium 参数就采集                                                                                       | STRING |
| $latest_utm_term          | 最近一次广告系列字词      | 启动参数中有 utm_term 参数就采集                                                                                         | STRING |
| $latest_utm_content       | 最近一次广告系列内容      | 启动参数中有 utm_content 参数就采集                                                                                      | STRING |
| $latest_utm_campaign      | 最近一次广告系列名称      | 启动参数中有 utm_campaign 参数就采集                                                                                     | STRING |
| $latest_scene             | 最近一次启动场景        | 最近一次小程序启动的场景，场景值说明可参考 [微信小程序官方介绍](https://developers.weixin.qq.com/miniprogram/dev/reference/scene-list.html) | STRING |
| $latest_share_distinct_id | 最近一次分享者         |                                                                                                               | STRING |
| $latest_share_depth       | 最近一次分享层次        |                                                                                                               | NUMBER |
| $latest_share_url_path    | 最近一次分享路径        |                                                                                                               | STRING |
| $latest_share_method      | 最近一次分享时途径       |                                                                                                               | STRING |

## 预置用户属性

| 属性名               | 属性显示名    | 属性说明                                  | 属性类型     |
| ----------------- | -------- | ------------------------------------- | -------- |
| $first_visit_time | 首次访问时间   | 新用户首次访问网页时， 会给此属性赋值                   | DATETIME |
| $utm_source       | 首次广告系列来源 | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性 | STRING   |
| $utm_medium       | 首次广告系列媒介 | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性 | STRING   |
| $utm_term         | 首次广告系列字词 | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性 | STRING   |
| $utm_content      | 首次广告系列内容 | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性 | STRING   |
| $utm_campaign     | 首次广告系列名称 | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性 | STRING   |
