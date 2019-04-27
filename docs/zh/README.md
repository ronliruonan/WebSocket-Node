Node: WebSocket Client & Server 的实现
=================================================

中文译文，如有异议，随时交流。

在原版发生变化后，此文章不确保能够及时更新。

概览
--------
这是一个Node环境中，主要使用纯JavaScript实现的WebSocket（协议版本8、13）。

*This is a (mostly) pure JavaScript implementation of the WebSocket protocol versions 8 and 13 for Node.*

在“test/scripts”文件夹中，有一些客户端和服务端的实例应用，它们使用了不同的互操作性测试协议。

*There are some example client and server applications that implement various interoperability testing protocols in the "test/scripts" folder.*

用ActionsScript 3 写的WebSocket客户端，项目 [AS3WebScocket](https://github.com/theturtle32/AS3WebSocket)。

*For a WebSocket client written in ActionScript 3, see my [AS3WebScocket](https://github.com/theturtle32/AS3WebSocket) project.*

参考文档
--------
WebSocket-Node 包含 client、server 的功能, 分别通过 WebSocketClient、WebSocketServer 来使用.  一旦连接被确认，发送和接收信息的API是完全一样的，不论是你是作为client 还是 server来运行。

点击下面的class来查看对应的API文档：
* [WebSocketClient](./WebSocketClient.md)
* [WebSocketConnection](./WebSocketConnection.md)
* [WebSocketFrame](./WebSocketFrame.md)
* [WebSocketRequest](./WebSocketRequest.md)
* [WebSocketServer](./WebSocketServer.md)
* [W3CWebSocket](./W3CWebSocket.md)

变更日志
---------

***当前版本: 1.0.27*** — 发布于 2018-09-19

* 在WebSocketClient中，允许将额外请求 `headers` 添加到 `tlsOptions` 配置参数中。请看pull request #323
* *Allowing additional request `headers` to be specified in the `tlsOptions` config parameter for WebSocketClient. See pull request #323*
* 解决有关 `new Buffer` 使用的否决警告
* *Resolving deprecation warnings relating to usage of `new Buffer`*

浏览器支持
---------------
目前所有的浏览器完全支持。
*（原味）All current browsers are fully supported.*

* Firefox 7-9 (Old) (Protocol Version 8)
* Firefox 10+ (Protocol Version 13)
* Chrome 14,15 (Old) (Protocol Version 8)
* Chrome 16+ (Protocol Version 13)
* Internet Explorer 10+ (Protocol Version 13)
* Safari 6+ (Protocol Version 13)

***比6.0版本还老的Safari浏览器不支持，因为它使用了一个很古老的WebSockets草案***

*Safari older than 6.0 is not supported since it uses a very old draft of WebSockets*

***如果需要同时兼容传统基于draft-75/draft-76/draft-00实现的浏览器，可以参考一下这里: https://gist.github.com/1428579***

*If you need to simultaneously support legacy browser versions that had implemented draft-75/draft-76/draft-00, take a look here: https://gist.github.com/1428579*

标准（Benchmarks）
----------
在Autobahn test suite有一些基本的基准部分。[基准页](http://theturtle32.github.com/WebSocket-Node/benchmarks/) 展示了运行在AutobahnServer 0.4.10, WebSocket-Node 1.0.2, WebSocket-Node 1.0.4, and ws 0.3.4的Autobahn测试结果.

*There are some basic benchmarking sections in the Autobahn test suite.  I've put up a [benchmark page](http://theturtle32.github.com/WebSocket-Node/benchmarks/) that shows the results from the Autobahn tests run against AutobahnServer 0.4.10, WebSocket-Node 1.0.2, WebSocket-Node 1.0.4, and ws 0.3.4.*

Autobahn Test
--------------
这个完善的Autobahn test suite经常被大多数WebSocket实现来测试规范的依从性和互操作性。

*The very complete [Autobahn Test Suite](http://autobahn.ws/testsuite/) is used by most WebSocket implementations to test spec compliance and interoperability.*

- [查看服务端测试结果](http://theturtle32.github.com/WebSocket-Node/test-report/servers/)

安装
------------
一些用户反馈，在没有手动安装node-gyp的情况下，搭建本地扩展困难。如果在搭建本地扩展中遇到困难，确保你安装了c++编译器，并且第一步已经执行了`npm install -g node-gyp`命令。

本地扩展是可选的，然而，WebSocket-Node 可以在扩展没有被编译的情况下运行。

*A few users have reported difficulties building the native extensions without first manually installing node-gyp.  If you have trouble building the native extensions, make sure you've got a C++ compiler, and have done `npm install -g node-gyp` first.*

*Native extensions are optional, however, and WebSocket-Node will work even if the extensions cannot be compiled.*

在项目根目录下 *(In your project root)* :c++

    $ npm install websocket
  
然后在代码中 *(Then in your code)* :

```javascript
var WebSocketServer = require('websocket').server;
var WebSocketClient = require('websocket').client;
var WebSocketFrame  = require('websocket').frame;
var WebSocketRouter = require('websocket').router;
var W3CWebSocket = require('websocket').w3cwebsocket;
```

Win系统用户注意：*(Note for Windows Users)*
----------------------
在验证 UTF-8 的数据时，需要使用一个C++组件，所以需要安装本地编译工具。可以通过 `windows-build-tools` 来安装：

*Because there is a small C++ component used for validating UTF-8 data, you will need to install 
native build tools. You can install the required components using `windows-build-tools`:*

```ps1
npm install --global --production windows-build-tools
```

或者，安装下面的组件 *（Alternatively, install the required components）*:

- [Microsoft Visual C++](http://www.microsoft.com/visualstudio/en-us/products/2010-editions/visual-cpp-express)
- [Python 2.7](http://www.python.org/download/) (NOT Python 3.x)

Current Features:
-----------------
- Licensed under the Apache License, Version 2.0
- Protocol version "8" and "13" (Draft-08 through the final RFC) framing and handshake
- 能够 处理/合并 接收到的碎片信息  
- *Can handle/aggregate received fragmented messages*
- 能够碎片化输出信息  
- *Can fragment outgoing messages*
- 路由能够通过不同的路径和协议组合来挂载多个应用 
- *Router to mount multiple applications to various path and protocol combinations*
- TLS支持通过WebSocketClient的出站连接 
- *TLS supported for outbound connections via WebSocketClient*
- TLS支持服务端连接（https.createServer 替代 http.createServer） 
- *TLS supported for server connections (use https.createServer instead of http.createServer)*
  - Thanks to [pors](https://github.com/pors) for confirming(确认/确认检验) this!
- 设置、解析Cookie 
- *Cookie setting and parsing*
- 可调的设置  *Tunable settings*
  - 最大可接收的帧大小  
  - *Max Receivable Frame Size*
  - 最大接收信息集合大小  
  - *Max Aggregate ReceivedMessage Size*
  - 是否碎片化输出信息  
  - *Whether to fragment outgoing messages*
  - 碎片化 大Size的输出信息  
  - *Fragmentation chunk size for outgoing messages*
  - 是否自动发送 ping 来维持keepalive  
  - *Whether to automatically send ping frames for the purposes of keepalive*
  - Keep-alive ping interval
  - 是否自动组装接收的碎片（允许应用直接处理单独的碎片） 
  - *Whether or not to automatically assemble received fragments (allows application to handle individual fragments directly)*
  - 配置等待时长，在发出确认close帧后，需要等几时后再关闭socket 
  - *How long to wait after sending a close frame for acknowledgment before closing the socket.*
- [W3C WebSocket API](http://www.w3.org/TR/websockets/) for applications running on both Node and browsers (via the `W3CWebSocket` class). 


已知问题/缺少功能 *Known Issues/Missing Features* :
------------------------------
- 没有支持用户自定义协议扩展的API
- *No API for user-provided protocol extensions.*


用法实例
==============

Server 实例
--------------

简短的Server实例能够回应任何发送给它的信息，不论是utf-8 还是 binary格式

*Here's a short example showing a server that echos back anything sent to it, whether utf-8 or binary.*

```javascript
#!/usr/bin/env node
var WebSocketServer = require('websocket').server;
var http = require('http');

var server = http.createServer(function(request, response) {
    console.log((new Date()) + ' 接收的请求来自： ' + request.url);
    response.writeHead(404);
    response.end();
});
server.listen(8080, function() {
    console.log((new Date()) + ' Server 正在监听端口 8080');
});

wsServer = new WebSocketServer({
    httpServer: server,
    // 在产品应用中，不应该使用自动接受的连接方式，
    // 因为这样会打败所有标准的跨域保护设备。
    // 应该*总是* 检查链接的origin和decide是否被允许
    // You should not use autoAcceptConnections for production
    // applications, as it defeats all standard cross-origin protection
    // facilities built into the protocol and the browser.  You should
    // *always* verify the connection's origin and decide whether or not
    // to accept it.
    autoAcceptConnections: false
});

function originIsAllowed(origin) {
  // 业务逻辑，判断origin是否被允许的
  // put logic here to detect whether the specified origin is allowed.
  return true;
}

wsServer.on('request', function(request) {
    if (!originIsAllowed(request.origin)) {
      // 确保只接受被允许的origin
      // Make sure we only accept requests from an allowed origin
      request.reject();
      console.log((new Date()) + ' 来自 origin ' + request.origin + ' 的连接被拒绝.');
      return;
    }
    
    var connection = request.accept('echo-protocol', request.origin);
    console.log((new Date()) + ' 连接被接受.');
    connection.on('message', function(message) {
        if (message.type === 'utf8') {
            console.log('接收的信息: ' + message.utf8Data);
            connection.sendUTF(message.utf8Data);
        }
        else if (message.type === 'binary') {
            console.log('接收的 Binary 信息有 ' + message.binaryData.length + ' 字节');
            connection.sendBytes(message.binaryData);
        }
    });
    connection.on('close', function(reasonCode, description) {
        console.log((new Date()) + ' Peer ' + connection.remoteAddress + ' 断线.');
    });
});
```

Client 实例
--------------

下面简单的Client实例将会打印任何控制台收到的utf-8信息，定期发送随机数

*This is a simple example client that will print out any utf-8 messages it receives on the console, and periodically sends a random number.*

这是在Node运行的Client代码，不是在浏览器

*This code demonstrates a client in Node.js, not in the browser*

```javascript
#!/usr/bin/env node
var WebSocketClient = require('websocket').client;

var client = new WebSocketClient();

client.on('connectFailed', function(error) {
    console.log('连接错误: ' + error.toString());
});

client.on('connect', function(connection) {
    console.log('WebSocket Client 连接成功');
    connection.on('error', function(error) {
        console.log("连接错误: " + error.toString());
    });
    connection.on('close', function() {
        console.log('echo-protocol 连接关闭');
    });
    connection.on('message', function(message) {
        if (message.type === 'utf8') {
            console.log("接收的消息: '" + message.utf8Data + "'");
        }
    });
    
    function sendNumber() {
        if (connection.connected) {
            var number = Math.round(Math.random() * 0xFFFFFF);
            connection.sendUTF(number.toString());
            setTimeout(sendNumber, 1000);
        }
    }
    sendNumber();
});

client.connect('ws://localhost:8080/', 'echo-protocol');
```

Client 实例 使用了 *W3C WebSocket API*
--------------------------------------------

如同上面的实例，只是使用了 W3C WebSocket API

*Same example as above but using the [W3C WebSocket API](http://www.w3.org/TR/websockets/).*

```javascript
var W3CWebSocket = require('websocket').w3cwebsocket;

var client = new W3CWebSocket('ws://localhost:8080/', 'echo-protocol');

client.onerror = function() {
    console.log('连接失败');
};

client.onopen = function() {
    console.log('WebSocket Client 连接成功');

    function sendNumber() {
        if (client.readyState === client.OPEN) {
            var number = Math.round(Math.random() * 0xFFFFFF);
            client.send(number.toString());
            setTimeout(sendNumber, 1000);
        }
    }
    sendNumber();
};

client.onclose = function() {
    console.log('echo-protocol Client Closed');
};

client.onmessage = function(e) {
    if (typeof e.data === 'string') {
        console.log("接收信息: '" + e.data + "'");
    }
};
```
    
路由请求实例
----------------------

在 `test` 文件夹中，请看 `libwebsockets-test-server.js`

*For an example of using the request router, see `libwebsockets-test-server.js` in the `test` folder.*


Resources
---------

A presentation on the state of the WebSockets protocol that I gave on July 23, 2011 at the LA Hacker News meetup.  [WebSockets: The Real-Time Web, Delivered](http://www.scribd.com/doc/60898569/WebSockets-The-Real-Time-Web-Delivered)
