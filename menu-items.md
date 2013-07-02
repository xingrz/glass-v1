菜单项
==========

除了推送内容，大多数有趣的服务同时也允许用户通过菜单项与时间线卡片进行交互。菜单项允许用户对时间线卡片作出相关动作。菜单项有两种：内建菜单项和自定菜单项。

内建菜单项提供对 Glass 特殊功能的访问，比如朗读时间线卡片、导航到一个地点、分享图片或回复信息。

![](https://developers.google.com/glass/images/glass-screens/sms_inbound_640.jpg)

自定菜单项允许您的应用展示特定于您的 Glassware 的行为，您也可以提供符合您的品牌的菜单项图标。


## 添加内建菜单项

You can add built-in menu items to your timeline items by populating the 
[`menuItems array`](reference/timeline.md#menuItems) when you insert them. 
To use a built-in menu item, you only need to populate the [`action`](reference/timeline.md#menuItems.action) 
of each `menuItem`.

> **Note:** When using the `REPLY` or `REPLY_ALL` built-in menu item, do not 
require users to speak a limited set of options, such as possible moves in a 
game or commands for a service. These menu items are intended to capture free 
form voice input.

```http
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "Hello Glass.CM",
  "menuItems": [
    {
      "action": "REPLY"
    }
  ]
}
```

> **Note:** The [reference documentation](reference/timeline.md#menuItems.action) 
contains a detailed description of the available built in actions.


## 定义自定菜单项

Built-in actions may not always be enough. Many services need to expose their own 
specific menu items. This is where custom actions come into play.

Create a custom menu item by specifying a `menuItem.action` of `CUSTOM` and a 
`menuItem.id`. When your user triggers one of your custom menu items, a [notification](subscriptions.md) 
is sent to your service with the `menuItem.id` populated. This allows you to 
determine the source of the notification.

You must also populate `menuItem.menuValue` to specify an `iconUrl` and `displayName` 
that will appear on the glass device.

```http
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "Hello Glass.CM",
  "menuItems": [
    {
      "action": "CUSTOM",
      "id": "complete"
      "values": [{
        "displayName": "Complete",
        "iconUrl": "http://glass.cm/icons/complete.png"
      }]
    }
  ]
}
```

> **Note:** For best results, use a PNG icon image that is 50 pixels square 
with a transparent background.


## 允许用户固定您的时间线卡片

您可以创建一个让您的用户固定时间线卡片的菜单项。固定后时间线卡片将一直显示在主时钟卡片的左侧。用户也可以使用同一个菜单项摘下卡片。

固定是一个内建菜单项，因此您要做的只是对一个 `menuItem` 提供 `TOGGLE_PINNED` [动作](reference/timeline.md#menuItems.action)。

```http
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "You can pin or unpin this card.",
  "menuItems": [
    {
      "action": "TOGGLE_PINNED"
    }
    ...
  ]
}
```

----------

除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。

原文最后更新：2013 年 6 月 26 日。
