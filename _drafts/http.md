http cs模型， 无状态， 可靠传输， 基于文本（http2.0以前）

报文格式

head + empty_line + body


head: [start_line + http_headers]


忽略起始行之前的内容





## 分级代理


## 缓存


## 长连接

### 优点

1.  节省创建和关闭TCP连接的开销；
1.  请求和响应可以用pipeline的方式；
1.  减小TCP拥塞（pipeline的方式减少TCP报文数）、时延（减少TCP握手开销）

### 实现

服务端默认客户端保持长连接，除非客户端的请求头中携带**connection：close**，如果服务端
在发送响应后，会立即关闭连接，应该在响应报文中携带**connection：close**

客户端默认长连接，当收到携带**connection：close**的响应时，关闭连接，当客户端在发送
一个请求时，不想再维持长连接时，应当在本次请求中携带**connection：close**

当服务端或者客户端发送的响应或者请求中，携带**connection：close**时，意味着这是本次
连接的最后一个响应或者请求

## 虚拟主机


## 认证和鉴权

RFC 2617




