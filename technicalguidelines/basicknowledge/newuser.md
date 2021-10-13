# 如何标识新用户

## 新增用户概念

{% hint style="info" %}
Web端新增用户：当日访问页面的独立用户群体中，历史上首次访问该站点的用户被称为新用户。

APP端新增用户：当日启动APP的独立用户群体中，历史上首次启动APP的用户被称为新用户。

小程序端新增用户：当日启动小程序的独立用户群体中，历史上首次启动小程序的用户被称为新用户。
{% endhint %}

## 判断用户是否为当日新增用户

对于用户是否为当日新增用户的判定，需要使用$is_first_day字段，该字段值为true时，则表示该用户为首日触发该事件，该字段值为false时，则表示该用户非首日触发该事件。

该字段属于公共属性，在任何一个事件中都存在该属性，对于不同的设备端，对于该字段值的判定逻辑如下：

### Web端

当一个用户**首次**访问了埋入Web JS SDK的网页时，SDK会设置一个标识，该标识具有时间属性，起始时间为用户访问页面时的时间，终止时间为当日的24点，这段时间会被标记为**首日**，在这段时间内的值都为，时间超过当日点之后，触发的所有事件中的​is_first_day字段值都为false。

### APP端

当一个用户**首次**启动了一个APP之后，该APP所埋入的SDK会在设备本地缓存一个标识，该标识具有时间属性，起始时间为用户启动APP的时间点，终止时间为当日的24点，这段时间会被标记为首日，在这段时间内的值都为，时间超过当日点之后，触发的所有事件中的​is_first_day字段值都为false。

### 小程序端

当一个用户**首次**启动了一个小程序之后，小程序埋入的SDK会在storage中缓存一个标识，该标识具有时间属性，起始时间为用户启动小程序的时间点，终止时间为当日的24点，这段时间会被标记为首日，在这段时间内的值都为，时间超过当日点之后，触发的所有事件中的​is_first_day字段值都为false。

## 通过判断用户是否首次触发某事件来判定新用户

对于不同的客户端，所需判断的事件也有所不同，通过判断这些事件中的字段值，来判断是否为新用户，​is_first_time值为true时，标识该用户为新增用户，值为时，标识该用户非新增用户，需要注意的是​is_first_time字段并非存在于每个事件中，其只存在于**、​AppStart、$MPlaunch**三个事件中。

### Web端

用户首次开始浏览页面时会触发事件，第一次触发的​pageview事件中的字段值为，后续所有该事件的触发​pageview事件中的$is_first_time的字段值都为false。

### APP端

用户首次启动APP时会触发事件，第一次触发的​AppStart事件中的字段值为，后续所有该事件的触发​AppStart事件中的$is_first_time的字段值都为false。

### 小程序端

用户首次启动小程序时会触发事件，第一次触发的​MPlaunch事件中的字段值为，后续所有该事件的触发​AppStart事件中的$is_first_time的字段值都为false。

## 通过获取用户的首次登录时间来判定用户是何时的新用户

首次登录时间存在于User表中，记录该用户第一次登录的时间，该字段的值由SDK统一设置。

### Web端

当集成Web JS SDK后，开启事件自动采集，则用户首次浏览页面时，SDK会调用profile_set_once接口在User表中的$first_visit_time字段中写入首次登录时间。

### APP端

当集成Android/IOS SDK后并且调用trackInstallation接口，在用户首次启动APP时，SDK会在$first_visit_time字段中写入首次登录时间。

### 小程序端

当集成小程序SDK后,开启开启事件自动采集，则用户首次启动小程序时，SDK会调用profile_set_once接口在User表中的$first_visit_time字段中写入首次登录时间。

## 首日标记的检测

在上文中，我们介绍了如何通过字段来判断一个用户是否是首日触发某事件，在判断新用户时，也会通过对启动事件或其他关键事件的​is_first_day字段进行判断来得知该用户是否为新增用户。

在实际的应用中，有一种特殊的情况，就是用户卸载重装了应用，在设备ID不变的情况下，重新安装应用会导致首日标记$is_first_day字段重新变为true，但实际上该用户是老用户，数据分析工作台加入了首日标记的检测机制。

当某事件开始上报且字段为时，会以上报时的为标准获取，同时查询该是否存在记录，如果存在记录，证明该用户并非第一次产生数据，也就是说，该用户不是新增用户，此时会修改​is_first_day字段值为false，如果没有存在记录，证明该用户是第一次产生数据的新用户，此时保持$is_first_day字段值为true不变，并记录下该用户的首次访问时间。