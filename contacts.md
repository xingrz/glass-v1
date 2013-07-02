联系人
==========

Contacts can be people or Glassware that users can share timeline items with. By default, Glassware cannot access timeline items that it did not create. Sharing to contacts allows users to share a timeline item with Glassware that did not create that timeline item.

There are two ways that your Glassware can use contacts:

Allow users to share your timeline items with other contacts: Add the SHARE built-in menu item to a timeline card. When users tap the share menu item, Glass displays a list of possible contacts to share with. See the menu items developer guide for more information on how to add built-in menu items.

Allow users to share timeline items with your Glassware: Create a contact that represents your Glassware. When users want to share a timeline card, your contact appears as an option. You can also declare a list of acceptable MIME types so that your contact only appears for cards that you are interested in. To get notified of when users share a timeline card with your contact, you can subscribe to timeline notifications.

Creating a contact

To allow users to share timeline items with your Glassware, insert a contact by POSTing a JSON representation of a contact to the insert REST endpoint.

All contacts must specify an id, which identifies the contact to the Glassware receiving the notifications. You must also specify a displayName and at least one imageUrls, which Glass uses to display the contact information to the user.

Note: For best results, use a PNG icon image that is 640 by 360 pixels with a transparent background for imageUrls.
Raw HTTP
POST /mirror/v1/contacts HTTP/1.1
Authorization: Bearer {auth token}
Content-Type: application/json
Content-Length: {length}

{
  "id": "harold"
  "displayName": "Harold Penguin",
  "iconUrl": "https://developers.google.com/glass/images/harold.jpg"
  "priority": 7
}
To find out more about how to handle notifications from shared timeline items,

How sharing works

Once you create a sharing contact, sharing timeline cards follows this general flow:

A user taps a timeline item, selects the Share menu item, and selects your contact.
The Mirror API creates a copy of the shared timeline card, gives your contact access, and inserts the copy into the user's timeline. Your Glassware cannot access the original timeline card.
If you subscribed to share notifications, you receive a payload containing the timeline card's identifying information. You can then retrieve the timeline item with Timeline.get.
You modify the shared timeline card and update the existing timeline card with Timeline.update.
Note: You should add identifying branding for your Glassware to distinguish the copied timeline item from the original timeline item. Always update the copied timeline item instead of inserting a new one.
The following screenshots show how sharing to contacts might look like on Glass.

![](https://developers.google.com/glass/images/glass-screens/clock_320.png)
![](https://developers.google.com/glass/images/glass-screens/hybrid_bundle_flowers_1_320.png)

![](https://developers.google.com/glass/images/glass-screens/hello_world_320.png)

![](https://developers.google.com/glass/images/glass-screens/contact_friends_gplus_320.jpg)

### Captions for shared photos

Users have the ability to share photos with your Glassware with an accompanying caption that they input with speech. The general user flow is:

1. The user taps a timeline item containing a photo, selects the **Share** menu item, and selects your contact.
2. The user taps again within a short period of time to add a caption to the photo.
3. The user speaks a caption.
4. The timeline item is shared with your Glassware as described previously in [How sharing works](#). In addition, the timeline item's [`text`](reference/timeline.md#text) property is set with the user's transcribed caption.

----------

_除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。_

_原文最后更新：2013 年 6 月 26 日。_
