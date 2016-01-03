# 学习http之首部字段

HTTP请求和响应报文中必定会包含HTTP首部。首部内容为客户端和服务器分别处理请求和响应提供了所需要的信息。

**HTTP请求报文**
请求报文由方法、URI、HTTP版本、HTTP首部字段等部分构成

**HTTP响应报文**
在响应中，HTTP报文由HTTP版本、状态码、HTTP首部字段构成

## 4种HTTP首部字段类型

 - 通用首部字段(General Header Fields) 
 - 请求首部字段(Request Header Fields)
 - 响应首部字段(Response Header Fields) 
 - 实体首部字段(Entity Header Fields)

## HTTP/1.1 首部字段一览表
### 通用首部字段
首部字段名|说明
----|----|
Cache-Control|控制缓存的行为
Connection|逐跳首部、连接的管理
Date|创建报文的日期管理
Pragma|报文指令
Trailer|报文末端的首部一览
Transfer-Encoding|指定报文主体的传输编码方式
Upgrade|升级为其他协议
Via|代理服务器的相关信息
Warning|错误通知
### 请求首部字段
首部字段名|说明
----|----|
Accept|用户代理可处理的媒体类型
Accept-Charset|优先的字符集
Accept-Encoding|优先的内容编码
Accept-Language|优先的语言（自然语言）
Authorization|Web认证信息
Expect|期待服务器的待定行为
From|用户的电子邮箱地址
Host|请求资源所在服务器
if-Match|比较实体标记（ETag）
if-Modified-Since|比较资源的更新时间
if-None-Match|比较实体标记(与if-Match相反)
if-Range|资源未更新时发送实体Byte的范围请求
if-Unmodified-Since|比较资源的更新时间(与if-Modified-Since相反)
Max-Forwards|最大传输逐跳数
Proxy-Authorization|代理服务器要求客户端的认证信息
Range|实体的字节范围请求
Referer|对请求中URI的原始获取方
TE|传输编码的优先级
User-Agent|HTTP客户端程序信息
### 响应首部字段
首部字段名|说明
----|----
Accept-Ranges|是否接收字节范围请求
Age|推算资源创建经过时间
ETag|资源的匹配信息
Location|令客户端重定向至指定URI
Proxy-Authenticate|代理服务器对客户端的认证信息
Retry-After|对再次发起请求的时机要求
Server|HTTP服务器的安装信息
Vary|代理服务器缓存的管理信息
WWW-Authenticate|服务器对客户端的认证信息
### 实体首部字段
首部字段名|说明
----|----
Allow|资源可支持的HTTP方法
Content-Encoding|实体主体适用的编码方式
Content-Language|实体主体的自然语言
Content-Length|实体主体的大小(单位：字节)
Content-Location|替代对应资源的URI
Content-MD5|实体主体的报文摘要
Content-Range|实体主体的位置范围
Content-Type|实体主体的媒体类型
Expires|实体主体过期的日期时间
Last-Modified|资源的最后修改日期时间
### 非HTTP/1.1首部字段
Cookie、Set-Cookie、Content-Disposition

### 其他首部字段（非标准）
X-Frame-Options、X-XSS-Protection、DNT、P3P

### End-to-end、Hop-by-hop
HTTP首部字段将定义成缓存代理和非缓存代理的行为，分成两种类型：

**端到端首部（End-to-end Header）**
分在此类别中的首部会转发给请求/响应对应的最终接收目标，且必须保存在由缓存生成的响应中,另外规定它必须被转发。

**逐跳首部(Hop-by-hop Header)**
分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发。

## HTTP/1.1通用首部字段详解
### Cache-Control
通过指定首部字段Cache-Control的指令，就能操作缓存的工作机制。指令的参数是可选的，多个指令之间通过","分割。
> Cache-Control: private, max-age=0, no-cache

#### 缓存指令一览
指令|参数|说明
----|----|----|
no-cache|无|强制向源服务器在此验证
no-store|无|不缓存请求或响应的任何内容
max-age=[秒]|必需|响应的最大Age值
max-stale(=[秒])|可省略|接收已过期的响应
min-fresh=[秒]|必需|期望在指定时间内的响应仍有效
no-transform|无|代理不可更改媒体类型
only-if-cached|无|从缓存获取资源
cache-extension|-|新指令标记(token)
#### 缓存响应指令
指令|参数|说明
----|----|----|
public|无|可向任意方提供响应的缓存
private|可省略|仅向特定用户返回响应
no-cache|可省略|缓存前必须先确认其有效性
no-store|无|不缓存请求或响应的任何内容
no-transform|无|代理不可更改媒体类型
must-revalidate|无|可缓存但必须再向源服务器进行确认
proxy-revalidate|无|要求中间缓存服务器对缓存的响应有效性再进行确认
max-age=[秒]|必需|响应的最大Age值
s-maxage=[秒]|必需|公共缓存服务器响应的最大Age值
cache-extension|-|新指令标记(token)

