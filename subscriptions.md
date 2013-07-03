订阅
==========

Mirror API 允许您[订阅通知](reference/subscriptions/insert.md)。通知通常会在用户在[时间线项](reference/timeline)上做出特定动作或用户的地理位置更新时被触发。当您订阅了一个通知后，您需要提供一个回调 URL 来处理这个通知。

> **注意:** 在生产环境中，您的回调 URL 必须支持 SSL。对于开发，我们提供了一个[订阅代理服务器](subscription-proxy.md)来将通知转发到您的非 SSL 开发服务器。


## 接收通知

从 Mirror API 来的通知会以 `POST` 请求的方式发送给订阅接口，包含一个 JSON 请求体。

```json
{
  "collection": "timeline",
  "itemId": "3hidvm0xez6r8_dacdb3103b8b604_h8rpllg",
  "operation": "UPDATE",
  "userToken": "harold_penguin",
  "verifyToken": "random_hash_to_verify_referer",
  "userActions": [
    {
      "type": "<TYPE>",
      "payload": "<PAYLOAD>"
    }
  ]
}
```

假如没有发生错误，您的服务必须向 API 返回一个 `200 OK` HTTP 状态码。如果您的应用返回了一个错误码，Mirror API 可能会尝试向您的服务再次发送通知。

> **注意:** 连接超时时间是 10 秒。如果需要较长的处理事件，请立即返回并在另一条线程上执行处理。


## 通知类型

Mirror API 会根据不同事件发送不同的通知请求。

### 分享的时间线项

用户与您的 Glassware 分享了一个时间线项：

```json
{
  "collection": "timeline",
  "itemId": "3hidvm0xez6r8_dacdb3103b8b604_h8rpllg",
  "operation": "UPDATE",
  "userToken": "harold_penguin",
  "verifyToken": "random_hash_to_verify_referer",
  "userActions": [
    {
      "type": "SHARE"
    }
  ]
}
```

其中 `itemId` 字段是所分享的时间线项的 `ID`，您可以使用它从 [Timeline.get](reference/timeline/get.md) 获取时间线项。一个典型的带照片附件的时间线项：

```json
{
  "id": "3hidvm0xez6r8_dacdb3103b8b604_h8rpllg",
  "attachments": [
      {
          "contentType": "image/jpeg",
          "id": "<ATTACHMENT_ID>"
      }
  ],
  "recipients": [
      {
          "kind": "glass#contact",
          "source": "api:<SERVICE_ID>",
          "id": "<CONTACT_ID>",
          "displayName": "<CONTACT_DISPLAY_NAME>",
          "imageUrls": [
              "<CONTACT_ICON_URL>"
          ]
      }
  ]
}
```

> **注意:** 更多关于和联系人分享内容的信息详见[联系人](reference/contacts.md)。

### 回复

用户使用内建的 `REPLY` 菜单项回复了您的时间线项：

```json
{
  "collection": "timeline",
  "itemId": "3hidvm0xez6r8_dacdb3103b8b604_h8rpllg",
  "operation": "INSERT",
  "userToken": "harold_penguin",
  "verifyToken": "random_hash_to_verify_referer",
  "userActions": [
    {
      "type": "REPLY"
    }
  ]
}
```

其中 `itemId` 属性是该项的 `ID`，包含：

* `inReplyTo` 属性是所回复的时间线项的 `ID`。
* `text` 属性是听写出来的文字。
* `recipients` 属性是所回复的时间线项的 `creator`（如果存在）。

示例：

```json
{
  "kind": "glass#timelineItem",
  "id": "3hidvm0xez6r8_dacdb3103b8b604_h8rpllg",
  "inReplyTo": "3236e5b0-b282-4e00-9d7b-6b80e2f47f3d",
  "text": "This is a text reply",
  "recipients": [
    {
      "id": "CREATOR_ID",
      "displayName": "CREATOR_DISPLAY_NAME",
      "imageUrls": [
        "CREATOR_IMAGE_URL"
      ]
    }
  ]
}
```

### 删除

用户删除了时间线项：

```json
{
  "collection": "timeline",
  "itemId": "3hidvm0xez6r8_dacdb3103b8b604_h8rpllg",
  "operation": "DELETE",
  "userToken": "harold_penguin",
  "verifyToken": "random_hash_to_verify_referer",
  "userActions": [
    {
      "type": "DELETE"
    }
  ]
}
```

其中 `itemId` 属性是被删除项的 ID。该项除了 ID 和 `isDeleted` 属性外将不在包含其它数据。

> **注意:** 如果用户从他们的时间线上删除了一个项，建议您同时在您的服务器上删除对应的内容。

### 自定菜单项被选择

用户选择了您的服务设定的[自定菜单项](menu-items.md)：

```json
{
  "collection": "timeline",
  "itemId": "3hidvm0xez6r8_dacdb3103b8b604_h8rpllg",
  "operation": "UPDATE",
  "userToken": "harold_penguin",
  "userActions": [
    {
      "type": "CUSTOM",
      "payload": "PING"
    }
  ]
}
```

其中 `itemId` 属性是用户所选择的菜单项的 ID。

`userActions` 数组包含了用户在该项上做出的自定动作的列表，您的服务需要处理这些动作。

### 地理位置更新

当前用户有新的地理位置：

```json
{
  "collection": "locations",
  "itemId": "latest",
  "operation": "UPDATE",
  "userToken": "harold_penguin",
  "verifyToken": "random_hash_to_verify_referer"
}
```

当您的 Glassware 收到一个地理位置更新后需要发送一个请求到 [glass.locations.get](reference/locations/get.md) 接口来获取最新的已知地理位置。您的 Glassware 会每 10 分钟收到地理位置更新。

> **注意:** 接收地理位置信息需要 `https://www.googleapis.com/auth/glass.location` 权限。

----------

_除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。_

_原文最后更新：2013 年 6 月 26 日。_
