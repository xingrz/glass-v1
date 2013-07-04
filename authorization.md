授权请求
==========

Requests to the Google Mirror API must be authorized using OAuth 2.0 credentials. 
You should use server-side flow when your application needs to access Google APIs 
on behalf of the user such as when the user is offline. This approach requires 
passing a one-time authorization code from your client to your server that is 
used to acquire an access and refresh tokens for your server.


## 创建一个 Client ID 和 Client Secret

首先，您需要激活您的应用的 Google Mirror API。您可以在 [Google APIs Console](https://code.google.com/apis/console/) 中完成此操作。

1. 在 [Google APIs Console](https://code.google.com/apis/console/) 中创建一个 API 项目。
2. 在您的 API 项目中选择 **Services** 标签页，并启用 Google Mirror API。
3. 在您的 API 项目中选择 **API Access** 标签页，并点击 **Create an OAuth 2.0 client ID**。
4. 在 **Branding Information** 部分命名您的应用（比如：我的 Glass 服务），然后点击 **Next**。您也可以顺便提供一个产品 Logo。
5. 在 **Client ID Settings** 部分进行下列操作：
  1. 选择 **Web application** 作为 **Application type**。
  2. 点击标题旁边的 **more options** ， **Your site or hostname**。
  3. 在 **Authorized Redirect URIs** 和 **JavaScript origins** 字段中列出您的服务器名称。
  4. 点击 **Create Client ID**。
6. 在 **API Access** 页面找到 **Client ID for Web applications** 并记下 **Client ID** 和 **Client Secret** 的值。


## 处理授权请求

When a user loads your application for the first time, they are presented with 
a dialog to grant permission for your application to access their Google Glass 
account with the requested permission scopes. After this initial authorization, 
the user is only presented with the permission dialog if your app's client ID 
changes or the requested scopes have changed.

### 授权用户

This initial sign-in returns an authorization result object that contains an 
*authorization code* if successful.

### 凭 authorization code 换取 access token

The authorization code is a one-time code that your server can exchange for 
an *access token*. This access token is passed to the Google Mirror API to 
grant your application access to user data for a limited time.

If your application requires `offline` access, the first time your app exchanges 
the authorization code, it also receives a *refresh token* that it uses to 
receive a new access token after a previous token has expired. Your application 
stores this refresh token (generally in a database on your server) for later use.

> **重要提示:** Always store user refresh tokens. If your application needs a 
  new refresh token it must sent a request with the `approval_prompt` query 
  parameter set to `force`. This will cause the user to see a dialog to grant 
  permission to your application again.
  
The following code samples demonstrate exchanging an authorization code for an 
access token with offline access and storing the refresh token.

_The `getCredentials` function retrieves OAuth 2.0 Credentials starting from the 
provided authorization code. If no refresh token could be retrieved, an exception 
is raised along with an authorization URL to redirect the user to in order to 
request offline access._

```php
<?php
require_once 'google-api-php-client/src/Google_Client.php';
require_once "google-api-php-client/src/contrib/Google_Oauth2Service.php";
session_start();
// ...

$CLIENT_ID = '<YOUR_CLIENT_ID>';
$CLIENT_SECRET = '<YOUR_CLIENT_SECRET>';
$REDIRECT_URI = '<YOUR_REGISTERED_REDIRECT_URI>';
$SCOPES = array(
    'https://www.googleapis.com/auth/glass.timeline',
    'https://www.googleapis.com/auth/userinfo.profile');


/**
 * Exception thrown when an error occurred while retrieving credentials.
 */
class GetCredentialsException extends Exception {
  protected $authorizationUrl;

  /**
   * Construct a GetCredentialsException.
   *
   * @param authorizationUrl The authorization URL to redirect the user to.
   */
  public function __construct($authorizationUrl) {
    $this->authorizationUrl = $authorizationUrl;
  }

  /**
   * @return the authorizationUrl.
   */
  public function getAuthorizationUrl() {
    return $this->authorizationUrl;
  }

  /**
   * Set the authorization URL.
   */
  public function setAuthorizationurl($authorizationUrl) {
    $this->authorizationUrl = $authorizationUrl;
  }
}

/**
 * Exception thrown when no refresh token has been found.
 */
class NoRefreshTokenException extends GetCredentialsException {}

/**
 * Exception thrown when a code exchange has failed.
 */
class CodeExchangeException extends GetCredentialsException {}

/**
 * Exception thrown when no user ID could be retrieved.
 */
class NoUserIdException extends Exception {}

/**
 * Retrieved stored credentials for the provided user ID.
 *
 * @param String $userId User's ID.
 * @return String Json representation of the OAuth 2.0 credentials.
 */
function getStoredCredentials($userId) {
  // TODO: Implement this function to work with your database.
  throw new RuntimeException('Not implemented!');
}

/**
 * Store OAuth 2.0 credentials in the application's database.
 *
 * @param String $userId User's ID.
 * @param String $credentials Json representation of the OAuth 2.0 credentials to
                              store.
 */
function storeCredentials($userId, $credentials) {
  // TODO: Implement this function to work with your database.
  throw new RuntimeException('Not implemented!');
}

/**
 * Exchange an authorization code for OAuth 2.0 credentials.
 *
 * @param String $authorizationCode Authorization code to exchange for OAuth 2.0
 *                                  credentials.
 * @return String Json representation of the OAuth 2.0 credentials.
 * @throws CodeExchangeException An error occurred.
 */
function exchangeCode($authorizationCode) {
  try {
    global $CLIENT_ID, $CLIENT_SECRET, $REDIRECT_URI;
    $client = new Google_Client();

    $client->setClientId($CLIENT_ID);
    $client->setClientSecret($CLIENT_SECRET);
    $client->setRedirectUri($REDIRECT_URI);
    $_GET['code'] = $authorizationCode;
    return $client->authenticate();
  } catch (Google_AuthException $e) {
    print 'An error occurred: ' . $e->getMessage();
    throw new CodeExchangeException(null);
  }
}

/**
 * Send a request to the UserInfo API to retrieve the user's information.
 *
 * @param String credentials OAuth 2.0 credentials to authorize the request.
 * @return Userinfo User's information.
 * @throws NoUserIdException An error occurred.
 */
function getUserInfo($credentials) {
  $apiClient = new Google_Client();
  $apiClient->setUseObjects(true);
  $apiClient->setAccessToken($credentials);
  $userInfoService = new Google_Oauth2Service($apiClient);
  $userInfo = null;
  try {
    $userInfo = $userInfoService->userinfo->get();
  } catch (Google_Exception $e) {
    print 'An error occurred: ' . $e->getMessage();
  }
  if ($userInfo != null && $userInfo->getId() != null) {
    return $userInfo;
  } else {
    throw new NoUserIdException();
  }
}

/**
 * Retrieve the authorization URL.
 *
 * @param String $userId User's Google ID.
 * @param String $state State for the authorization URL.
 * @return String Authorization URL to redirect the user to.
 */
function getAuthorizationUrl($userId, $state) {
  global $CLIENT_ID, $REDIRECT_URI, $SCOPES;
  $client = new Google_Client();

  $client->setClientId($CLIENT_ID);
  $client->setRedirectUri($REDIRECT_URI);
  $client->setAccessType('offline');
  $client->setApprovalPrompt('force');
  $client->setState($state);
  $client->setScopes($SCOPES);
  $tmpUrl = parse_url($client->createAuthUrl());
  $query = explode('&', $tmpUrl['query']);
  $query[] = 'user_id=' . urlencode($userId);
  return
      $tmpUrl['scheme'] . '://' . $tmpUrl['host'] . $tmpUrl['port'] .
      $tmpUrl['path'] . '?' . implode('&', $query);
}

/**
 * 使用所提供的 authorization code 获取凭证。
 *
 * This function exchanges the authorization code for an access token and
 * queries the UserInfo API to retrieve the user's Google ID. If a
 * refresh token has been retrieved along with an access token, it is stored
 * in the application database using the user's Google ID as key. If no
 * refresh token has been retrieved, the function checks in the application
 * database for one and returns it if found or throws a NoRefreshTokenException
 * with the authorization URL to redirect the user to.
 *
 * @param String authorizationCode 用于获取 access token 的 authorization code
 * @param String state 为了避免错误而附加在授权 URL 中的状态码
 * @return String OAuth 2.0 凭证的 JSON 表示
 * @throws NoRefreshTokenException 从可用来源无法获取 refresh token
 */
function getCredentials($authorizationCode, $state) {
  $userId = '';
  try {
    $credentials = exchangeCode($authorizationCode);
    $userInfo = getUserInfo($credentials);
    $userId = $userInfo -> getId();
    $credentialsArray = json_decode($credentials, true);
    if (isset($credentialsArray['refresh_token'])) {
      storeCredentials($userId, $credentials);
      return $credentials;
    } else {
      $credentials = getStoredCredentials($userId);
      $credentialsArray = json_decode($credentials, true);
      if ($credentials != null &&
          isset($credentialsArray['refresh_token'])) {
        return $credentials;
      }
    }
  } catch (CodeExchangeException $e) {
    print '授权码交换时发生错误。';
    // Glass 服务应当尝试获取当前会话的用户和凭证
    // 如果不可用，重定向用户到授权 URL
    $e -> setAuthorizationUrl(getAuthorizationUrl($userId, $state));
    throw $e;
  } catch (NoUserIdException $e) {
    print '没有获取到用户 ID。';
  }
  
  // 没有获取到 refresh token
  $authorizationUrl = getAuthorizationUrl($userId, $state);
  throw new NoRefreshTokenException($authorizationUrl);
}
?>
```

_译注：其它语言的示例请参考[原文](https://developers.google.com/glass/authorization)。_

### 授权和保存凭证

When users visit your app after a successful first-time authorization flow, your 
application can use a stored refresh token to authorize requests without prompting 
the end user.

If you have already authenticated the user, your application can retrive the refresh 
token from its database and store the token in a server-side session. If the refresh 
token is revoked or is otherwise invalid, you'll need to catch this and take 
appropriate action.


## 使用 OAuth 2.0 凭证

Once OAuth 2.0 credentials have been retrieved as shown in the previous section, 
they can be used to authorize a Google Mirror API service object and send requests 
to the API.

The following code snippets show how to instantiate and authorize a Google Mirror API 
service object and send a request to the Google Mirror API to retrieve a timeline 
item's metadata.

### 实例化服务对象

以下代码演示了如何实例化一个服务对象并授权它发起 API 请求。

```php
<?php
session_start();

require_once "google-api-php-client/src/Google_Client.php";
require_once "google-api-php-client/src/contrib/Google_MirrorService.php";

// ...

/**
 * 构建一个 Mirror 服务对象。
 *
 * @param string credentials OAuth 2.0 凭证的 JSON 表示
 * @return Google_MirrorService 服务对象
 */
function buildService($credentials) {
  $apiClient = new Google_Client();
  $apiClient -> setUseObjects(true);
  $apiClient -> setAccessToken($credentials);
  return new Google_MirrorService($apiClient);
}

// ...
?>
```

_译注：其它语言的示例请参考[原文](https://developers.google.com/glass/authorization)。_

### Send authorized requests and check for revoked credentials

The following code snippet uses an authorized Google Mirror API service instance and 
sends an authorized `GET` request to the Google Mirror API to retrieve a 
`timeline item`'s metadata.

If an error occurs, the code checks for an HTTP `401` status code, which should be 
handled by redirecting the user to the authorization URL.

More Google Mirror API operations are documented in the [API Reference](reference).

```php
/**
 * 打印一个时间线项的元数据。
 *
 * @param Google_MirrorService $service Mirror 服务实例
 * @param string $itemId 要打印元数据的时间线项的 ID
 */
function printTimelineItem($service, $itemId) {
  try {
    $item = $service -> timeline->get($itemId);

    print "文本: " . $item -> getText() . "\n";
    print "HTML: " . $item -> getHtml() . "\n";
  } catch (apiAuthException) {
    // 凭证已被撤销
    // TODO: 重定向用户到授权 URL，并且/或者从数据库中删除凭证
    throw new RuntimeException('Not implemented!');
  } catch (apiException $e) {
    print "发生错误: " . $e -> getMessage();
  }
}
```

_译注：其它语言的示例请参考[原文](https://developers.google.com/glass/authorization)。_

## 下一步

You can learn more about available API methods in the [API Reference](reference), and you can 
review our end-to-end [Example Apps](quickstart.md) to examine some working code.

----------

_除非特别[说明](https://developers.google.com/readme/policies)，本页内容使用 [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/) 授权，示例代码使用 [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) 授权。_

_[原文](https://developers.google.com/glass/authorization)最后更新：2013 年 6 月 26 日。_