#### 表示是否能缓存的指令
**public指令**
> Cache-Control:public

当指定使用public指令时，则明确表明其他用户也可利用缓存。

**private指令**
> Cache-Control:private

当指定使用private指令后，响应只以特定的用户作为对象。

**no-cache指令**
> Cache-Control:no-cache

使用no-cache指令的目的是为了防止从缓存中返回过期的资源。


#### 控制可执行缓存对象的指令
**no-store指令**
> Cache-Control:no-store

当使用no-store指令时，暗示请求或响应中包含机密信息。因此，该指令规定缓存不能在本地存储请求或响应的任一部分。

#### 指定缓存期限和认证的指令
**s-maxage指令**
> Cache-Control:s-maxage=608000(单位：秒)

s-maxage指令的功能和max-age指令相同，不同之处在于s-maxage指令只适用与供多位用户使用的公共缓存服务器（代理服务器）。

**max-age指令**
> Cache-Control:max-age=608000(单位：秒)

如果发送的请求中包含max-age指令时，如果判定缓存资源的缓存时间数值比指定时间的数值更小，那么客户端就接收缓存的资源。如果指定值为0，那么缓存服务器通常需要将请求转发给源服务器。

**min-fresh指令**
> Cache-Control:min-fresh=8000(单位：秒)

min-fresh指令要求缓存服务器返回至少还未过指定时间的缓存资源。

**max-stale指令**
> Cache-Control:max-stale=8000(单位：秒)

使用max-stale可指示缓存资源，即使过期也照常接收。

**only-if-cached指令**
> Cache-Control:only-if-cached

使用only-if-cached指令表示客户端仅在缓存服务器本地缓存目标资源的情况下才会要去其返回。若发送请求缓存服务器的本地缓存无响应，则返回状态码_504Gateway Timeout_

**must-revalidate指令**
> Cache-Control:must-revalidate

使用must-revalidate指令，代理会向源服务器再次验证即将返回的响应缓存目前是否仍然有效。若代理无法连通源服务器再次获取有效资源的话，缓存必须给客户端一条_504Gateway Timeout_状态码。另外使用了must-revalidate指令会忽略请求的max-stale指令。

**proxy-revalidate指令**
> Cache-Control:proxy-revalidate

proxy-revalidate指令要求所有的缓存服务器在接收到客户端带有该指令的请求返回响应之前，必须再次验证缓存的有效性。

**no-stransform指令**
> Cache-Control:no-stransform

使用no-stransform指令规定无论是在请求还是响应中，缓存都不能改变实体主体的媒体类型。这样做可防止缓存或代理压缩图片等类似操作。

#### Cache-Control扩展
**cache-extension token**
> Cache-Control:private, suix='23'

使用cache-extension标记token，可扩展Cache-Control首部字段内的指令，如果缓存服务器不能理解suix这个指令，就会直接忽略，如果理解对缓存服务器来说是有意义的。

### Connection
Connection有两个作用

 1. 控制不再转发给代理的首部字段
> Connection:不再转发的首部字段名
 2. 管理持久连接
> Connection:close

HTTP/1.1之前默认连接都是非持久连接，想再旧版本的HTTP协议上维持持续连接，需要指定Connection字段的值为**Keep-Alive**

### Date
首部字段Date表明创建HTTP报文的日期和时间
> Date:Tue, 03 Jul 2015 12:12:12 GMT

### Pragma
Pragma是HTTP/1.1之前版本的历史遗留字段，仅作为与HTTP/1.0向后兼容而定义，该首部字段表示要求所有的中间服务器不返回缓存的资源。
> Pragma:no-cache

### Trailer
首部字段Trailer会事先说明在报文主体后记录了那些首部字段，该首部字段可应用在HTTP/1.1版本分块传输编码时
> Trailer: Expires
> Expires: Tue, 28 Sep 2015 23:59:59 GMT

