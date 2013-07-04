时间线项
==========

当用户和时间线进行交互时，他们获取信息的主要方式是时间线项的形式，也就是卡片。时间线卡片显示来自不同 Glassware 的内容，通过前后滑动来翻阅新旧卡片。

您可以从时间线上插入、更新、读取和删除时间线卡片。此外，您还可以在一张时间线卡片中附带其它对象，比如地理坐标或媒体。

> **注意:** 时间线卡片是用户体验的中心，因此在创建时间线项前请先了解 [UI 设计指导](ui-guidelines.md)。

以下是一些排列在用户的时间线上的时间线卡片的例子：

![](https://developers.google.com/glass/images/glass-screens/weather_bundle_1_160.png)
![](https://developers.google.com/glass/images/glass-screens/clock_160.png)
![](https://developers.google.com/glass/images/glass-screens/sms_inbound_160.png)
![](https://developers.google.com/glass/images/glass-screens/picture_uploading_160.png)
![](https://developers.google.com/glass/images/glass-screens/hybrid_bundle_flowers_1_160.png)

关于其它对时间线项的操作，请移步[参考文档](reference/timeline/index.md)。

您也可以通过[菜单项](menu-items.md)向用户提供对时间线卡片执行操作的选项。


## 插入一个时间线项

要[插入](reference/timeline/insert.md)一个时间线项，POST 一个 [JSON 格式表示的时间线项](reference/timeline/index.md) 到 REST 接口。

> **注意:** 时间线项在用户的眼镜上保留 7 天，在 Google 的服务器上保留 30 天。

时间线项中的大多数字段是可选的。最简单的形式是只包含一个简短的文字信息，举个例子：

```http
POST /mirror/v1/timeline HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
Content-Type: application/json
Content-Length: 26

{ "text": "嗨！Glass.CM" }
```

如果成功，您会收到一个 `201 Created` 返回码以及刚才所创建项目的完整副本。对于刚刚的例子，一个成功返回大致上是这个样子：

```http
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
 "kind": "glass#timelineItem",
 "id": "1234567890",
 "selfLink": "https://www.googleapis.com/mirror/v1/timeline/1234567890",
 "created": "2012-09-25T23:28:43.192Z",
 "updated": "2012-09-25T23:28:43.192Z",
 "etag": "\"G5BI0RWvj-0jWdBrdWrPZV7xPKw/t25selcGS3uDEVT6FB09hAG-QQ\"",
 "text": "嗨！Glass.CM"
}
```

插入的项目在用户的时间线上看起来会像这样：

![](https://developers.google.com/glass/images/glass-screens/hello_world_320.png)

### 插入带附件的时间线项

有图有真相，图片能够比文字表达更多信息。因此，您也可以在一个时间线项上附带图像或视频。这是一个创建时间线项同时附带照片的例子：

```http
POST /upload/mirror/v1/timeline HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
Content-Type: multipart/related; boundary="mymultipartboundary"
Content-Length: {length}

--mymultipartboundary
Content-Type: application/json; charset=UTF-8

{ "text": "A solar eclipse of Saturn. Earth is also in this photo. Can you find it?" }
--mymultipartboundary
Content-Type: image/jpeg
Content-Transfer-Encoding: binary

[binary image data]
--mymultipartboundary--
```

一个附带图像的时间线项在眼镜上看起来是这样的：

![](https://developers.google.com/glass/images/glass-screens/photo_attach_saturn_640.png)

> **注意:** 在底层，附件通过 [HTTP Multipart](http://www.w3.org/Protocols/rfc1341/7_2_Multipart.html) 上传。使用 Google APIs 客户端库的[媒体上传](media-upload.md)能够简化此操作。

### 附带视频

如果您要附带视频文件到您的时间线项，推荐您使用流来传输视频，而不是一口气上传整个实体。
Google Mirror API 支持使用 HTTP 实时流，逐步下载以及实时流媒体协议 (RTSP)。

> **注意:** RTSP 通常会被防火墙封锁，如果可能请使用其它选择。

要通过流传输视频，使用 `video/vnd.google-glass.stream-url` 的 `content-type` 附带流媒体的 URL 作为 HTTP multi-part 请求的一部分。例如：

```http
POST /upload/mirror/v1/timeline HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
Content-Type: multipart/related; boundary="mymultipartboundary"
Content-Length: {length}

--mymultipartboundary
Content-Type: application/json; charset=UTF-8

{ "text": "这是一个视频" }
--mymultipartboundary
Content-Type: video/vnd.google-glass.stream-url

http://glass.cm/intro.mp4
--mymultipartboundary--
```

详见[媒体格式支持](http://developer.android.com/guide/appendix/media-formats.html)。


## 读取时间线项

您的服务可以访问它创建的以及分享给它的所有时间线项。以下展示了如何[列出](reference/timeline/list.md)对您的服务可见的时间线项。

```http
GET /mirror/v1/timeline HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
```

您可以使用其它 REST 操作来[读取](reference/timeline/get.md)、[更新](reference/timeline/update.md)或[删除](reference/timeline/delete.md)时间线项。

### 访问附件

您可以通过时间线项名为 [`attachments`](refenence/timeline.md#attachments) 的数组属性访问它的附件，然后通过附件的 [`contentUrl`](reference/timeline.md#attachments.contentUrl) 属性或[附件接口](reference/timeline/attachments/get.md)获得它的二进制数据。

> **注意:** 和其它 API 接口一样，附件内容受 OAuth 2.0 保护。Google API 客户端库通过媒体下载功能提供对附件二进制内容的访问。

```http
GET /mirror/v1/timeline/{itemId}/attachments/{attachmentId} HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
```


## 卡片组

卡片组让您能够将多个相关联的卡片组合起来，并且能够展开和折叠。卡片组与普通时间线卡片的不同之处在于其封面的右上角有一个折角。

用户可以轻触卡片组来将它展开成一个与主时间线独立的子时间线。用户可以前后滑动来查看卡片，或向下滑动返回主时间线。下列图片展示了右上角带有折角的卡片组，以及它下面的两张子卡片。

![](https://developers.google.com/glass/images/glass-screens/hybrid_bundle_flowers_1_320.jpg)

![](https://developers.google.com/glass/images/glass-screens/hybrid_bundle_flowers_2_320.jpg)
![](https://developers.google.com/glass/images/glass-screens/hybrid_bundle_flowers_3_320.jpg)
  
有两种方式可以组合卡片：分页和会话方式。

分页方式用于展示不足以在一张卡片中容纳的内容。分页卡片在卡片组中共用同一个 `timelineId` 和一样的菜单项。要对卡片进行分页，可以指定 [`timelineItem.htmlPages[]`](reference/timeline.md#htmlPages) 属性的值。

会话方式用于展示类似于 Email 会话的卡片。要以会话组织时间线卡片，使用相同的 [`bundleId`](reference/timeline.md#bundleId) 来创建他们。最近一个添加的会作为卡片组封面。

> **注意:** 要将特定时间线项设为卡片组，只需将它的 [`isBundleCover`](reference/timeline.md#isBundleCover) 属性设为 `true`。

----------

_除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。_

_[原文](https://developers.google.com/glass/timeline)最后更新：2013 年 6 月 26 日。_
