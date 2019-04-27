W3CWebSocket
============

* [构造函数](#构造函数)
* [局限性](#局限性)

`var W3CWebSocket = require('websocket').w3cwebsocket`

浏览器实现 基于 [W3C WebSocket API](http://www.w3.org/TR/websockets/).

这个暴露的class让开发者可以在NODE中使用浏览器的*W3C WebSocket API*：

```javascript
var WS = require('websocket').w3cwebsocket;

WS === window.WebSocket
// => true when in the browser

var ws = new WS('ws://example.com/resource', 'foo', 'http://example.com');
// - In Node it creates an instance of websocket.W3CWebSocket.
// - In the browser it creates an instance of window.WebSocket (third parameter
//   is ignored by the native WebSocket constructor).

ws.onopen = function() { console.log('ws open'); };
// etc.
```


构造函数
-----------

```javascript
new W3CWebSocket(requestUrl, requestedProtocols, [[[[origin], headers], requestOptions], clientConfig])
```

**clientConfig** [WebSocketClient](./WebSocketClient.md) 构造函数的一个参数.

**requestUrl**, **requestedProtocols**, **origin**, **headers** and **requestOptions** 这些参数被用在[WebSocketClient](./WebSocketClient.md)的 `connect()` 方法中.

构造函数的API可以使用W3C API，将Node应用"伪浏览器化".

当运行浏览器的时候(for example by using [browserify](http://browserify.org/))，浏览器的本地 `WebSocket` 实现将被使用, 然后就是第一个和第二个参数(`requestUrl` and `requestedProtocols`)被使用 (those allowed by the *W3C WebSocket API*).


局限性
-----------

* `bufferedAmount` 属性值总是0.
* `binaryType` 是"arraybuffer"给的默认值，“blob”不支持(Node 没有实现 the `Blob` class).
* `send()` 允许的参数类型 `DOMString`, `ArrayBuffer`, `ArrayBufferView` (`Int8Array`, 等等) or Node `Buffer`, 但不允许 `Blob`.
