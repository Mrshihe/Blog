### 从输入url到页面展示，大致可以概括为6步
1. DNS解析
2. TCP连接
3. 发送HTTP请求
4. 服务器返回HTTP响应
5. 浏览器解析渲染页面
6. TCP断开

#### DNS解析
DNS是将域名解析为IP地址的过程，以访问www.baidu.com为例。完整域名为 www.baidu.com. 最后一个.就是根域名服务器，通常情况下省略，解析过程从右向左。

- 浏览器输入www.baidu.com后先在浏览器的DNS缓存中查找，有则完成解析。
- 浏览器没有缓存，去查询操作系统的缓存，有则完成解析。
- 操作系统也没有缓存，去查看本地host文件，有则完成解析。
- host里也没有缓存，去本地DNS服务器（一般由本地网络服务商提供，如联通、电信）查询，有则完成解析。
- 本地DNS服务器里没有查到，去跟域名服务器查询（根域名服务器目前世界有13个），根域名服务器返回顶级域名服务器(.com)地址。
- 本地DNS服务器去顶级域名服务器查询，顶级域名服务器返回权威域名服务器(baidu.com)地址。
- 权威域名服务器将www.baidu.com查到对应IP地址返回给本地DNS服务器。

DNS解析方式有两种：(浏览器到本地DNS服务器为递归解析，本地DNS服务器以后为迭代解析)
- 递归解析 ：本地DNS向根域名查询，根域名向顶级域名查询，顶级域名向权威域名查询，权威域名查询完成后，返回给顶级域名，顶级域名返回给根域名，根域名返回给本地DNS。
- 迭代解析 ：本地DNS向根域名查询，根域名返回顶级域名地址，本地DNS向顶级域名查询，顶级域名返回权威域名地址，本地DNS向权威域名查明，权威域名查询完成后返回给本地DNS。

#### TCP连接
TCP进行三次握手，三次握手的目的：《计算机网络》第四版中 “为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误”
(client发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达server。本来这是一个早已失效的报文段。但server收到此失效的连接请求报文段后，就误认为是client再次发出的一个新的连接请求。于是就向client发出确认报文段，同意建立连接)
##### TCP中的主要字段
```
序号(sequence number): seq 标识从TCP源端向目的端发送的字节流
确认号(acknowledgement number): ack Ack=Seq+1
标志位(Flags): ACK 确认标志 ACK=1确认有效 / SYN 发起新连接 / FIN 释放连接  
```
1. 第一次握手，浏览器发起 SYN=1,seq=x 然后进入SYN_SEND状态，等待服务器确认。
2. 第二次握手，服务器发起 SYN=1,ACK=1(应答有效),ack=x+1(对浏览器报文进行确认),seq=y 然受进入SYN_RECV状态。
3. 第三次握手，浏览器发起 ACK=1(应答有效), ack=y+1, seq=x+1 双方进入ESTABLISHED状态。

#### HTTP请求
HTTP请求包含请求行、请求头、请求体三个主要部分
- ##### 请求行包括请求方法、请求地址、协议/版本号
HTTP/1.0中定义了三种GET、POST、HEAD　HTTP/1.1中新增了五种OPTIONS、PUT、DELETE、TRACE、CONNECT
1. GET 主要用于获取资源，关注点一般在响应主体上
2. POST 主要用于提交数据进行处理，数据一般包含在请求体中
3. HEAD 作用和GET类似，但是不会有响应主体
4. OPTIONS 查询目地资源支持的请求方法,一般不会主动发起 ([跨域](https://github.com/Mrshihe/blog/blob/master/markdown/js/跨域及其解决方案.md)时，浏览器发起复杂请求时主动发起)
5. PUT 用于传输文件，请求体中包含文件内容，自身不带验证机制，存在安全性问题
6. DELETE 用来删除文件，本身不具有验证机制
7. TRACE 让服务器端将之前的请求通信环回给客户端的方法，查询发送出去的请求是怎样被加工修改/篡改的
8. CONNECT 要求在与代理服务器通信时建立隧道，实现用隧道协议进行TCP通信。主要使用SSL（Secure Sockets Layer，安全套接层）和TLS（TransportLayer Security，传输层安全）协议把通信内容加密后经网络隧道传输

#### HTTP响应
HTTP响应包含状态行、响应头、请求头
- ##### 状态码
> 1xx: // 请求已被接受，需要继续处理
> 2xx: // 请求已成功被服务器接收、理解、并接受
> 3xx: // 重定向-客户端采取进一步的操作才能完成请求, 后续的请求地址（重定向目标）在本次响应的Location域中指明
>   301: 永久重定向
>   302: 临时重定向
> 4xx: // 客户端错误-请求有语法错误或请求无法实现
>   400: // 请求报文中存在语法错误
>   403: // 服务器拒绝该次访问
>   404: // 无法找到请求的资源
> 5xx: // 服务端错误
>   500: // 内部服务器错误
>   502: // 网关错误

#### 浏览器解析渲染页面
##### 浏览器渲染大致分为以下几步：
1. 解析HTML，构建DOM树
2. 解析CSS，生成CSS规则树
3. 合并DOM树和CSS规则，生成render树
4. 布局render树（Layout/reflow），负责各元素尺寸、位置的计算 <不可见的标签(head)或者display:none的元素不会插入render树中>
5. 绘制render树（paint），绘制页面像素信息
6. 发送给GPU，GPU会将各层合成（composite），显示在屏幕上

元素的内容、结构、位置或尺寸发生了变化，需要重新计算样式和渲染树，称为回流( Layout/reflow )，元素样式的改变（例如，背景色，边框颜色，文字颜色等）称为重绘。
#### 引发回流的情况
```
offset(Top/Left/Width/Height)\scroll(Top/Left/Width/Height)\cilent(Top/Left/Width/Height)\width,heigh\getComputedStyle()
```
避免DOM回流
①分离读写操作( 现代浏览器都有渲染队列机制，读取样式会打断渲染队列 )  
②样式集中改变  xxx.style.cssText / xxx.className
③元素批量处理  document.createDocumentFragment 使用完释放 / 字符串拼接
④css3硬件加速  transfrom开启硬件加速，不会引发回流和重绘
⑤动画效果应用到position属性为absolute或fixed的元素上（脱离文档流）
#### 引发重绘的情况
```
background/color/border-color
```
##### 回流一定引发重绘，重绘不一定引发回流

#### TCP断开连接
1. 客服端发送FIN=1,seq=x,然后进入FIN-WAIT-1状态。
2. 服务端进入CLOSE-WAIT状态，发送ACK=1,ack=x+1,seq=y, 客户端进入FIN-WAIT-2状态。
3. 服务端发送FIN=1,ACK=1,ack=x+1,seq=z，服务端进入LAST-ACK状态。
4. 客户端发送ACK=1,ack=z+1,seq=x+1，客户端进入TIME-WAIT状态。