### Transfer-Encoding
首部字段Transfer-Encoding规定了传输报文主体时采用的编码方式。HTTP/1.1的传输编码方式仅对分块传输编码有效。
> Transfer-Encoding: chunked

### Upgrade
首部字段Upgrade用于检测HTTP协议及其他协议是否可使用更高的版本进行通信，其参数值可以用来指定一个完全不同的通信协议。
> Upgrade: TLS/1.0,HTTP/1.1
> Connection:Upgrade

Connection的值被指定为Upgrade,该字段产生作用的Upgrade对象仅限于客户端和邻接服务器之间。使用首部字段Upgrade的时候还需要单独指定Connection的值为Upgrade,对于附有首部字段Upgrade的请求，服务器可用**101 Switching Protocols**状态码作为相应返回。

### Via
首部字段Via是为了追踪客户端与服务器之间的请求和响应报文的传输路径。首部字段Via不仅用于追踪报文的转发，还可避免请求回环的发生，所以必须在经过代理时附加该首部字段内容。
> Via: 1.0 www.baidu.com(Squid/3.1)

### Warning
Warning首部是从HTTP/1.0的相应首部演变过来的，该首部通常会告知用户一些与缓存相关的问题的警告。
> Warning: 113 www:baidu.com:8080 "有一个非法访问" Tue,03 Jul 2015 12:12:12 GMT
> Warning: [警告码][警告的主机:端口][警告内容]([日期时间])

#### HTTP/1.1警告码
警告码|警告内容|说明
----|----|----|
110|Response is stale(响应过期)|代理返回已过期的资源
111|Revalidation failed(再次验证失败)|代理再验证资源有效性时失败(服务器无法到达等原因)
112|Disconnection operation(断开连接操作)|代理与互联网连接被故意切断
113|Heuristic expiration(试探性过期)|响应的使用期超过24小时(有效缓存时间大于24小时的情况下)
199|Miscellaneous warning(杂项警告)|任意的警告内容
214|Transformation applied(使用了转换)|代理对内容编码或媒体类型等执行了某些处理时
299|Miscellaneous persistent warning(持久杂项警告)|任意的警告内容

### Accept
Accept首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可用type/subtype这种形式，一次性指定多个媒体类型。若要想给媒体类型设置优先级可使用**q=**来设置，使用(;)号分隔，范围为(0~1)，默认权重为q=0.1
> Accept: text/html,application/xml;q=0.9

例：

 文本文件
 > text/html,text/plain,text/css
 
 图片文件
 > image/jpeg,image/gif,image/png
 
 视频文件
 > video/mpeg,video/mp4
 
 二进制文件
 > application/octet-stream,application/zip

### Accept-Charset
Accept-Charset 首部字段可用来通知服务器用户代理支持的字符及字符集的相对优先顺序。
> Accept-Charset: ios-8859-1,gbk;q=0.8

### Accept-Encoding
Accept-Encoding首部字段用来告知服务器用户代理支持的内容编码及内容编码的优先顺序，可一次性指定多个内容编码。
> Accept-Encoding: gzip,deflate

例：

 - gzip
 - compress
 - deflate
 - identity
### Accept-Language
首部字段Accept-Language用来告知服务器用户代理能够处理的自然语言集，以自然语言集的相对优先级。
> Accept-Language: zh-cn;zh;q=0.7,en-us,en;q=0.3

### Authorization
首部字段Authorization是用来告知服务器，用户代理的认证信息。
> Authorization: Basic fcvrgafVGDSEGfsdahsg==

### Expect
客户端使用首部字段Expect来告知服务器，期望出现的某种特定行为，若服务器无法理解会返回**417 Expectation Failed**。
> Expect: 100-continue

### From
首部字段From用来告知服务器使用用户代理的用户的电子邮件地址。
> From: heqingsong1@gmail.com

### Host
首部字段Host会告知服务器，请求的资源所处的互联网主机名和端口号。在HTTP/1.1的版本中Host字段必须被包含。
> Host: www.google.com

### if-Match
形如像if-xxx这种形式的请求首部字段，都可以称为条件请求。服务器接收到附带条件的请求后，只有判断指定条件为真时，才会执行请求。
> if-Match: "suix"

首部字段if-Match属附带条件之一，它会告知服务器匹配资源所用的实体标记ETag值

