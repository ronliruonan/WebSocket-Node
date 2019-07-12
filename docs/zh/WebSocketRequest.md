WebSocketRequest 
================

* [构造函数](#构造函数)
* [属性](#属性)
* [方法](#方法)
* [事件](#事件)

该对象代表了客户端连接服务端的请求，允许你接收 /或 拒绝连接 依据你自己拟定的协议。  
*（原文）This object represents a client requesting to connect to the server, and allows you to accept or reject the connection based on whatever criteria you decide.*

构造函数
-----------

该对象在`WebSocketServer`内部被创建。
This object is created internally by `WebSocketServer`.

然而，如果你需要让WebSocket支持没有负载http server的`WebSocketServer`，你必须手动触发`upgrad`事件，并且在`WebSocketRequest`的构造函数中填写适当的参数。注意：你必须填写一个完整的配置文件给构造函数。下面的配置项中，只有`httpServer`不是必须的。  
*（原文）However if you need to integrate WebSocket support without mounting an instance of `WebSocketServer` to your http server directly, you can handle the `upgrade` event yourself and pass the appropriate parameters to the `WebSocketRequest` constructor.  **NOTE:** You *must* pass a complete set of config options to the constructor.  See the section *'Server Config Options'* above.  The only option that isn't required in this context is `httpServer`.*

```javascript
new WebSocketRequest(socket, httpRequest, config);
```
构造函数不会被立刻解析，不会立刻验证客户端的握手，所有你需要调用`readHandshake()`(如果来自客户端的握手无效或者报错，将会抛出错误)，所以必须总被try/cactch包裹。  
*(原文)The constructor won't immediately parse and validate the handshake from the client, so you need to call `readHandshake()`, which will `throw` an error if the handshake from the client is invalid or if an error is encountered, so it must always be wrapped in a try/catch block.*

属性
----------
### httpRequest

NODE HTTP的request对象引用。对于Node环境的服务端比较有用，例如Express
A reference to the original Node HTTP request object.  This may be useful in combination with some other Node-based web server, such as Express, for accessing cookies or session data.


### host

A string containing the contents of the `Host` header passed by the client.  This will include the port number if a non-standard port is used.

Examples:
```
www.example.com
www.example.com:8080
127.0.0.1:3000
```

### resource

A string containing the path that was requested by the client.

### resourceURL

A Node URL object containing the parsed `resource`, including the query string parameters.

### remoteAddress

The remote client's IP Address as a string.  If an `X-Forwarded-For` header is present, the value will be taken from that header to facilitate WebSocket servers that live behind a reverse-proxy.

### websocketVersion

**Deprecated, renamed to webSocketVersion**

### webSocketVersion

A number indicating the version of the WebSocket protocol requested by the client.

### origin

If the client is a web browser, `origin` will be a string containing the URL of the page containing the script that opened the connection.  If the client is **not** a web browser, `origin` may be `null` or "*".

### requestedExtensions

An array containing a list of extensions requested by the client.  This is not currently used for anything. **Example:**

```javascript
[
    {
        name: "simple-extension";
    },
    {
        name: "my-great-compression-extension",
        params: [
            {
                name: "compressionLevel",
                value: "10";
            }
        ]
    }
]
```

### requestedProtocols

An array containing a list of strings that indicate the subprotocols the client would like to speak.  The server should select the best one that it can support from the list and pass it to the accept() function when accepting the connection.  Note that all the strings in the `requestedProtocols` array will have been converted to lower case, so that acceptance of a subprotocol can be case-insensitive.

方法
-------

### accept(acceptedProtocol, allowedOrigin)
返回连接实例
*Returns: WebSocketConnection instance*

在检查完WebSocketRequest的属性后，在request对象上调用这个方法。如果你没有指定协议，`accep5edProtocol` 要设置为`null`。
After inspecting the WebSocketRequest's properties, call this function on the request object to accept the connection.  If you don't have a particular subprotocol you wish to speak, you may pass `null` for the `acceptedProtocol` parameter.  Note that the `acceptedProtocol` parameter is *case-insensitive*, and you must either pass a value that was originally requested by the client or `null`.  For browser clients (in which the `origin` property would be non-null) you must pass that user's origin as the `allowedOrigin` parameter to confirm that you wish to accept connections from the given origin.  The return value contains the established `WebSocketConnection` instance that can be used to communicate with the connected client.

### reject([httpStatus], [reason])

If you decide to reject the connection, you must call `reject`.  You may optionally pass in an HTTP Status code (such as 404) and a textual description that will be sent to the client in the form of an "X-WebSocket-Reject-Reason" header.  The connection will then be closed.

Events
------

### requestAccepted
`function(webSocketConnection)`

Emitted by the WebSocketRequest object when the `accept` method has been called and the connection has been established.  `webSocketConnection` is the established `WebSocketConnection` instance that can be used to communicate with the connected client.

### requestRejected
`function()`

Emitted by the WebSocketRequest object when the `reject` method has been called and the connection has been terminated.
