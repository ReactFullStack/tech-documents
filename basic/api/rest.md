# RESTFUL API
* [基本概念](#基本概念)
* [特征和优点](#特征和优点)
* [最佳实践](#最佳实践)
* [常见问题](#常见问题)
* [参考资料](#参考资料)

## 基本概念
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

## 特征和优点

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

## 最佳实践
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