# 跨域资源共享 CORS
* [基本概念](#基本概念)
    - [同源策略](#同源策略)
    - [什么是CORS](#什么是CORS)
    - [CORS基本配置](#CORS基本配置)

* [进阶](#进阶)
    - [JSONP](#JSONP)
    - [CORS常见实践](#CORS常见实践)
* [常见问题](#常见问题)
* [参考资料](#参考资料)  

## 基本概念
### 同源策略
https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy

同源策略是一个重要的安全策略，它用于限制一个origin的文档或者它加载的脚本如何能与另一个源的资源进行交互。它能帮助阻隔恶意文档，减少可能被攻击的媒介。

如果两个 URL 的 protocol、port (如果有指定的话)和 host 都相同的话，则这两个 URL 是同源。这个方案也被称为“协议/主机/端口元组”，或者直接是 “元组”。（“元组” 是指一组项目构成的整体，双重/三重/四重/五重/等的通用形式）。

### 什么是CORS
http://www.ruanyifeng.com/blog/2016/04/cors.html

CORS （Cross-Origin Resource Sharing，跨域资源共享）是一个系统，它由一系列传输的HTTP头组成，这些HTTP头决定浏览器是否阻止前端 JavaScript 代码获取跨域请求的响应。

同源安全策略 默认阻止“跨域”获取资源。但是 CORS 给了web服务器这样的权限，即服务器可以选择，允许跨域请求访问到它们的资源。

### CORS基本配置
https://developer.mozilla.org/zh-CN/docs/Glossary/CORS
* Access-Control-Allow-Origin
    - 指示请求的资源能共享给哪些域。
* Access-Control-Allow-Credentials
    - 指示当请求的凭证标记为 true 时，是否响应该请求。
* Access-Control-Allow-Headers
    - 用在对预请求的响应中，指示实际的请求中可以使用哪些 HTTP 头。
* Access-Control-Allow-Methods
    - 指定对预请求的响应中，哪些 HTTP 方法允许访问请求的资源。
* Access-Control-Expose-Headers
    - 指示哪些 HTTP 头的名称能在响应中列出。
* Access-Control-Max-Age
    - 指示预请求的结果能被缓存多久。
* Access-Control-Request-Headers
    - 用于发起一个预请求，告知服务器正式请求会使用那些 HTTP 头。
* Access-Control-Request-Method
    - 用于发起一个预请求，告知服务器正式请求会使用哪一种 HTTP 请求方法。
* Origin
    - 指示获取资源的请求是从什么域发起的。
## 进阶
### JSONP
https://baike.baidu.com/item/jsonp/493658

### CORS常见实践
* node包
    - https://github.com/expressjs/cors
* nginx
    - https://github.com/expressjs/cors
    
## 常见问题
* 如何阻止跨源访问
    - 阻止跨域写操作，只要检测请求中的一个不可推测的标记(CSRF token)即可，这个标记被称为 Cross-Site Request Forgery (CSRF) 标记。你必须使用这个标记来阻止页面的跨站读操作。
    - 阻止资源的跨站读取，需要保证该资源是不可嵌入的。阻止嵌入行为是必须的，因为嵌入资源通常向其暴露信息。
    - 阻止跨站嵌入，需要确保你的资源不能通过以上列出的可嵌入资源格式使用。浏览器可能不会遵守 Content-Type 头部定义的类型。例如，如果您在HTML文档中指定 <script> 标记，则浏览器将尝试将标签内部的 HTML 解析为JavaScript。 当您的资源不是您网站的入口点时，您还可以使用CSRF令牌来防止嵌入。

* 简单请求与非简单请求
浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。

只要同时满足以下两大条件，就属于简单请求。

（1) 请求方法是以下三种方法之一：

    - HEAD
    - GET
    - POST
    
（2）HTTP的头信息不超出以下几种字段：
    
    - Accept
    - Accept-Language
    - Content-Language
    - Last-Event-ID
    - Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain

跨域处理中，针对非简单请求，有个预检请求

## 参考资料
* https://zhuanlan.zhihu.com/p/24411090
* https://developer.mozilla.org/zh-CN/docs/Glossary/CORS
* http://www.ruanyifeng.com/blog/2016/04/cors.html
