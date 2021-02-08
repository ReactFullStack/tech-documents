# API
* [Authentication](#Authentication)
* [OAuth](#OAuth)
* [Basic](#Basic)
* [Token](#Token)
    * [背景](#背景)
    * [概念](#概念)
    * [原理](#原理)
    * [身份认证概述](#身份认证概述)
    * [基于token机制的身份认证](#基于token机制的身份认证)
    * [APP利用token机制进行身份认证](#APP利用token机制进行身份认证)
    * [存储](#存储)
    * [加密](#加密)
    * [优势](#优势)
    * [使用说明](#使用说明)
* [JWT](#JWT)
    * [JWT原理](#JWT原理)
    * [数据结构](#数据结构)
    * [使用](#使用)
    * [几个特点](#几个特点)
* [RESTFUL_API](#RESTFUL_API)
    - [基本概念](#基本概念)
    - [特征和优点](#特征和优点)
    - [最佳实践](#最佳实践)
* [常见问题](#常见问题)
* [参考资料](#参考资料)  

## Authentication
## OAuth
## Basic
## Token

### 背景
- Token的引入
    - Token是在客户端频繁向服务端请求数据，服务端频繁的去数据库查询用户名和密码并进行对比，判断用户名和密码正确与否，并作出相应提示，在这样的背景下，Token便应运而生。
- Token的定义
    - Token是服务端生成的一串字符串，以作客户端进行请求的一个令牌，当第一次登录后，服务器生成一个Token便将此Token返回给客户端，以后客户端只需带上这个Token前来请求数据即可，无需再次带上用户名和密码。
- 使用Token的目的
    - 是为了减轻服务器的压力，减少频繁的查询数据库，使服务器更加健壮。

### 概念
token的意思是“令牌”，是服务端生成的一串字符串，作为客户端进行请求的一个标识。

当用户第一次登录后，服务器生成一个token并将此token返回给客户端，以后客户端只需带上这个token前来请求数据即可，无需再次带上用户名和密码。

简单token的组成；uid(用户唯一的身份标识)、time(当前时间的时间戳)、sign（签名，token的前几位以哈希算法压缩成的一定长度的十六进制字符串。为防止token泄露）。

### 原理
1. 将荷载payload，以及Header信息进行Base64加密，形成密文payload密文，header密文。
2. 将形成的密文用句号链接起来，用服务端秘钥进行HS256加密，生成签名.
3. 将前面的两个密文后面用句号链接签名形成最终的token返回给服务端
    - 用户请求时携带此token（分为三部分，header密文，payload密文，签名）到服务端，服务端解析第一部分（header密文），用Base64解密，可以知道用了什么算法进行签名，此处解析发现是HS256。
    - 服务端使用原来的秘钥与密文(header密文+"."+payload密文)同样进行HS256运算，然后用生成的签名与token携带的签名进行对比，若一致说明token合法，不一致说明原文被修改。
    - 判断是否过期，客户端通过用Base64解密第二部分（payload密文），可以知道荷载中授权时间，以及有效期。通过这个与当前时间对比发现token是否过期。
![token原理](https://note.youdao.com/yws/public/resource/9d567cc709c6dbf691736a27dfa6b16c/xmlnote/WEBRESOURCEc78f4bdac19fee6bf35b048275500478/14681)

### 身份认证概述
 
由于HTTP是一种没有状态的协议，它并不知道是谁访问了我们的应用。这里把用户看成是客户端，客户端使用用户名还有密码通过了身份验证，不过下次这个客户端再发送请求时候，还得再验证一下。

通用的解决方法就是，当用户请求登录的时候，如果没有问题，在服务端生成一条记录，在这个记录里可以说明登录的用户是谁，然后把这条记录的id发送给客户端，客户端收到以后把这个id存储在cookie里，下次该用户再次向服务端发送请求的时候，可以带上这个cookie，这样服务端会验证一下cookie里的信息，看能不能在服务端这里找到对应的记录，如果可以，说明用户已经通过了身份验证，就把用户请求的数据返回给客户端。

以上所描述的过程就是利用session，那个id值就是sessionid。我们需要在服务端存储为用户生成的session，这些session会存储在内存，磁盘，或者数据库。

### 基于token机制的身份认证
使用token机制的身份验证方法，在服务器端不需要存储用户的登录记录。大概的流程：

客户端使用用户名和密码请求登录。
服务端收到请求，验证用户名和密码。
验证成功后，服务端会生成一个token，然后把这个token发送给客户端。
客户端收到token后把它存储起来，可以放在cookie或者Local Storage（本地存储）里。
客户端每次向服务端发送请求的时候都需要带上服务端发给的token。
服务端收到请求，然后去验证客户端请求里面带着token，如果验证成功，就向客户端返回请求的数据。
利用token机制进行登录认证，可以有以下方式：

1. 用设备mac地址作为token

客户端：客户端在登录时获取设备的mac地址，将其作为参数传递到服务端

服务端：服务端接收到该参数后，便用一个变量来接收，同时将其作为token保存在数据库，并将该token设置到session中。客户端每次请求的时候都要统一拦截，将客户端传递的token和服务器端session中的token进行对比，相同则登录成功，不同则拒绝。

此方式客户端和服务端统一了唯一的标识，并且保证每一个设备拥有唯一的标识。缺点是服务器端需要保存mac地址；优点是客户端无需重新登录，只要登录一次以后一直可以使用，对于超时的问题由服务端进行处理。

2. 用sessionid作为token

客户端：客户端携带用户名和密码登录

服务端：接收到用户名和密码后进行校验，正确就将本地获取的sessionid作为token返回给客户端，客户端以后只需带上请求的数据即可。

此方式的优点是方便，不用存储数据，缺点就是当session过期时，客户端必须重新登录才能请求数据。

当然，对于一些保密性较高的应用，可以采取两种方式结合的方式，将设备mac地址与用户名密码同时作为token进行认证。

### APP利用token机制进行身份认证
用户在登录APP时，APP端会发送加密的用户名和密码到服务器，服务器验证用户名和密码，如果验证成功，就会生成相应位数的字符产作为token存储到服务器中，并且将该token返回给APP端。

以后APP再次请求时，凡是需要验证的地方都要带上该token，然后服务器端验证token，成功返回所需要的结果，失败返回错误信息，让用户重新登录。其中，服务器上会给token设置一个有效期，每次APP请求的时候都验证token和有效期。

### 存储
token可以存到数据库中，但是有可能查询token的时间会过长导致token丢失（其实token丢失了再重新认证一个就好，但是别丢太频繁，别让用户没事儿就去认证）。

为了避免查询时间过长，可以将token放到内存中。这样查询速度绝对就不是问题了，也不用太担心占据内存，就算token是一个32位的字符串，应用的用户量在百万级或者千万级，也是占不了多少内存的。

### 加密
token是很容易泄露的，如果不进行加密处理，很容易被恶意拷贝并用来登录。加密的方式一般有：

在存储的时候把token进行对称加密存储，用到的时候再解密。
文章最开始提到的签名sign：将请求URL、时间戳、token三者合并，通过算法进行加密处理。

### 优势

1. 无状态、可扩展

在客户端存储的 token 是无状态的，并且能够被扩展。基于这种无状态和不存储Session信息，负载均衡服务器 能够将用户的请求传递到任何一台服务器上，因为服务器与用户信息没有关联。相反在传统方式中，我们必须将请求发送到一台存储了该用户 session 的服务器上（称为Session亲和性），因此当用户量大时，可能会造成 一些拥堵。使用 token 完美解决了此问题。

2. 安全性

请求中发送 token 而不是 cookie，这能够防止 CSRF(跨站请求伪造) 攻击。即使在客户端使用 cookie 存储 token，cookie 也仅仅是一个存储机制而不是用于认证。另外，由于没有 session，让我们少我们不必再进行基于 session 的操作。 

Token 是有时效的，一段时间之后用户需要重新验证。我们也不一定需要等到token自动失效，token有撤回的操作，通过 token revocataion可以使一个特定的 token 或是一组有相同认证的 token 无效。

3. 可扩展性

使用 Tokens 能够与其它应用共享权限。例如，能将一个博客帐号和自己的QQ号关联起来。当通过一个 第三方平台登录QQ时，我们可以将一个博客发到QQ平台中。

使用 token，可以给第三方应用程序提供自定义的权限限制。当用户想让一个第三方应用程序访问它们的数据时，我们可以通过建立自己的API，给出具有特殊权限的tokens。

4. 多平台与跨域

我们已经讨论了CORS (跨域资源共享)。当我们的应用和服务不断扩大的时候，我们可能需要通过多种不同平台或其他应用来接入我们的服务。

可以让我们的API只提供数据，我们也可以从CDN提供服务（Having our API just serve data, we can also make the design choice to serve assets from a CDN.）。 在为我们的应用程序做了如下简单的配置之后，就可以消除 CORS 带来的问题。只要用户有一个通过了验证的token，数据和资源就能够在任何域上被请求到。

```
 Access-Control-Allow-Origin: * 
```

5. 基于标准

有几种不同方式来创建 token。最常用的标准就是 JSON Web Tokens。很多语言都支持它。

### 使用说明
1. 生成一个由base64编码的token，该token由Header，Payload，Signature组成。

2. token作为用户请求的标识，客户端保存这token的全部信息。服务端只需要保存token的Signature部分。

3. 服务端把token的Signature存于redis和服务器的数据库中。

4. 客户端请求的数据附带token，服务端拿到token，首先校验token，以防token伪造。校验规则如下：

    - 拆分出token的Header，Payload，Signature。
    - 校验Signature，通过token的header和payload生成Signature，看看生成的Signature是否和客户端附带上来的Signature一致。如果一致继续请求操作，不一致则打回操作
    - 查看Signature是否存在服务器的redis和数据库中。如果不存在则打回请求操作

![使用](https://note.youdao.com/yws/public/resource/9d567cc709c6dbf691736a27dfa6b16c/xmlnote/WEBRESOURCE207446f19d649296d2b65f14f3e2436d/14683)


## JWT

### JWT原理
- 全称：JSON WEB TOKENS
- JWT 的原理是，服务器认证以后，生成一个 JSON 对象，发回给用户，就像下面这样。

```
{
  "姓名": "张三",
  "角色": "管理员",
  "到期时间": "2018年7月1日0点0分"
}
```

- 以后，用户与服务端通信的时候，都要发回这个 JSON 对象。服务器完全只靠这个对象认定用户身份。为了防止用户篡改数据，服务器在生成这个对象的时候，会加上签名（详见后文）。

- 服务器就不保存任何 session 数据了，也就是说，服务器变成无状态了，从而比较容易实现扩展。

### 数据结构
实际的 JWT 大概就像下面这样。

- 它是一个很长的字符串，中间用点（.）分隔成三个部分。注意，JWT 内部是没有换行的，这里只是为了便于展示，将它写成了几行。
- JWT 的三个部分: Header.Payload.Signature

    - Header（头部）
    - Payload（负载）
    - Signature（签名）

![JWT TOKEN](https://note.youdao.com/yws/public/resource/9d567cc709c6dbf691736a27dfa6b16c/xmlnote/WEBRESOURCEdc038de030624df24086a5025d97f0d8/14685)

#### 1. Header
Header 部分是一个 JSON 对象，描述 JWT 的元数据，通常是下面的样子。

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

上面代码中，alg属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；typ属性表示这个令牌（token）的类型（type），JWT 令牌统一写为JWT。

最后，将上面的 JSON 对象使用 Base64URL 算法（详见后文）转成字符串。

#### 2. Payload
Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据。JWT 规定了7个官方字段，供选用。

    - iss (issuer)：签发人
    - exp (expiration time)：过期时间
    - sub (subject)：主题
    - aud (audience)：受众
    - nbf (Not Before)：生效时间
    - iat (Issued At)：签发时间
    - jti (JWT ID)：编号
除了官方字段，你还可以在这个部分定义私有字段，下面就是一个例子。


```
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```
注意，JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

这个 JSON 对象也要使用 Base64URL 算法转成字符串。

#### 3. Signature
Signature 部分是对前两部分的签名，防止数据篡改。

首先，需要指定一个密钥（secret）。这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用"点"（.）分隔，就可以返回给用户。

#### 4. Base64URL
前面提到，Header 和 Payload 串型化的算法是 Base64URL。这个算法跟 Base64 算法基本类似，但有一些小的不同。

JWT 作为一个令牌（token），有些场合可能会放到 URL（比如 api.example.com/?token=xxx）。Base64 有三个字符+、/和=，在 URL 里面有特殊含义，所以要被替换掉：=被省略、+替换成-，/替换成_ 。这就是 Base64URL 算法。

### 使用
客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。

此后，客户端每次与服务器通信，都要带上这个 JWT。你可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息Authorization字段里面。

Authorization: Bearer <token>
另一种做法是，跨域的时候，JWT 就放在 POST 请求的数据体里面。

### 几个特点
- JWT 默认是不加密，但也是可以加密的。生成原始 Token 以后，可以用密钥再加密一次。
- JWT 不加密的情况下，不能将秘密数据写入 JWT。
- JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数。
- JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。
- JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。为了减少盗用，JWT 的有效期应该设置得比较短。对于一些比较重要的权限，使用时应该再次对用户进行认证。
- 为了减少盗用，JWT 不应该使用 HTTP 协议明码传输，要使用 HTTPS 协议传输。

## RESTFUL_API

### 基本概念
- [理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)
- 全称：Representational State Transfer 
    - 译文:表现层状态转化
    - 如果一个架构符合REST原则，就称它为RESTful架构
- 什么是RESTful架构：
    - 每一个URI代表一种资源；
    - 客户端和服务器之间，传递这种资源的某种表现层；
    - 客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。
- RESTful架构有一些典型的设计误区
    - 最常见的一种设计错误，就是URI包含动词。因为"资源"表示一种实体，所以应该是名词，URI不应该有动词，动词应该放在HTTP协议中。
```
// 1.1 错误设计
/posts/show/1
其中show是动词

// 1.1 正确设计
GET /posts/1
用GET方法表示show

// 1.2 错误设计
POST /accounts/1/transfer/500/to/2
如果某些动作是HTTP动词表示不了的，你就应该把动作做成一种资源。比如网上汇款，从账户1向账户2汇款500元，错误的URI是：

// 1.2 正确设计
POST /transaction?from=1&to=2&amount=500.00 HTTP/1.1
Host: 127.0.0.1
正确的写法是把动词transfer改成名词transaction，资源不能是动词，但是可以是一种服务
```

### 特征和优点

1. 客户端-服务器（Client-Server）：提供服务的服务器和使用服务的客户端分离解耦；
    - 提高客户端的便捷性（操作简单）
    - 简化服务器提高可伸缩性（高性能、低成本）
    - 允许客户端服务端分组优化，彼此不受影响

2. 无状态（Stateless）：来自客户的每一个请求必须包含服务器处理该请求所需的所有信息（请求信息唯一性）；
    - 提高可见性（可以单独考虑每个请求）
    - 提高可靠性（更容易故障恢复）
    - 提高了可扩展性（降低了服务器资源使用）

3. 可缓存（Cachable）：服务器必须让客户端知道请求是否可以被缓存？如果可以，客户端可以重用之前的请求信息发送请求；
    - 减少交互连接数
    - 减少连接过程的网络时延

4. 分层系统（Layered System）：允许服务器和客户端之间的中间层（代理，网关等）代替服务器对客户端的请求进行回应，而客户端不需要关心与它交互的组件之外的事情；
    - 提高了系统的可扩展性
    - 简化了系统的复杂性

5. 统一接口（Uniform Interface）：客户和服务器之间通信的方法必须是统一化的。（例如：GET,POST,PUT.DELETE）
    - 提高交互的可见性
    - 鼓励单独优化改善组件

6. 支持按需代码（Code-On-Demand，可选）：服务器可以提供一些代码或者脚本并在客户的运行环境中执行。
    - 提高可扩展性

### 最佳实践
- [RESTful API 资源命名指南](http://restful.p2hp.com/home/resource-naming)
- [RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
##### 1. 协议
- API与用户的通信协议，总是使用HTTPs协议。

##### 2. 域名
- 应该尽量将API部署在专用域名之下。
        https://api.example.com
- 如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。
        https://example.org/api/

##### 3. 版本（Versioning）
- 应该将API的版本号放入URL。

```html
http://www.example.com/app/1.1/foo

http://www.example.com/app/2.0/foo
```
- 另一种做法是将版本号放入HTTP头信息里，这样不如放入URL方便和直观，[Github](https://developer.github.com/v3/media/#request-specific-version)就采用了这种做法。
    - 因为不同的版本，可以理解成同一种资源的不同表现形式，所以应该采用同一个URL。版本号可以在HTTP请求头信息的Accept字段中进行区分（参见[Versioning REST Services](http://www.informit.com/articles/article.aspx?p=1566460)）：

```html
Accept: vnd.example-com.foo+json; version=1.0

Accept: vnd.example-com.foo+json; version=1.1

Accept: vnd.example-com.foo+json; version=2.0
```
##### 4. 路径（Endpoint）
- 路径又称"终点"（endpoint），表示API的具体网址。
- 在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

##### 资源作为网址，只能有名词，不能有动词，而且所用的名词往往与数据库的表名对应
```javascript
举例来说，以下是不好的例子:

/getProducts
/listOrders
/retreiveClientByOrder?orderId=1
对于一个简洁结构，你应该始终用名词。 此外，利用的HTTP方法可以分离网址中的资源名称的操作。

GET /products ：返回所有产品清单
POST /products ：添加一个产品
GET /products/4 ：获取产品 4
PATCH（或）PUT /products/4 ：更新产品 4
```
##### API中的名词应该使用复数。无论子资源或者所有资源 举例来说，获取产品的API可以这样定义
```
获取单个产品：http://127.0.0.1:8080/AppName/rest/products/1
获取所有产品: http://127.0.0.1:8080/AppName/rest/products
```

##### 5. HTTP动词
- 对于资源的具体操作类型，由HTTP动词表示。
- 常用的HTTP动词有下面五个（括号里是对应的SQL命令）。
    - GET（SELECT）：从服务器取出资源（一项或多项）。
    - POST（CREATE）：在服务器新建一个资源。
    - PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
    - PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
    - DELETE（DELETE）：从服务器删除资源。
- 还有三个不常用的HTTP动词。
    - PATCH（UPDATE）：在服务器更新(更新)资源（客户端提供改变的属性）。
    - HEAD：获取资源的元数据。
    - OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的

```javascript
下面是一些例子。

GET /zoos：列出所有动物园
POST /zoos：新建一个动物园
GET /zoos/ID：获取某个指定动物园的信息
PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/ID：删除某个动物园
GET /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```

##### 6. 过滤信息（Filtering）
- 如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。
- query_string 查询字符串,地址栏后面问号后面的数据,格式: name=xx&sss=xxx
- 参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，GET /zoos/ID/animals 与 GET /animals?zoo_id=ID 的含义是相同的。
```javascript
下面是一些常见的参数。

?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，GET /zoo/ID/animals 与 GET /animals?zoo_id=ID 的含义是相同的。

```
##### 7. 状态码（Status Codes）
- 服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

```javascript
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功。
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
状态码的完全列表参见这里。
```

##### 8. 错误处理（Error handling）
- 如果状态码是4xx，就应该向用户返回出错信息。一般来说，返回的信息中将error作为键名，出错信息作为键值即可。

```javascript
{
    error: "Invalid API key"
}
```
##### 9. 返回结果
- 针对不同操作，服务器向用户返回的结果应该符合以下规范。
    - GET /collection：返回资源对象的列表（数组）
    - GET /collection/resource：返回单个资源对象
    - POST /collection：返回新生成的资源对象
    - PUT /collection/resource：返回完整的资源对象
    - PATCH /collection/resource：返回完整的资源对象
    - DELETE /collection/resource：返回一个空文档

##### 10. 超媒体（Hypermedia API）
- RESTful API最好做到Hypermedia（即返回结果中提供链接，连向其他API方法），使得用户不查文档，也知道下一步应该做什么。

```javascript
比如，Github的API就是这种设计，访问api.github.com会得到一个所有可用API的网址列表。

{
"current_user_url": "https://api.github.com/user",
"authorizations_url": "https://api.github.com/authorizations",
// ...
}

从上面可以看到，如果想获取当前用户的信息，应该去访问api.github.com/user，然后就得到了下面结果。

{
  "message": "Requires authentication",
  "documentation_url": "https://developer.github.com/v3"
}

上面代码表示，服务器给出了提示信息，以及文档的网址。
```

## 常见问题

## 参考资料
- [理解RESTful架构 - 阮一峰](http://www.ruanyifeng.com/blog/2011/09/restful.html)
- [什么是REST](http://restful.p2hp.com/)
- [理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)