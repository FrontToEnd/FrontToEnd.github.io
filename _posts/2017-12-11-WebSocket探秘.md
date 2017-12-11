---
layout:     post                   
title:      WebSocket探秘              
subtitle:   记录一下WebSocket的基本用法
date:       2017-12-11
author:     chuck
header-img: img/home-bg-o.jpg
catalog: true                      
tags:                               
    - JavaScript
    - HTML5
    - WebSocket
---


最近在项目中使用了`WebSocket`进行长连接通信，本文就简要的记录一下所用到的东西。

首先我们先学习几个概念：
**长连接**：
一个连接上可以连续发送多个数据包，在连接期间，如果没有数据包发送，需要双方发链路检查包。
**TCP/IP**：
TCP/IP属于传输层，主要解决数据在网络中的传输问题，只管传输数据。但是那样对传输的数据没有一个规范的封装、解析等处理，使得传输的数据就很难识别，所以才有了应用层协议对数据的封装、解析等，如HTTP协议。
**HTTP**：
HTTP是应用层协议，封装解析传输的数据。从HTTP1.1开始其实就默认开启了长连接，也就是请求header中看到的`Connection:Keep-alive`。但是这个长连接只是说保持了（服务器可以告诉客户端保持时间Keep-Alive:timeout=200;max=20;）这个TCP通道，直接Request - Response，而不需要再创建一个连接通道，做到了一个性能优化。但是HTTP通讯本身还是Request - Response。
**socket**：
与HTTP不一样，socket不是协议，它是在程序层面上对传输层协议（可以主要理解为TCP/IP）的接口封装。
我们知道传输层的协议，是解决数据在网络中传输的，那么socket就是传输通道两端的接口。所以对于前端而言，socket也可以简单的理解为对TCP/IP的抽象协议。
**WebSocket**：
WebSocket是包装成了一个应用层协议作为socket,从而能够让客户端和远程服务端通过web建立全双工通信。websocket提供ws和wss两种URL方案。

###WebSocket API

-------
接下来我们再来看看`WebSocket`的API。首先我们需要做的就是创建一个实例：

```
const wsUri = 'ws://zhhy-screen.ums86.com/webSocket/memberId/pc';

let websocket  = new WebSocket(wsUri);//创建WebSocket
```
因为项目中要涉及到重连机制，所以就使用了[ReconnectingWebSocket](https://github.com/joewalnes/reconnecting-websocket)这个插件，用法也十分简单，在需要创建ws实例的页面引入插件：

```
import ReconnectingWebSocket from './js/reconnecting-websocket.min'
// 具体的路径根据具体项目而定

let websocket  = new ReconnectingWebSocket(wsUri);
// 然后直接将创建实例的语句写成这样就可以了。
```
#### WebSocket 事件

* open

该事件是在服务器相应WebSocket连接请求触发。

```
websocket.onopen = (evt) => {
    let val = 'message';
    websocket.send(JSON.stringify(val));
};
```

* message

该事件是服务器有响应信息的时候触发。

```
websocket.onmessage = (evt) => {
    console.log(evt.data);
};
  //实际项目中就是在此事件中获取返回的数据的。
```

* error

该事件是出错时触发，并且会关闭连接。

```
websocket.onerror = (evt) => {
  	console.log('error');  	 
}
```

* close

连接关闭时触发，这在两端都可以关闭。另外如果连接失败也是会触发的。

```
websocket.onclose = function(evt) {
    console.log("Connection closed.");
};  
```
#### WebSocket 方法

* send()
该方法用来给服务端发送消息。需要注意的是，必须在与服务端成功建立连接后才可以发送消息，所以一般这样处理：

```
if (websocket.readyState === WebSocket.OPEN) {
    websocket.send(JSON.stringify(val));
}
```

* close()

该方法用来主动关闭连接。


```
websocket.close();
```

 上面出现的`readyState`就是ws实例的当前状态，一共有4种：
 
*  CONNECTING：值为0，表示正在连接。
*  OPEN：值为1，表示连接成功，可以通信了。
*  CLOSING：值为2，表示连接正在关闭。
*  CLOSED：值为3，表示连接已经关闭，或者打开连接失败。

所以可以根据`websocket.readyState === WebSocket.OPEN`来判断是否连接成功。

下面我们再来看看建立ws实例的时候，浏览器的请求和相应：

```
Request URL:ws://zhhy-screen.ums86.com/webSocket/memberId/pc
Request Method:GET
Status Code:101  //状态码为101表示切换协议

Response Headers

Connection:upgrade
Content-Type:application/octet-stream
Date:Fri, 08 Dec 2017 02:20:11 GMT
Sec-WebSocket-Accept:M2OEhXpd4zqIkjEQtUsEr0ZSJWs= //服务器端将加密处理后的握手Key通过这个字段返回给客户端表示服务器同意握手建立连接。
Sec-WebSocket-Extensions:permessage-deflate;client_max_window_bits=15
Server:Flaginfo_Web
ufe-result:A2
Upgrade:websocket

Request Headers

Accept-Encoding:gzip, deflate
Accept-Language:zh-CN,zh;q=0.9,en;q=0.8,fr;q=0.7
Cache-Control:no-cache
Connection:Upgrade //告知服务器当前请求连接是升级的。
Cookie:_qddaz=QD.f47rqu.5a3mle.j9tqibli
Host:zhhy-screen.ums86.com
Origin:http://localhost:8083
Pragma:no-cache
Sec-WebSocket-Extensions:permessage-deflate; client_max_window_bits
Sec-WebSocket-Key:jo9tTVEKTIC4UXR4HZpZMQ== //为了表示服务器同意和客户端进行Socket连接，服务器端需要使用客户端发送的这个Key进行校验，然后返回一个校验过的字符串给客户端，客户端验证通过后才能正式建立Socket连接。
Sec-WebSocket-Version:13 //版本，必须是13
Upgrade:websocket //告诉服务器这个HTTP连接是升级的Websocket连接。
User-Agent:Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36 //客户端的浏览器信息，可以根据此字段判断浏览器版本和客户端类型
```

以上就是所掌握的所有websocket知识，同时项目中还涉及到了PC的不同页面主动向App推送消息，其中就用到了VUEX(项目是使用VUE框架进行开发)进行消息的监听与管理，不同页面通过commit消息`this.$store.commit('sendData',data);`，创建ws实例的页面进行监听，监听到消息的变化后，就通过ws的`send()`方法来向app推送消息，服务器端就起到一个中间人的作用。

参考文章：[WebSocket探秘](https://juejin.im/post/5a1bdf676fb9a045055dd99d)