### if-Modified-Since
首部字段if-Modified-Since，属附带条件之一，它会告知服务器if-Modified-Since字段值早于资源更新的更新时间，则希望能处理该请求。如果资源没有更新过则返回状态码**304 Not Modified**的响应。
> if-Modified-Since: Thu,15 Apr 2015 00:00:00 GMT 

### if-None-Match
首部字段if-None-Match，属附带条件之一，它与if-Match作用相反，与ETag不一致时，它就告知服务器处理该请求。
> if-None-Match: *

### if-Range
首部字段if-Range属于附带条件之一。它告知服务器若指定的if-Range字段值(ETag值或时间)和请求资源的ETage值或时间相一致时，则作为范围请求处理。
> if-Range: "123456"
> Range:bytes=500-1000

### if-Unmodified-Since
首部字段if-Unmodified-Since和首部字段if-Modified-Since的作用相反。它的作用是告知服务器，指定的请求资源只有在字段值内指定的日期时间之后，未发生更新的情况下，才能处理请求。
> if-Unmodified-Since: Thu,03 Jul 2015 00:00:00 GMT

### Max-Forwards
通过TRACE方法或OPTIONS方法，发送包含首部字段Max-Forwards的请求时，该字段以十进制整数形式指定可经过的服务器最大数目
> Max-Forwards: 10

### Proxy-Authorization
客户端发送包含首部字段Proxy-Authorization的请求时，以告知服务器认证所需要的信息。
> Proxy-Authorization: Basic dGfdaDfgjoiuoireCCD

### Range
对于只需要获取部分资源的范围请求，包含首部字段Range即可告知服务器资源的指定范围。
> Range: bytes=5001-1000

### Referer
首部字段Referer会告知服务器请求的原始资源的URI
> Referer: http://www.google.com/index.html

### TE
首部字段TE会告知服务器客户端能够处理响应的传输编码方式及相对优先级。
> TE: gzip,deflate;q=0.5

### User-Agent
首部字段User-Agent会将创建请求的浏览器和用户代理名称等信息传达给服务器
> User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 8_0 like Mac OS X) AppleWebKit/600.1.3 (KHTML, like Gecko) Version/8.0 Mobile/12A4345d Safari/600.1.4

### Accept-Ranges
首部字段Accept-Ranges是用来告知客户端服务器是否能处理范围请求，以指定获取服务器某个部分的资源。可处理返回范围请求时指定其为bytes,反之则指定其为none
> Accept-Ranges: bytes

### Age
首部字段Age能告知客户端，源服务器在多久前创建了响应，单位为毫秒
> Age: 600

### ETag
首部字段ETag能告知客户端实体标识。它是一种可将资源以字符串形式做唯一性标识的方式。
> ETag: usagi-abcd

#### 强ETag值
强ETge值，不论实体发生多么细微的变化都会改变其值。
> ETag: "usagi-1234"

#### 弱ETag值
只有资源发生了根本改变才会改变ETag值
> ETag: W/"usagi-1234"

### Location
使用首部字段Location可以将响应接收方引导至某个与请求URI位置不同的资源。该字段会配合**3xx:Redirection**的响应。
> Location: http://www.google.com/index.html

### Proxy-Authenticate
该首部字段Proxy-Authenticate会把由代理服务器所要求的认证信息发送给客户端。
> Proxy-Authenticate: Basic realm="Usagidesign Auth"

### Retry-After
首部字段Retry-After告知客户端应该在多久之后再次发送请求。主要配合**503 Service Unavailable响应**或**3xx Redirect响应**
> Retry-After: 120

### Server
首部字段Server告知客户端当前服务器上安装的HTTP服务器应用程序的信息。
> Server: Apache/2.2.17 (Unix) PHP/5.2.5

### Vary
首部字段Vary可对缓存进行控制。如果代理服务器接收到带有Vary首部字段指定获取资源的请求时，如果使用的Accept-Language字段的值相同，那么就直接从缓存返回响应。
> Vary: Accept-Language

### WWW-Authenticate
首部字段WWW-Authenticate用于HTTP访问认证。它会告知客户端适用于访问请求URI所指定的认证方案(Basic或Digest)和带参数提示的质询。状态码**401 Unauthorized**响应中，肯定带有首部字段WWW-Authenticate。
> WWW-Authenticate: Basic realm="Usagidesign Auth"

