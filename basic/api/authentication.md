# API
- [API](#api)
  - [OAuth](#oauth)
    - [什么是oauth](#什么是oauth)
    - [背景](#背景)
    - [中心组件](#中心组件)
      - [1. OAuth Scopes](#1-oauth-scopes)
      - [2. Actors](#2-actors)
      - [3. Client](#3-client)
      - [4. OAuth Tokens](#4-oauth-tokens)
      - [5. OAuth有两个流程](#5-oauth有两个流程)
      - [6. OAuth Flows](#6-oauth-flows)
    - [安全性建议](#安全性建议)
    - [OpenID](#openid)
      - [基本概念](#基本概念)
      - [OpenID Connect流程主要涉及如下几个步骤：](#openid-connect流程主要涉及如下几个步骤)
  - [Basic](#basic)
  - [Token](#token)
    - [背景](#背景-1)
    - [概念](#概念)
    - [原理](#原理)
    - [身份认证概述](#身份认证概述)
    - [基于token机制的身份认证](#基于token机制的身份认证)
    - [APP利用token机制进行身份认证](#app利用token机制进行身份认证)
    - [存储](#存储)
    - [加密](#加密)
    - [优势](#优势)
    - [使用说明](#使用说明)
  - [JWT](#jwt)
    - [JWT原理](#jwt原理)
    - [数据结构](#数据结构)
      - [1. Header](#1-header)
      - [2. Payload](#2-payload)
      - [3. Signature](#3-signature)
      - [4. Base64URL](#4-base64url)
    - [使用](#使用)
    - [几个特点](#几个特点)
  - [常见问题](#常见问题)
  - [参考资料](#参考资料)

## OAuth

- [理解OAuth 2.0](https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)

### 什么是oauth

OAuth 不是一个API或者服务，而是一个验证授权(Authorization)的开放标准，所有人都有基于这个标准实现自己的OAuth。

更具体来说，OAuth是一个标准，app可以用来实现secure delegated access. OAuth基于HTTPS，以及APIs，Service应用使用access token来进行身份验证。

OAuth主要有OAuth 1.0a和OAuth 2.0两个版本，并且二者完全不同，且不兼容。OAuth2.0 是目前广泛使用的版本，我们多数谈论OAuth时，为OAuth2.0。

### 背景

在OAuth之前，HTTP Basic Authentication, 即用户输入用户名，密码的形式进行验证, 这种形式是不安全的。OAuth的出现就是为了解决访问资源的安全性以及灵活性。OAuth使得第三方应用对资源的访问更加安全。

### 中心组件

OAuth 主要下面中心组件构成 (Central Components), 接下来会依次介绍如下这些组件。

1. Scopes and Consent
2. Actors
3. Clients
4. Tokens
5. Authorization Server
6. Flows

#### 1. OAuth Scopes
Scopes即Authorizaion时的一些请求权限，即与access token绑定在一起的一组权限。OAuth Scopes将授权策略（Authorization policy decision）与授权执行分离开来。并会很明确的表示OAuth Scopes将会获得的权限范围。

#### 2. Actors
OAuth的流程中，主要有如下四个角色。其关系如下图所示：

    - Resource Owner: 用户拥有资源服务器上面的数据。例如：我是一名Facebook的用户，我拥有我的Facebook 个人简介的信息。
    - Resource Server: 存储用户信息的API Service
#### 3. Client
想要访问用户的客户端
    
    - Authorization Server: OAuth的主要引擎，授权服务器，获取token。

#### 4. OAuth Tokens
    - Access token: 即客户端用来请求Resource Server(API). Access tokens通常是short-lived短暂的。access token是short-lived, 因此没有必要对它做revoke, 只需要等待access token过期即可。
    - Refresh token: 当access token过期之后refresh token可以用来获取新的access token。refresh token是long-lived。refresh token可以被revoke。

Token从Authorization server上的不同的endpoint获取。主要两个endpoint为authorize endpoint和token endpoint. authorize endpoint主要用来获得来自用户的许可和授权(consent and authorization)，并将用户的授权信息传递给token endpoint。token endpoint对用户的授权信息，处理之后返回access token和refresh token。 当access token过期之后，可以使用refresh token去请求token endpoint获取新的token。（开发者在开发endpoint时，需要维护token的状态，refresh token rotate）

#### 5. OAuth有两个流程
- 获取Authorization
- 获取Token

    - 这两个流程发送在不同的channel，Authorization发生在Front Channel（发生在用户浏览器）而Token发生在Back Channel。
    - Front Channel: 客户端通过浏览器发送Authorization请求，由浏览器重定向到Authorization Server上的Authorization Endpoint，由Authorization Server返回对话框，并询问“是否允许这个应用获取如下权限”。Authorization通过结束后通过浏览器重定向到回调URL（Callback URL）。
    - Back Channel: 获取Token之后，token应有由客户端应用程序使用，并与资源服务器（Resource Service）进行交互。

- Front Channel

```javascript

Request:

GET https://accounts.google.com/o/oauth2/auth?scope=gmail.insert gmail.send
&redirect_uri=https://app.example.com/oauth2/callback
&response_type=code&client_id=812741506391
&state=af0ifjsldkj

GET请求，指定了redirect_uri, 完成authorization之后，需要重定向到哪里。 response_type表明是用哪种OAuth flow进行验证。State为安全标志位，类似于XRSF，更多XRSF可以了解Cross-Site-Request-Forgery (跨站请求伪造)。

Response:

HTTP/1.1 302 Found
Location: https://app.example.com/oauth2/callback?
code=MsCeLvIaQm6bTrgtp7&state=af0ifjsldkj

返回的code即表明，已经获得授权authorization grant. state用来保证不是伪造的请求，和request传入的保持一致。
```
- Back Channel

```javascript
Request:

POST /oauth2/v3/token HTTP/1.1
Host: www.googleapis.com
Content-Type: application/x-www-form-urlencoded

code=MsCeLvIaQm6bTrgtp7&client_id=812741506391&client_secret={client_secret}&redirect_uri=https://app.example.com/oauth2/callback&grant_type=authorization_code
请求的参数，code即为上一步front channel所返回的code。

Response:

{
  "access_token": "2YotnFZFEjr1zCsicMWpAA",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "tGzv3JOkF0XG5Qx2TlKWIA"
}
当获取到access token之后，就可以在Authorization header中使用token，进行对资源服务器的请求访问

curl -H "Authorization: Bearer 2YotnFZFEjr1zCsicMWpAA" \
  https://www.googleapis.com/gmail/v1/users/1444587525/messages
```

#### 6. OAuth Flows
- implicit flow: 也称之为 2 Legged OAuth 所有OAuth的过程都在浏览器中完成，且access token通过authorization request (front channel only) 直接返回。不支持refresh token。安全性不高。
- Authorization code: 也称之为 3 Legged OAuth。使用front channel和back channel。front channel负责authorization code grant。back channel负责将authorization code换成（exchange）access token以及refresh token。
- Client Credential flow: 对于server-to-server的场景。通常使用这种模式。在这种模式下要保证client secret不会被泄露。
- Resource Owner Password Flow：类似于直接用户名，密码的模式，不推荐使用。

### 安全性建议
- 使用CSRF token。state参数保证整个流程的完整性
- 重定向URL（redirect URIs）要在白名单内
- 通过client ID将authorization grant和token request确保在同一个client上发生
- 对于保密的client（confidential client），确保client secret不被泄露。不要将secret随代码一起发布

### OpenID
- OpenID Connect

#### 基本概念
OpenID Connect 是在OAuth2.0 协议基础上增加了身份验证层 （identity layer）。OAuth 2.0 定义了通过access token去获取请求资源的机制，但是没有定义提供用户身份信息的标准方法。OpenID Connect作为OAuth2.0的扩展，实现了Authentication的流程。OpenID Connect根据用户的 id_token 来验证用户，并获取用户的基本信息。

id_token通常是JWT（Json Web Token），JWT有三部分组成，header，body，signature。header主要用来声明使用的算法，声明claim在body中，并且签名在signature中。OpenID Connection 在OAuth2.0 的基础上额外增加了UserInfo的Endpoint。id_token作为访问UserInfo Endpoint的凭证来获取用户的基本信息（profile，email，phone），并验证用户。

#### OpenID Connect流程主要涉及如下几个步骤：

发现获取OIDC metadata
1. 执行OAuth流程，获取id_token和access_token。例如：在 Authorization code模式下即为通过code来换取id_token和access_token。
1. 获取JWT签名（signature key）并且可选的动态的注册客户端应用
1. 基于日期签名来本地验证JWT id_token，或者将id_token发给后端backend进行验证
1. 根据id_token通过UserInfo Endpoint获取用户信息，根据access_token获取用户其他资源信息

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


## 常见问题

- [OpenID 和 OAuth 有什么区别？](https://www.zhihu.com/question/19628327)
- 

## 参考资料
- [理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
- [OAuth2.0 详解](https://zhuanlan.zhihu.com/p/89020647)
- [OAuth](http://en.wikipedia.org/wiki/OAuth)
- [学习文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-protocols)
- [简单流程图](https://www.cnblogs.com/flashsun/p/7424071.html)