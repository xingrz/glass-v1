API 快速参考
==========

本 API 参考根据资源类型组织。每种资源都有一种或多种数据表示方式和一种或多种方法。


## 时间线

For Timeline Resource details, see the resource representation page.

<table>
  <tr>
    <th>方法</th>
    <th>HTTP 请求</th>
    <th>描述</th>
  </tr>
  <tr>
    <td colspan="3">URIs relative to https://www.googleapis.com/mirror/v1, unless otherwise noted</td>
  </tr>
  <tr>
    <td>删除</td>
    <td>DELETE /timeline/id</td>
    <td>删除一个时间线项。</td>
  </tr>
  <tr>
    <td>获取</td>
    <td>`GET /timeline/id`</td>
    <td>根据 ID 获取一个时间线项。</td>
  </tr>
  <tr>
    <td>插入</td>
    <td><code>POST https://www.googleapis.com/upload/mirror/v1/timeline</code><br>
        和<br>
        <code>POST /timeline</code></td>
    <td>Inserts a new item into the timeline.</td>
  </tr>
  <tr>
    <td>罗列</td>
    <td>`GET /timeline`</td>
    <td>Retrieves a list of timeline items for the authenticated user.</td>
  </tr>
  <tr>
    <td>修改</td>
    <td>`PATCH /timeline/id`</td>
    <td>Updates a timeline item in place. This method supports patch semantics.</td>
  </tr>
  <tr>
    <td>更新</td>
    <td>`PUT https://www.googleapis.com/upload/mirror/v1/timeline/id`<br>
        和<br>
        `PUT /timeline/id`</td>
    <td>Updates a timeline item in place.</td>
  </tr>
</table>


## 时间线附件


## 订阅


## 地理位置


## 联系人