### Allow
首部字段Allow用于通知客户端能够支持Request-URI指定资源的所有HTTP方法。当服务器接收到不支持的HTTP方法的时候，会以状态码**405 Method Not Allowed**作为响应返回，同时会返回能支持的方法写入首部字段Allow后返回。
> Allow: GET, HEAD

### Content-Encoding
首部字段Content-Encoding会告知客户端服务器对实体的主体部分选用的内容编码方式。
> Content-Encoding: gzip

### Content-Language
首部字段Content-Language会告知客户端，实体主体使用的自然语言
> Content-Language: zh-CN

### Content-Length
首部字段Content-Length表明了实体主体部分的大小(单位字节)。
> Content-Length: 1500

### Content-Location
首部字段Content-Location给出与报文主体部分相对应的URI。与Location不同，Content-Location表示的是报文主体返回资源对应的URI。
> Content-Location: http://www.google.com/index.html

### Content-MD5
首部字段Content-MD5是一串由MD5算法生成的值，其目的在于检查报文主体在传输过程中是否保持完整，以及确认传输到达。
> Content-MD5: OGIEJIMKVNKNLNIWPJJPQNJKNXSJKANXZ==

### Content-Range
首部字段Content-Range用于范围请求，服务器返回响应时使用首部字段Content-Range，能告知客户端作为响应返回的实体的那个部分符合范围请求。
> Content-Range: bytes 5001 - 100000/100000

### Content-Type
首部字段Content-Type说明了实体主体内对象的媒体类型。
> Content-Type: text/html; charset=UTF-8

### Expires
首部字段Expires会将资源失效的日期告知客户端
> Expires: Wed, 04 Jul 2016 08:26:05 GMT

### Last-Modified
首部字段Last-Modified指明资源最终修改的时间。
> Last-Modified: Wed, 23 May 2016 09:09:09 GMT

### Set-Cookie
服务端设置cookie信息到客户端
> Set-Cookie:status=enable; expires=Sun, 03-Jan-16 21:52:35 GMT; domain=www.baidu.com; path=/

**expires属性**
Cookie的expires属性指定浏览器可发送Cookie的有效期。当省略的时候，其有效期仅限于维持浏览器会话时间段内。

**path属性**
Cookie的path属性可用于限制指定的Cookie的发送范围的文件目录。

**domain属性**
通过Cookie的domain属性指定的域名可做到与结尾匹配一致。

**secure属性**
Cookie的secure属性用于限制web页面仅在HTTPS安全连接时，才可以发送Cookie。

**HttpOnly属性**
Cookie的HttpOnly属性是Cookie的扩展功能，它使JS无法获得Cookie。其主要目的为防止跨站脚本攻击。

### X-Frame-Options
首部字段X-Frame-Options属于HTTP响应首部，用于控制网站内容在其他web网站的Frame标签内的显示问题。主要目的是为了防止点击劫持攻击。
> X-Frame-Options: DENY

有两个取值：DENY表示拒绝，SAMEORIGIN表示仅在同源域名下的页面匹配时许可。 

### X-XSS-Protection
首部字段X-XSS-Protection属于HTTP响应首部，它是针对跨站脚本攻击(XSS)的一种对策，用于控制浏览器XSS防护机制的开关。有两个取值：0表示无效，1表示有效。
> X-XSS-Protection: 1

### X-Forwarded-For
X-Forwarded-For 是一个扩展头。它最开始是由Squid这个缓存代理软件引入，用来表示HTTP请求端真实IP，现在已经成为事实上的标准，被各大 HTTP 代理、负载均衡等转发服务广泛使用。
> X-Forwarded-For: client, proxy1, proxy2

### X-Powered-By
扩展头X-Powered-By这个不是Apache或者Nginx输出的，而是由语言解析器或者应用程序框架输出的。这个值的意义用于告知网站是用何种语言或框架编写的。
> X-Powered-By:Express

### DNT
首部字段DNT属于HTTP请求首部，其中DNT是Do Not Track的简称，意为拒绝个人信息被收集，是表示拒绝被精准广告跟踪的一种方法。有两个取值：0同意被追踪，1拒绝被追踪。
> DNT: 1

### P3P
首部字段P3P属于HTTP响应首部，通过利用P3P技术，可以让web网站上的个人隐私变成一种仅供程序可以理解的形式，以达到保护用户隐私的目的。
> P3P: CP=/"CURa ADMa DEVa PSAo PSDo OUR BUS UNI PUR INT DEM STA PRE COM NAV OTC NOI DSP COR/"
