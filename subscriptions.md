订阅
==========

The Mirror API allows you to [subscribe to notifications](reference/subscriptions/insert.md) that are sent when the user takes specific actions on a [Timeline Item](reference/timeline) or when the user location has been updated. When you subscribe to a notification, you provide a callback URL that processes the notification.

> **Note:** In production, your callback URL must support SSL. For development purposes, we provide an [subscription proxy server](subscription-proxy.md) that can forward notifications to your non-SSL development server.


## 接收通知

A notification from the Mirror API is sent as a `POST` request to the subscribed endpoint containing a JSON request body.

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

Your service must respond to the API with a `200 OK` HTTP status code if no error occurred. If your service responds with an error code, the Mirror API might try to resend the notification to your service.

> **Note:** The connection will time out after 10 seconds. If a long process is required, respond right away and do the process in another thread.


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

The `itemId` attribute is set to the `ID` of the item containing:

* `inReplyTo` attribute set to the `ID` of the timeline item it is a reply to.
* `text` attribute set to the text transcription.
* `recipients` attribute set to the `creator` of the timeline item it is a reply to, if it exists.

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

The `itemId` attribute is set to the ID of the deleted item. The item no longer contains metadata other than its ID and the `isDeleted` property.

> **Note:** If the user deletes an item from their timeline, it's recommended that you delete this content from your systems too.

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

The `itemId` attribute is set to the ID of the menu item that the user selected.

The `userActions` array contains the list of custom actions that the user took on this item. Your service should handle those actions accordingly.

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

When your Glassware receives a location update, send a request to the [glass.locations.get](reference/locations/get.md) endpoint to retrieve the latest known location. Your Glassware receives location updates every ten minutes.

> **注意:** 接收地理位置信息需要 `https://www.googleapis.com/auth/glass.location` 权限。

----------

_除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。_

_原文最后更新：2013 年 6 月 26 日。_
