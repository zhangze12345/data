# Web JS SDK

## 页面浏览

{% hint style="info" %}
事件ID：$pageview

事件显示名：页面浏览

触发条件：打开一个页面时自动采集
{% endhint %}

### 携带属性

| 属性ID           | 属性显示名  | 属性说明                     | 数据类型   |
| -------------- | ------ | ------------------------ | ------ |
| $is_first_time | 是否首次触发 | 标识用户是否第一次访问网站            | BOOL   |
| $title         | 页面标题   | 页面\<title>标签中的内容         | STRING |
| $url           | 页面地址   | 默认采集                     | STRING |
| $url_path      | 页面路径   |                          | STRING |
| $url_host      | 页面地址域名 | 根据$url属性值解析得到            | STRING |
| $referrer_host | 前向地址域名 | 根据$referre属性值解析，直接访问此值为空 | STRING |
| $referrer      | 前向地址   | 直接访问此值为空                 | STRING |
| $utm_source    | 广告系列来源 | 如果$url有utm相关参数，则采集此属性    | STRING |
| $utm_medium    | 广告系列媒介 | 如果$url有utm相关参数，则采集此属性    | STRING |
| $utm_term      | 广告系列字词 | 如果$url有utm相关参数，则采集此属性    | STRING |
| $utm_content   | 广告系列内容 | 如果$url有utm相关参数，则采集此属性    | STRING |
| $utm_campaign  | 广告系列名称 | 如果$url有utm相关参数，则采集此属性    | STRING |

## 页面元素点击

{% hint style="info" %}
事件ID：$WebClick

事件显示名：页面元素点击

触发条件：点击一个元素/控件/按钮时触发
{% endhint %}

### 携带属性

| 属性ID                | 属性显示名    | 属性说明             | 数据类型   |
| ------------------- | -------- | ---------------- | ------ |
| $title              | 页面标题     | 页面\<title>标签中的内容 | STRING |
| $url                | 页面地址     | 默认采集             | STRING |
| $url_path           | 页面路径     |                  | STRING |
| $url_host           | 页面地址域名   | 根据$url属性值解析得到    | STRING |
| $element_id         | 元素ID     | 元素有ID属性则采集       | STRING |
| $element_content    | 元素内容     | 元素标签中的文本         | STRING |
| $element_name       | 元素名称     | 元素有name属性则采集     | STRING |
| $element_class_name | 元素样式名称   | 元素有class属性则采集    | STRING |
| $element_type       | 元素类型     | 默认采集             | STRING |
| $element_selector   | 元素选择器    | 默认采集             | STRING |
| $element_target_url | 元素目标地址   | \<a>标签默认采集       | STRING |
| $element_path       | 元素路径     | 默认采集             | STRING |
| $element_position   | 元素叶子节点序号 |                  | STRING |

## 页面视区停留

{% hint style="info" %}
事件ID：$WebStay

事件显示名：页面视区停留

触发条件：页面滚动时，如果之前的停留为有效停留，则触发此事件
{% endhint %}

### 携带属性

| 属性ID               | 属性显示名   | 属性说明             | 数据类型   |
| ------------------ | ------- | ---------------- | ------ |
| $title             | 页面标题    | 页面\<title>标签中的内容 | STRING |
| $url               | 页面地址    | 默认采集             | STRING |
| $url_path          | 页面路径    |                  | STRING |
| $url_host          | 页面地址域名  | 根据$url属性值解析得到    | STRING |
| $viewport_width    | 视区宽度    | 当前浏览器可视区域宽度      | NUMBER |
| $viewport_position | 视区距顶部距离 | 当前视区距页面顶部距离      | NUMBER |
| $viewport_height   | 视区高度    | 当前浏览器可视区域高度      | NUMBER |
| $event_duration    | 停留时长    | 距离上次Scroll事件时间差  | NUMBER |

### 注意事项

{% hint style="info" %}
有效停留：浏览页面某个区域不发生滚动，持续超过4秒的时间，即视为一次有效停留
{% endhint %}

{% hint style="danger" %}
有效停留中，4秒为一个默认参数，如果需要调整，可以在SDK中进行配置
{% endhint %}

## 所有事件都有的预置属性

