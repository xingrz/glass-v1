Timeline Items

Inserting a timeline item
Inserting a timeline item with an attachment
Attaching video
Reading timeline items
Accessing attachments
Bundling cards
When users interact with their timeline, the main way they receive information is in the form of timeline items, otherwise known as cards. Timeline cards display content from various Glassware and swiping forward and backward on Glass reveals more cards in the past and future.

You can insert, update, read, and delete timeline cards from a timeline. In addition, you can attach objects to a timeline card, such as a location or media.

Note: Because timeline cards are central to the user experience, understand the UI guidelines before creating timeline items.
Here is an example of a set of timeline cards arranged on a user's timeline:

        
For a full list of possible operations for timeline items, see the reference documentation.

You can provide options for users to execute actions on timeline cards with menu items.

Inserting a timeline item

To insert a timeline item, POST a JSON representation of a timeline item to the REST endpoint.

Note: Timeline items last for seven days on a user's Glass and 30 days on Google's servers.
Most of the fields in a timeline item are optional. In its simplest form, a timeline item contains only a short text message, like in this example:

Raw HTTPJavaPython
POST /mirror/v1/timeline HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
Content-Type: application/json
Content-Length: 26

{ "text": "Hello world" }
On success, you receive a 201 Created response code with a full copy of the created item. For the previous example, a successful response might look like this:

Raw HTTP
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
 "text": "Hello world"
}
The inserted item that would appear in the user's timeline looks like this:


Inserting a timeline item with an attachment

A picture is worth a thousand words, which is a lot more than you can fit into a timeline item. To this end, you can also attach images and video to a timeline item. Here's an example of how to insert a timeline item with a photo attachment:

Raw HTTPJavaPython
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
A timeline item with an attached image looks something like this on Glass:


Note: At a low level, attachments are uploaded using HTTP Multipart. Google APIs client libraries make this easy using media upload.
Attaching video

If you are attaching video files to your timeline items, we recommend that you stream the video instead of uploading the entire payload at once. The Google Mirror API supports streaming with HTTP live streaming, progressive download, and the real time streaming protocol (RTSP).

Note: RTSP is frequently blocked by firewalls, so use the other options when possible.
To stream video, attach the URL to the streaming video with a content-type of video/vnd.google-glass.stream-url as part of a HTTP multi-part request. For example:

POST /upload/mirror/v1/timeline HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
Content-Type: multipart/related; boundary="mymultipartboundary"
Content-Length: {length}

--mymultipartboundary
Content-Type: application/json; charset=UTF-8

{ "text": "Skateboarding kittens" }
--mymultipartboundary
Content-Type: video/vnd.google-glass.stream-url

http://example.com/path/to/kittens.mp4
--mymultipartboundary--
See supported media formats for a list of supported media types.

Reading timeline items

Your service can access all timeline items that it created, and all timeline items that were shared with it. Here's how to list the timeline items that are visible to your service.

Raw HTTPJavaPython
GET /mirror/v1/timeline HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
You can use other REST operations to get, update and delete timeline items.

Accessing attachments

You can access attachments to a timeline item through an array property named attachments. You can then obtain the binary data of an attachment through the contentUrl property of the attachment or with the attachments endpoint.

Note: The attachment content is protected by OAuth 2.0, just like other calls to the API endpoints. Google API client libraries provide access to the binary content of attachments using the media download feature.
Raw HTTPJava
GET /mirror/v1/timeline/{itemId}/attachments/{attachmentId} HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer {auth token}
Bundling cards

Bundling allows you to combine many related cards into a bundle cover card that is expandable and collapsible. Bundles are distinguished from normal timeline cards by a page curl in the upper right corner of the bundle's cover card.

A user taps on a bundle cover card to expand the bundled cards into a sub-timeline that is independent of the main timeline. The user can swipe back and forth on the sub-timeline to view the cards and can return to the main timeline by swiping down. The following images show the bundle cover card with the page curl at the top right corner and two bundled cards below it.


  
There are two ways to bundle content: paging and threading.

Use paging for content that does not fit on a single timeline card. Paged cards in a bundle all share the same timelineId and therefore have the same set of menu items. To paginate cards, specify values in the timelineItem.htmlPages[] property.

Use threading to bundle related items like email threads. To thread timeline cards, create them with the same value for bundleId. The most recently added item is the bundle's cover card.

Note: To set a specific timeline item as the bundle cover card, set its isBundleCover property to true.
