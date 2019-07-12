WebSocketServer
===============

* [构造函数](#构造函数)
* [配置项](#配置项)
* [属性](#属性)
* [方法](#方法)
* [事件](#事件)

`var WebSocketServer = require('websocket').server`

构造函数
-----------

```javascript
new WebSocketServer([serverConfig]);
```

方法
-------

### mount(serverConfig)

`mount`会附加到WebSocketServer实例中的Node http.Server 实例。 `serverConfig` 必需参数，详细请看下面的**配置项**。如果在构造函数中填写`serverConfig`，那么这个方法会自动启动。  
*（原文） `mount` will attach the WebSocketServer instance to a Node http.Server instance. `serverConfig` is required, and is an object with configuration values.  For those values, see **Server Config Options** below.  If you passed `serverConfig` to the constructor, this function will automatically be invoked.*

### unmount()

`unmount` 会脱离WebSocketServer实例中的Node http.Server 实例。所有的连接都是单独运行、互补影响，但是新连接不会被影响。  
*（原文）`unmount` will detach the WebSocketServer instance from the Node http.Server instance.  All existing connections are left alone and will not be affected, but no new WebSocket connections will be accepted.*

### closeAllConnections()

优雅的关闭所有 打开状态的连接。  
*（原文）Will gracefully close all open WebSocket connections.*

### shutDown()

优雅的关闭所有 打开状态的连接，卸载Node http.Server的实例  
*（原文） Gracefully closes all open WebSocket connections and unmounts the server from the Node http.Server instance.*

配置项
---------------------

**httpServer** - (http.Server实例) **必需**.  

被附加的HTTP实例 /或 HTTPS实例。你可以直接传递一个或一组HTTP/HTTPS实例。
当你使用同一个WebSocketServer实例时，可以通过传递一组HTTP/HTTPS实例，来支持 加密（ws://） 与 非加密（wss://） 的WebSocket。  
*（原文） The Node http or https server instance(s) to attach to.  You can pass a single instance directly, or pass an array of instances to attach to multiple http/https servers.  Passing an array is particularly useful when you want to accept encrypted and unencrypted WebSocket connections on both ws:// and wss:// protocols using the same WebSocketServer instance.*

**maxReceivedFrameSize** - uint - *Default: 64KiB*  

接收碎片大小上限。单个碎片消息同样受限制。  
*（原文）The maximum allowed received frame size in bytes.  Single frame messages will also be limited to this maximum.*

**maxReceivedMessageSize** - uint - *Default: 1MiB*  

聚合消息大小上限。  
*（原文）The maximum allowed aggregate message size (for fragmented messages) in bytes.*

**fragmentOutgoingMessages** - Boolean - *Default: true*  

是否开启碎片化输出消息。true：消息将自动碎片成`fragmentationThreshold` 字节大小的块。  
*（原文）Whether or not to fragment outgoing messages.  If true, messages will be automatically fragmented into chunks of up to `fragmentationThreshold` bytes.*

**fragmentationThreshold** - uint - *Default: 16KiB*  

被碎片之前，一个碎片的最大值.  
*（原文）The maximum size of a frame in bytes before it is automatically fragmented.*

**keepalive** - boolean - *Default: true*  

true: 根据客户端的`keepaliveInterval`毫秒，服务端定期发送ping命令。每个客户端都有一个独立的keepalive 计时器，计时器将在服务端接收到客户端任何数据后重置。  
*（原文） If true, the server will automatically send a ping to all clients every `keepaliveInterval` milliseconds.  Each client has an independent keepalive timer, which is reset when any data is received from that client.*

**keepaliveInterval** - uint - *Default: 20000*  

间歇性发送keepalive ping 来和client保持连接。  
*（原文） The interval in milliseconds to send keepalive pings to connected clients.*

**dropConnectionOnKeepaliveTimeout** - boolean - *Default: true*  

true: 服务端将会统筹所有 发出keepavlie ping之后， 且 在指定的`keepaliveGracePeriod`次数内，且，没有接收到任何数据的连接。忽略false的`keepalive`。   
*（原文） If true, the server will consider any connection that has not received any data within the amount of time specified by `keepaliveGracePeriod` after a keepalive ping has been sent. Ignored if `keepalive` is false.*

**keepaliveGracePeriod** - uint - *Default: 10000*  

在发送keepalive ping 之后，在关闭连接之前，没有任何响应的连接。忽略`keepalive` 或`dropConnectionOnKeepaliveTimeout`为false的连接。在价受到任何client信息后，限期计时器将被重置。  
*（原文） The amount of time to wait after sending a keepalive ping before closing the connection if the connected peer does not respond. Ignored if `keepalive` or `dropConnectionOnKeepaliveTimeout` are false.  The grace period timer is reset when any data is received from the client.*

**assembleFragments** - boolean - *Default: true*  

true: 碎片消息将被自动聚合，并且完整的消息将触发`message` 事件。false：每个碎片将被WebSocketConnection 对象通过`frame`事件触发，并且应用将要自己合并碎片和消息。当个碎片消息将被触发在附在`frame`事件的`message`事件。大多数用户会保持它的默认值为'true'。      
*（原文） If true, fragmented messages will be automatically assembled and the full message will be emitted via a `message` event. If false, each frame will be emitted on the WebSocketConnection object via a `frame` event and the application will be responsible for aggregating multiple fragmented frames.  Single-frame messages will emit a `message` event in addition to the `frame` event. Most users will want to leave this set to `true`.*

**autoAcceptConnections** - boolean - *Default: false*  

true: WebSocket连接自动接受，不管是client指定的路径或者协议。......。client 任何origin将被接收。这个配置只能用于简单的小案例。你可能应该保持它为false，并且检查请求对象是否被允许。  
*（原文）If this is true, websocket connections will be accepted regardless of the path and protocol specified by the client. The protocol accepted will be the first that was requested by the client.  Clients from any origin will be accepted. This should only be used in the simplest of cases.  You should probably leave this set to `false`; and inspect the request object to make sure it's acceptable before accepting it.*

**closeTimeout** - uint - *Default: 5000*  

关闭超时毫秒数，在发送close frame之后，放弃和关闭套接字之前。  
*（原文） The number of milliseconds to wait after sending a close frame for an acknowledgement to come back before giving up and just closing the socket.*

**disableNagleAlgorithm** - boolean - *Default: true*  

The Nagle Algorithm makes more efficient use of network resources by introducing a small delay before sending small packets so that multiple messages can be batched together before going onto the wire.  This however comes at the cost of latency, so the default is to disable it.  If you don't need low latency and are streaming lots of small messages, you can change this to 'false';

**ignoreXForwardedFor** - Boolean - *Default: false*  
Whether or not the `X-Forwarded-For` header should be respected.
It's important to set this to 'true' when accepting connections
from untrusted clients, as a malicious client could spoof its
IP address by simply setting this header.  It's meant to be added
by a trusted proxy or other intermediary within your own
infrastructure.
More info: [X-Forwarded-For on Wikipedia](http://en.wikipedia.org/wiki/X-Forwarded-For)

事件
------

这儿有3个WebSocketServer触发事件，允许你处理请求，启动连接，检测连接的关闭    
*(原文) There are three events emitted by a WebSocketServer instance that allow you to handle incoming requests, establish connections, and detect when a connection has been closed.*

### request
`function(webSocketRequest)`

配置`autoAcceptConnections`为`false`，在任何一个新请求发生时，触发该事件。
应该根据 请求协议 和 用户origin 来验证连接的有效性，然后通过调用webSocketRequest.accept('chosen-protocol', 'accepted-origin')来接受连接 /或 调用webSocketRequest.reject()拒绝连接。   
*（原文） If `autoAcceptConnections` is set to `false`, a `request` event will be emitted by the server whenever a new WebSocket request is made.  You should inspect the requested protocols and the user's origin to verify the connection, and then accept or reject it by calling webSocketRequest.accept('chosen-protocol', 'accepted-origin') or webSocketRequest.reject()*

### connect
`function(webSocketConnection)`

任何被允许的新WebSocket，触发该事件。  
*（原文）Emitted whenever a new WebSocket connection is accepted.*

### close
`function(webSocketConnection, closeReason, description)`

任何连接关闭都将触发该事件。`webSocketConnection`: 一个关闭的WebSocketConnection的实例引用。`closeReason`: 数字状态码，`description`: 文本描述。     
*（原文）Whenever a connection is closed for any reason, the WebSocketServer instance will emit a `close` event, passing a reference to the WebSocketConnection instance that was closed.  `closeReason` is the numeric reason status code for the connection closure, and `description` is a textual description of the close reason, if available.*