| 属性名                         | 默认显示名       | 说明                     | 数据类型   |
| --------------------------- | ----------- | ---------------------- | ------ |
| $lib                        | SDK 类型      | SDK 类型                 | STRING |
| $lib_version                | SDK 版本      | SDK 版本                 | STRING |
| $lib_method                 | 埋点方式        | 埋点方式，目前值统一为 code       | STRING |
| $screen_height              | 屏幕高度        | 屏幕高度，采集的是像素            | NUMBER |
| $screen_width               | 屏幕宽度        | 屏幕宽度，采集的是像素            | NUMBER |
| $url_host                   | 页面地址域名      | 服务端根据$url属性值解析此属性      | STRING |
| $is_first_day               | 是否首日访问      | 表示是否是首日触发事件            | BOOL   |
| $is_login_id                | 是否登录 ID     | 数据入库时判断添加              | BOOL   |
| $ip                         | IP          | 服务端通过解析 HTTP 请求而得到     | STRING |
| $timezone_offset            | 时区偏移量       | 从浏览器的时间对象上直接获取         | NUMBER |
| $country                    | 国家          | 由 IP 解析得到              | STRING |
| $province                   | 省份          | 由 IP 解析得到              | STRING |
| $city                       | 城市          | 由 IP 解析得到              | STRING |
| $latest_referrer            | 最近一次站外地址    | 只要前向域名不是当前页面的域名，就会重置   | STRING |
| $latest_referrer_host       | 最近一次站外域名    | 默认不采集，需要手动设置           | STRING |
| $latest_utm_source          | 最近一次广告系列来源  | 只要$url中包含对应渠道信息就会重置    | STRING |
| $latest_utm_term            | 最近一次广告系列字词  |                        | STRING |
| $latest_utm_content         | 最近一次广告系列内容  |                        | STRING |
| $latest_utm_campaign        | 最近一次广告系列名称  |                        | STRING |
| $latest_utm_medium          | 最近一次广告系列媒介  |                        | STRING |
| $latest_search_keyword      | 最近一次搜索引擎关键词 | 由于各搜索引擎策略不同，可能有获取不到的情况 | STRING |
| $latest_traffic_source_type | 最近一次流量来源类型  |                        | STRING |

## UA信息相关预置属性

| 属性ID             | 属性显示名  | 属性示例           | 数据类型   |
| ---------------- | ------ | -------------- | ------ |
| $manufacturer    | 设备制造商  | Huawei         | STRING |
| $model           | 设备型号   | Huawei mate 40 | STRING |
| $os              | 操作系统   | Android        | STRING |
| $os_version      | 操作系统版本 | Android 10     | STRING |
| $browser         | 浏览器名   | Chrome         | STRING |
| $browser_version | 浏览器版本  | 90             | STRING |
| $bot_name        | 爬虫名称   |                | STRING |

## 预置用户属性



| 属性名                        | 属性显示名      | 属性说明                                    | 属性类型     |
| -------------------------- | ---------- | --------------------------------------- | -------- |
| $first_visit_time          | 首次访问时间     | 新用户首次访问网页时， 会给此属性赋值                     | DATETIME |
| $utm_source                | 首次广告系列来源   | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性   | STRING   |
| $utm_medium                | 首次广告系列媒介   | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性   | STRING   |
| $utm_term                  | 首次广告系列字词   | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性   | STRING   |
| $utm_content               | 首次广告系列内容   | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性   | STRING   |
| $utm_campaign              | 首次广告系列名称   | 新用户首次访问时，通过带有 utm 参数的渠道访问时，才会采集这些用户属性   | STRING   |
| $first_referrer            | 首次前向地址     | 新用户第一次来到有卓尔数科SDK页面时的前向页面地址              | STRING   |
| $first_referrer_host       | 首次前向域名     | 新用户第一次来到有卓尔数科SDK页面时的前向页面域名              | STRING   |
| $first_browser_language    | 首次使用的浏览器语言 | 新用户第一次来到有卓尔数科SDK页面时的浏览器语言               | STRING   |
| $first_browser_charset     | 首次浏览器字符类型  | 新用户第一次来到有卓尔数科SDK页面时的浏览器字符类型             | STRING   |
| $first_search_keyword      | 首次搜索引擎关键词  |                                         | STRING   |
| $first_traffic_source_type | 首次流量来源类型   | 这个的属性值包括：付费广告流量、自然搜索流量、社交网站流量、引荐流量、直接流量 | STRING   |
