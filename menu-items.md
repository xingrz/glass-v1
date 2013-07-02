Menu Items

Adding built-in menu items
Defining custom menu items
Allowing users to pin your timeline card
Delivering content is only half of the story. Most interesting services also allow users to interact with timeline cards through menu items. Menu items allow users to request actions that are related to the timeline card, and come in two types: built-in menu items and custom menu items.

Built-in menu items provide access to special functionalities provided by Glass, such as reading a timeline card aloud, navigating to a location, sharing an image, or replying to a message:

 
Custom menu items allow your application to expose behavior that is specific to your Glassware, and you can also provide a menu item icon to match your branding.

Adding built-in menu items

You can add built-in menu items to your timeline items by populating the menuItems array when you insert them. To use a built-in menu item, you only need to populate the action of each menuItem.

Note: When using the REPLY or REPLY_ALL built-in menu item, do not require users to speak a limited set of options, such as possible moves in a game or commands for a service. These menu items are intended to capture free form voice input.
Raw HTTP
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "Hello world",
  "menuItems": [
    {
      "action": "REPLY"
    }
  ]
}
Note: The reference documentation contains a detailed description of the available built in actions.
Defining custom menu items

Built-in actions may not always be enough. Many services need to expose their own specific menu items. This is where custom actions come into play.

Create a custom menu item by specifying a menuItem.action of CUSTOM and a menuItem.id. When your user triggers one of your custom menu items, a notification is sent to your service with the menuItem.id populated. This allows you to determine the source of the notification.

You must also populate menuItem.menuValue to specify an iconUrl and displayName that will appear on the glass device.

Raw HTTP
HTTP/1.1 201 Created
Date: Tue, 25 Sep 2012 23:30:11 GMT
Content-Type: application/json
Content-Length: 303

{
  "text": "Hello world",
  "menuItems": [
    {
      "action": "CUSTOM",
      "id": "complete"
      "values": [{
        "displayName": "Complete",
        "iconUrl": "http://example.com/icons/complete.png"
      }]
    }
  ]
}
Note: For best results, use a PNG icon image that is 50 pixels square with a transparent background.
Allowing users to pin your timeline card

You can create a menu item that lets your users pin the timeline card, which permanently displays the timeline card to the left of the main clock card. Users can unpin the card as well, by using the same menu item.

The pinning menu item is a built-in menu item, so all you need to do is provide the TOGGLE_PINNED action for a menuItem.

Raw HTTP
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

----------

除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。

原文最后更新：2013 年 6 月 26 日。
