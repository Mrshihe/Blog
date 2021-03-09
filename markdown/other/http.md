#### HTTP常用请求头字段
```
Host: www.baidu.com
客户端请求的资源所处的主机名和端口号
Referer: https://www.baidu.com/s?ie=UTF-8&wd=%E5%
客户端请求的原始资源的URI
Cookie: BIDUPSID=0FA3B0B34EA21368F4EC83116E240288;
服务器设置得Cookie信息
Accept: application/json, text/javascript, */*; q=0.01
客户端能够处理的媒体类型及媒体类型的相对优先级;优先级用q=表示,范围0~1,可精确到小数点后3位,默认为最大值1
Accept-Encoding: gzip, deflate
客户端支持的内容编码及内容编码的优先级顺序(采用权重q值来表示相对优先级)
Accept-Language: zh-CN,zh;q=0.9
客户端能够处理的自然语言集
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.190 Safari/537.36
客户端使用的操作系统和浏览器的名称和版本

```
#### HTTP常用响应头字段
```
Allow：GET, POST
服务器支持哪些请求方法
Content-Encoding: gzip
服务器对实体的主体部分选用的内容编码方式
Content-Length: 78
实体主体部分的大小(单位是字节)
Set-Cookie: PSINO=2; domain=.baidu.com; path=/
服务器端设置Cookie
```


### HTTP缓存，分为强缓存、协商缓存两种
加快资源获取速度, 减少网络传输, 缓解服务端压力

#### 强缓存
强缓存由Expires、Cache-Control 、Pragma3个Header属性共同控制
1. Expires:值为一个HTTP日期(优先级最低)
浏览器发起请求时，根据系统时间和Expires进行比较，如果系统时间超过Expires，缓存失效。（当系统时间和服务器时间不一致的时候，会有缓存有效期不准的问题)

2. Cache-Control:(HTTP/1.1新增)
常用的属性值:
```
max-age: 单位秒，缓存时间是距离发起的时间的秒数，超过间隔的秒数缓存失效
no-cache：不使用强缓存
no-store：禁止使用缓存(包括协商缓存)
private：专用于个人的缓存，中间代理、CDN 等不能缓存此响应
public：响应可以被中间代理、CDN 等缓存
must-revalidate：在缓存过期前可以使用，过期后必须向服务器验证
```

3. Pragma:no-cache 不使用强缓存,(优先级最高)


#### 协商缓存
浏览器的强缓存失效或者请求头中设置了不走强缓存，并且在请求头中设置了If-Modified-Since或者If-None-Match的时候，会用这两个属性去验证是否命中协商缓存

1. ETag / If-None-Match
ETag/If-None-Match 的值是一串hash码(资源的标识符)，服务端的文件变化的时候hash码会随之改变，通过请求头中的 If-None-Match 和当前文件的hash值进行比较，如果相等则表示命中协商缓存。
ETag 又有强弱校验之分，如果 hash 码是以 "W/" 开头的一串字符串，说明此时协商缓存的校验是弱校验。只有服务器上的文件差异（根据 ETag 计算方式来决定）达到能够触发 hash 值后缀变化的时候，才会真正地请求资源。

2. Last-Modified / If-Modified-Since
Last-Modified/If-Modified-Since 的值代表的是文件的最后修改时间，第一次请求服务端会把资源的最后修改时间放到 Last-Modified 响应头中，第二次发起请求的时候，请求头会带上上一次响应头中的 Last-Modified 的时间
并放到 If-Modified-Since 请求头属性中，服务端根据文件最后一次修改时间和 If-Modified-Since 的值进行比较。

##### Last-Modified / If-Modified-Since以下情况并不准确
- 文件的修改频率在秒级以下，会错误地返回 304
- 文件被修改了，但是内容没有任何变化的时候, 会错误地返回 304