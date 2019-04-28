WebSocketClient
===============

* [构造函数](#构造函数)
* [配置信息](#Client配置信息)
* [方法](#方法)
* [事件](#事件)
* **实例**
  * [使用代理服务器连接](#使用代理服务器连接)

`var WebSocketClient = require('websocket').client`

这个类可以让 Client 连接到WeSocket的Server端

*-->  This object allows you to make client connections to a WebSocket server.*

构造函数
-----------
```javascript
new WebSocketClient([clientConfig]);
```

Client配置信息
---------------------
**webSocketVersion** - uint - *默认版本: 13*  
建立连接时，使用WebSocket的协议版本。当前支持的版本8和13.
一旦IETF落实协议，这个配置项将要被废除，那是仅有且可用的缓解过渡的中间草案协议。唯一影响的是Origin的header信息。  
*--> Which version of the WebSocket protocol to use when making the connection.  Currently supported values are 8 and 13.
This option will be removed once the protocol is finalized by the IETF It is only available to ease the transition through the intermediate draft protocol versions. The only thing this affects the name of the Origin header.*

**maxReceivedFrameSize** - uint - *默认大小: 1MiB*  
能接收的最大字节单位的帧值。单帧的消息也将受到这个限制。  
*--> The maximum allowed received frame size in bytes.  Single frame messages will also be limited to this maximum.*

**maxReceivedMessageSize** - uint - *默认大小: 8MiB*  
碎片消息的最大字节单位的聚合消息大小  
*--> The maximum allowed aggregate message size (for fragmented messages) in bytes.*
            
**fragmentOutgoingMessages** - Boolean - *默认开关: true*  
是否开启碎片化输出消息。true：消息将被自动碎片化成块 `fragmentationThreshold` 字节。    
Whether or not to fragment outgoing messages.  If true, messages will be automatically fragmented into chunks of up to `fragmentationThreshold` bytes.
            
**fragmentationThreshold** - uint - *默认大小: 16KiB*  
在自动碎片化之前，最大的帧字节大小。  
The maximum size of a frame in bytes before it is automatically fragmented.

**assembleFragments** - boolean - *默认开关: true*  
ture: 碎片化消息将被聚合，并且，完整的消息将被通过一个 `message` 事件进行发射。  
false: 每个碎片兼将被发射，通过WebSocketConnection对象的`frame`事件，并且，由应用来聚合这些碎片帧。  
Single-frame 消息将被发射 通过给`frame`事件附加的`message`事件。  
大多数用户将选择让其保持默认值。  

*--> If true, fragmented messages will be automatically assembled and the full message will be emitted via a `message` event. If false, each frame will be emitted on the WebSocketConnection object via a `frame` event and the application will be responsible for aggregating multiple fragmented frames.  Single-frame messages will emit a `message` event in addition to the `frame` event. Most users will want to leave this set to `true`.*

**closeTimeout** - uint - *默认时长: 5000*  
关闭等待时长(ms)，在发送acknowledgement之后，acknowledgement：在放弃和关闭套接字之前的标识   

*--> The number of milliseconds to wait after sending a close frame for an acknowledgement to come back before giving up and just closing the socket.*

**tlsOptions** - object - *默认: {}*  
使用TLS的通过 `https.request`配置，See [Node's HTTPS 文档](http://nodejs.org/api/https.html#https_https_request_options_callback)  

*--> Options to pass to `https.request` if connecting via TLS.  See [Node's HTTPS documentation](http://nodejs.org/api/https.html#https_https_request_options_callback)*


方法
-------
### connect(requestUrl, requestedProtocols, [[[origin], headers], requestOptions])
`requestUrl`, 建立连接时必须指定一个符合websocket标准的url，例如：`ws://www.mygreatapp.com:1234/websocketapp/`;  
`requestedProtocols` 指定指定多种client支持的子协议。远程Server将会选择最优的子协议进行连接，并返回在建立连接时。  
`origin` 可选项，被用于用户代理方案 来确认当前页面包含任何连接的脚本内容。（这不像Node.. 可能大多时间应该设置为null）。

*--> Will establish a connection to the given `requestUrl`.  `requestedProtocols` indicates a list of multiple subprotocols supported by the client.  The remote server will select the best subprotocol that it supports and send that back when establishing the connection.  `origin` is an optional field that can be used in user-agent scenarios to identify the page containing any scripting content that caused the connection to be requested.  (This seems unlikely in node.. probably should leave it null most of the time.)  `requestUrl` should be a standard websocket url, such as:
`ws://www.mygreatapp.com:1234/websocketapp/`*


`headers` 应该设置为`null` 或者 一个对象（指定额外的HTTP 请求头）与请求一起发送。
可用于身份的token令牌等，那么server端可以在开放完整的WebSocket连接之前，校验authentication/authorization 身份信息。

*--> `headers` should be either `null` or an object specifying additional arbitrary HTTP request headers to send along with the request.  This may be used to pass things like access tokens, etc. so that the server can verify authentication/authorization before deciding to accept and open the full WebSocket connection.*


`requestOptions` 应该设置为`null` 或者一个对象（指定额外的配置）来传递给 `http.request` or `https.request`。这可以用于传递一个自定义`agent`来启用`WebSocketClient`来使用HTTP或者HTTPS代理，通过[koichik/node-tunnel](https://github.com/koichik/node-tunnel) 或者 similar。  
*--> `requestOptions` should be either `null` or an object specifying additional configuration options to be passed to `http.request` or `https.request`.  This can be used to pass a custom `agent` to enable `WebSocketClient` usage from behind an HTTP or HTTPS proxy server using [koichik/node-tunnel](https://github.com/koichik/node-tunnel) or similar.*



传递`headers`时候，必须填写`origin`；传递`origin`和`headers`的时候，必须填写 `requestOptions`。  
*--> `origin` must be specified if you want to pass `headers`, and both `origin` and `headers` must be specified if you want to pass `requestOptions`.  The `origin` and `headers` parameters may be passed as `null`.*

### abort()

在`connect`或者`connectFailed`事件被发射之前，调用`abort()`来取消正在进行中的链接。如果`connect`或者`connectFailed`事件已经被发射，调用`abort()`不会有效果。

*--> Will cancel an in-progress connection request before either the `connect` event or the `connectFailed` event has been emitted.  If the `connect` or `connectFailed` event has already been emitted, calling `abort()` will do nothing.*


事件
------
### connect
`function(webSocketConnection)`

在成功通过WebSocket握手与远程Server后触发。`webSocketConnection`作为`WebSocketConnection`的实例可以和远程Server进行发送和接收消息。  

*--> Emitted upon successfully negotiating the WebSocket handshake with the remote server.  `webSocketConnection` is an instance of `WebSocketConnection` that can be used to send and receive messages with the remote server.*

### connectFailed
`function(errorDescription)`

连接错误时触发，发生在：连接远程主机错误 或者 server返回无效的握手响应。

*--> Emitted when there is an error connecting to the remote host or the handshake response sent by the server is invalid.*

### httpResponse
`function(response, webSocketClient)`

当Server应答任何东西时触发（除了"101 Switching Protocols"），例如 提供一个处理重定向的机会。`response` 是[http.IncomingMessage](http://nodejs.org/api/http.html#http_http_incomingmessage)类的实例，但不适合处理大的响应实体，否则基础套接字将被立即WebSocket-Node关闭。

*--> Emitted when the server replies with anything other then "101 Switching Protocols".  Provides an opportunity to handle redirects for example. The `response` parameter is an instance of the [http.IncomingMessage](http://nodejs.org/api/http.html#http_http_incomingmessage) class.  This is not suitable for handling receiving of large response bodies, as the underlying socket will be immediately closed by WebSocket-Node as soon as all handlers for this event are executed.*

通常，如果远程Server发送一个不是'101'的HTTP响应码，`WebSocketClient`会自动触发`connectFailed`事件（含有远程Server返回的描述信息）。但是，如果有一个或多个监听`httpResponse`事件，那么`connectFailed`事件将不会因为接收到了110以外的状态码而触发。`connectFailed` 仍然会在 non-HTTP 错误的情况下触发，例如发生不能达到 或 不接受tcp连接的情况。

*--> Normally, if the remote server sends an HTTP response with a response code other than 101, the `WebSocketClient` will automatically emit the `connectFailed` event with a description of what was received from the remote server.  However, if there are one or more listeners attached to the `httpResponse` event, then the `connectFailed` event will not be emitted for non-101 responses received.  `connectFailed` will still be emitted for non-HTTP errors, such as when the remote server is unreachable or not accepting TCP connections.*


Examples
========

使用代理服务器连接
----------------------------

Using [koichik/node-tunnel](https://github.com/koichik/node-tunnel):

```javascript
var WebSocketClient = require('websocket').client;
var client = new WebSocketClient();
var tunnel = require('tunnel');

var tunnelingAgent = tunnel.httpOverHttp({
  proxy: {
    host: 'proxy.host.com',
    port: 8080
  }
});

var requestOptions = {
    agent: tunnelingAgent
};

client.connect('ws://echo.websocket.org/', null, null, null, requestOptions);
```
