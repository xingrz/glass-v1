菜单项
==========

除了推送内容，大多数有趣的服务同时也允许用户通过菜单项与时间线卡片进行交互。菜单项允许用户对时间线卡片作出相关动作。菜单项有两种：内建菜单项和自定菜单项。

内建菜单项提供对 Glass 特殊功能的访问，比如朗读时间线卡片、导航到一个地点、分享图片或回复信息。

![](https://developers.google.com/glass/images/glass-screens/sms_inbound_640.jpg)

自定菜单项允许您的应用展示特定于您的 Glassware 的行为，您也可以提供符合您的品牌的菜单项图标。


## 添加内建菜单项

您可以通过向 [`menuItems`](reference/timeline.md#menuItems) 数组插入元素来向您的时间线项添加内建菜单项。要使用内建菜单项，您仅需设定每个 `menuItem` 的 [`action`](reference/timeline.md#menuItems.action)。

> **注意:** 当使用 `REPLY` 或 `REPLY_ALL` 内建菜单项时，不要要求用户说出特定的选项，比如游戏中的某些动作或服务中的一些指令。这些菜单项用于捕捉自由的语音输入。

```http
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "嗨！Glass.CM",
  "menuItems": [
    {
      "action": "REPLY"
    }
  ]
}
```

> **注意:** [参考文档](reference/timeline.md#menuItems.action)包含了其它内建动作的详细描述。


## 定义自定菜单项

内建动作总会不够用的，许多服务需要提供他们特定的菜单项，这就需要自定动作。

通过将 `menuItem.action` 设定为 `CUSTOM` 可以建立自定菜单项。当您的用户触发您的其中一个自定菜单项，一个带有 `menuItem.id` 的[通知](subscriptions.md)会被发送到您的服务，允许您区分通知的来源。

您也必须填写 `menuItem.menuValue` 来指定一个将出现在眼镜上的图标地址 `iconUrl` 和显示名称 `displayName`。

```http
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "嗨！Glass.CM",
  "menuItems": [
    {
      "action": "CUSTOM",
      "id": "visit"
      "values": [{
        "displayName": "访问网站",
        "iconUrl": "http://glass.cm/favicon.png"
      }]
    }
  ]
}
```

> **注意:** 使用 50 像素正方形、透明背景的 PNG 图标能够取得最好的效果。


## 允许用户固定您的时间线卡片

您可以创建一个让您的用户固定时间线卡片的菜单项。固定后时间线卡片将一直显示在主时钟卡片的左侧。用户也可以使用同一个菜单项摘下卡片。

固定是一个内建菜单项，因此您仅需对提供一个 `TOGGLE_PINNED` [动作](reference/timeline.md#menuItems.action)的 `menuItem`。

```http
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "您可以固定或摘下这张卡片。",
  "menuItems": [
    {
      "action": "TOGGLE_PINNED"
    }
    ...
  ]
}
```

----------

_除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。_

_[原文](https://developers.google.com/glass/menu-items)最后更新：2013 年 6 月 26 日。_
