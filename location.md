地理位置
==========

You can use the Google Mirror API to observe the user's location in timeline 
items, request their [last known location](reference/locations/get.md) directly, and subscribe to periodic 
location updates. You can also deliver pre-rendered map images in timeline 
cards by giving the Mirror API the coordinates to draw.

> Note: Retrieving users' location requires the additional 
  `https://www.googleapis.com/auth/glass.location` scope.


## Retrieving the latest known location

To [retrieve](reference/locations/get.md) the latest known location for 
the current user, send a `GET` request to the REST endpoint:

```http
GET /mirror/v1/locations/ HTTP/1.1
Authorization: Bearer {auth token}
```


## Subscribing to location updates

Similar to [subscribing to timeline updates](subscriptions.md), you can 
subscribe to location updates by subscribing to the `locations` collection.

```http
POST /mirror/v1/subscriptions HTTP/1.1
Authorization: Bearer {auth token}
Content-Type: application/json
Content-Length: {length}

{
  "collection": "locations",
  "userToken": "harold_penguin",
  "verifyToken": "random_hash_to_verify_referer",
  "callbackUrl": "https://example.com/notify/callback"
}
```

> **Note:** At this time, location notifications are sent every 10 minutes.


## Rendering maps on timeline cards

The Google Mirror API can render maps for you and overlay markers and lines 
to signify important places and paths. Use the `glass://map` URI to request 
a map. Here's an example:

```html
<img src="glass://map?w=width&h=height&marker=0;latitude,longitude&marker=1;latitude,longitude&polyline=;latitude,longitude,latitude,longitude"
  width="width"
  height="height"/>
```

> **Note:** Always specify the width and height of the image in the `<img>` 
  tag as well. This prevents reflows as the map image is being rendered.

Here is a description of required parameters:

* `w` - The width in pixels of the returned map image
* `h` - The height in pixels of the returned map image

Only one of the items in the following list are additionally required, but 
you can specify all of them:

* `center` and `zoom` - The center (latitude,longitude) of the map to render 
  at and the zoom level. See [Zoom Levels](https://developers.google.com/maps/documentation/staticmaps/#Zoomlevels) 
  for more information.
* `marker` - Specify the pin markers to draw at the specified coordinates. 
  The marker parameter takes a marker type (`0` indicates a `pin` and `1`, 
  the current location), the latitude coordinate, and the longitude coordinate. 
  The map automatically centers and zooms around the markers you create if 
  you don't explicitly specify `center` and `zoom`.
* `polyline` - Specify the polyline coordinates to represent a path on the 
  map. Each polyline consists of a width and color followed by the vertices 
  in the polyline. For example: `polyline=8,ffff0000;47.6,-122.34,47.62,-122.40` 
  specifies an 8-pixel wide red line between `(47.6,-122.34)` and `(47.62,-122.40)`. 
  The map is automatically centered and zoomed to fit the polyline if you 
  don't explicitly specify `center` and `zoom`.

The following example shows a best practice of how to display a map image with 
some text and what it looks like:

```html
<article>
  <figure>
    <img src="glass://map?w=240&h=360&marker=0;42.369590,
      -71.107132&marker=1;42.36254,-71.08726&polyline=;42.36254,
      -71.08726,42.36297,-71.09364,42.36579,-71.09208,42.3697,
      -71.102,42.37105,-71.10104,42.37067,-71.1001,42.36561,
      -71.10406,42.36838,-71.10878,42.36968,-71.10703"
      height="360" width="240">
  </figure>
  <section>
    <div class="text-auto-size">
      <p class="yellow">12 minutes to home</p><p>Medium traffic on Broadway</p>
    </div>
  </section>
</article>
```

> **Note:** You can omit the color and width of the polyline like in this example. 
  The map is rendered using the default color and width in this situation.

![](https://developers.google.com/glass/images/map_card.png)

----------

_除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。_

_原文最后更新：2013 年 6 月 26 日。_
