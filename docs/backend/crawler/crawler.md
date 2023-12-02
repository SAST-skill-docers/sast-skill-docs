# 爬虫


??? info "我需要什么前置知识？"

    我们期待您已经了解并掌握了 Python 的基础语法以及 Web 相关知识，如果您对此有任何疑问，可以参考：

    - [科协技能引导文档 Python 部分](https://docs.net9.org/languages/python/)
    - [科协技能引导文档 Web 部分](https://docs.net9.org/basic/web/)

    此外，我们还提供了一些 HTTP 相关知识以供参考：

    - [HTTP 协议的历史演变和设计思路](https://www.ruanyifeng.com/blog/2016/08/http.html)
    - [HTTP 有哪些方法可供我们调用？](https://www.w3schools.com/tags/ref_httpmethods.asp)
    - [HTTP 各种响应状态码的含义](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)
    - [HTTP 的 Cookie 是什么？他有怎样的结构？](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)

    如果您在阅读本文档时遇到了任何困难或疑问，欢迎在下方评论区提问讨论。当然，您也可以查询相关官方文档或者选择尝试向 ChatGPT 提问。

## 什么是爬虫？

在解释什么是爬虫之前，请先让我们搞明白什么是**上网**。上网可以被简单地认为是“浏览器按照用户意愿，向服务器发送一系列请求，并将服务器的响应呈现给用户”。

??? example "一个简单的例子"

    您浏览本文档时就在上网，而这一过程所做的无非是您通过浏览器向我们的服务器发出如下请求：“我想要学习爬虫相关内容，请把相关教程发送到我的设备上。”

    在接收到您的请求后，服务器会传回一个 HTML 文件以及一些其他附属文件，最后在浏览器中展现为您现在所阅读的文档。

爬虫是一种可以模拟网页浏览器向网站发送并处理请求的自动化脚本，帮助我们获得服务器的数据，进而用于其他需求。借助于爬虫，理论上我们可以依照需求处理一切与网络请求有关的内容，比如实现某某网站的抢票、实现某某网站音视频及文字的批量获取。

## 爬前须知

截至目前，我们已经搞清楚了何为爬虫以及为何爬虫。但在正式开始爬虫的教学之前，我们还要了解什么不可为以及什么可为。

### 不可为

首先，我们爬取的数据来自互联网，而互联网中存储着大量的个人用户信息、企业信息。理论上只要我们的爬虫技术足够高超，我们就能获得互联网上存储的任何信息。然而对于用户未授权数据的爬取是违反相关法律法规的，因此我们在爬取数据前一定要确保自身行为的合法性。

??? danger "爬虫虽好，但不要贪爬哟"

    电影《孤注一掷》中的程序员男主被缅北电诈集团强迫从事非法爬虫与网络入侵，为犯罪分子提供了巨额非法收入。“只要爬虫技术高，薪资千万不用愁。”但面对金钱利益诱惑，我们一定要守住法律与道德底线。

其次，由于爬虫是高效的自动化脚本，其向服务器发出请求的频率可能远超普通人类正常使用服务所能产生的请求频率。在这种高频请求下，服务器可能会面临一些攻击：

- DoS (Denial of Service) attack：拒绝服务攻击，通过构造大量的（尤其是繁重的，也就是 CC 攻击）请求使得服务器“忙不过来”，让服务器无法正常服务。
- DDoS (Distributed Denial of Service) attack：分布式拒绝服务攻击，在 DoS 攻击的基础上改为多个客户端（~~肉鸡~~ 用户）同时对目标网站发起大量请求。

??? example "简单的例子"

    尽管谈论攻击还比较遥远，但我们不妨考虑以下日常生活场景：

    - 当你在高考成绩查询网站上准时查询时。
    - 双十一午夜十二点，当你在某网购平台上下单时。
    - 周四下午一点，当你在“新清华学堂”公众号上抢 40 元前排学生票时。

如果服务器处理请求的能力较低，短时间内的大量请求可能导致服务器瘫痪，对服务提供者造成经济损失。故意攻击服务器的行为也是违反相关法律法规的，我们要时刻避免这种现象的发生。

### Requests Headers（请求标头）

上面简单介绍了什么不可为，接下来介绍一下我们应怎样知道什么是可为的。但在这之前，我们要先了解一下 Requests Headers 相关内容。

我们已经知道爬虫是向服务器发送特定网络请求并接收响应的自动化高效脚本。而通常来说，一次网络请求的主要内容包括：

- 你是谁：User-Agent 与 Cookie 。
- 你从哪里来：IP （不包含在 Requests Headers 中，而是包含在 TCP/IP 协议栈中的 IP 数据包中，这里我们略过）。
- 你来这里干什么：Method 与 Parameter （如果是 POST 请求的话还会有更多内容，但是作为爬虫的话可以略微简化）。

User-Agent 用于标识发起请求的客户端应用程序或设备的信息。它提供了关于客户端的一些基本信息，例如浏览器类型、操作系统、版本号等，以便服务器能够根据这些信息提供适当的响应。以下是几个例子：

- 我的这台 Windows 机器上 Edge 浏览器的 User-Agent： `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36` 。
- Python requests 库默认的 User-Agent： `python-requests/{version}`，其中 `version` 为 Python 的版本号。 
- Postman 默认的 User-Agent： `PostmanRuntime/{version}`，其中 `version` 为 Postman 的版本号。

??? question "什么是 Postman ？"

    以下内容来自 ChatGPT：

    Postman是一种用于测试和开发API的工具。它提供了一个用户友好的界面，使开发人员能够轻松地创建、发送和调试HTTP请求，并查看服务器的响应。Postman支持多种HTTP请求方法，如GET、POST、PUT、DELETE等，还可以设置请求头、请求体和参数。开发人员可以使用Postman来测试API的功能、验证其正确性，并进行性能测试。

    除了发送请求和接收响应外，Postman还提供了其他功能，如自动化测试、断言验证、环境变量管理、协作和版本控制等。它还支持集成到开发流程中，可以与版本控制系统和持续集成工具进行集成，以便团队协作和自动化测试。

    Postman还提供了一个云平台，允许团队成员共享API文档、测试集合和环境配置，以便更好地协作和管理API开发过程。

    总而言之，Postman是一个功能强大的API开发和测试工具，可以帮助开发人员更高效地测试、调试和管理API。

Cookie 是一种在客户端（通常是 Web 浏览器）和服务器之间传递的小型数据文件。它由服务器在 HTTP 响应中设置，并在随后的请求中由客户端包含在 HTTP 标头中发送回服务器。在 Cookie 中，往往记录了诸如用户登录状态、个性化设置、会话记录等内容。以众多网站的自动登录为例，服务器接收客户端发出的 Cookie ，然后与服务器上存储的用户信息进行查找校验，选取符合的用户信息免密登录。

Method 是 HTTP 请求中的方法，Parameter 是这一过程传入的参数。我们可以调用的 Method 诸如 GET, POST, DELETE 等。

### 何可为

通过 Requests Headers ，服务器可以大概了解发送请求的用户的基本信息。服务器可以编写 `robots.txt` 文件来规定不同的爬虫对于网页上各文件的访问权限。但请注意，这份 `robots.txt` 文件只是一份“君子协议”，并没有严格的限制，网站会采取其他手段防止恶意攻击。

通过在网站域名后加 `/robots.txt`，我们可以访问该网站的 `robots.txt` 文件。但请注意，子域名的 `robots.txt` 未必与主域名的相符。

我们建议您阅读 [Google for developers 对于 robots.txt 文件的说明](https://developers.google.com/search/docs/crawling-indexing/robots/intro?hl=zh-cn) 来进行简单的了解。这里我们仅讲解几个例子。

- [百度](https://www.baidu.com)

```
User-agent: Baiduspider
Disallow: /baidu
Disallow: /s?
Disallow: /ulink?
Disallow: /link?
Disallow: /home/news/data/
Disallow: /bh

User-agent: Googlebot
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?
Disallow: /home/news/data/
Disallow: /bh

# 此处略去部分内容

User-agent: *
Disallow: /
```

解读：一律禁止除了我指定的爬虫外的所有爬虫。而对于我指定的爬虫，有些文件你也是不能访问的。

- [淘宝](https://www.taobao.com) 

```
User-agent: Baiduspider
Disallow: /

User-agent: baiduspider
Disallow: /
```

解读：一律禁止百度爬虫对任何文件的访问。

- [天猫](https://www.tmall.com) 

```
User-agent: * 
Disallow: /
```

解读：一律禁止所有爬虫对任何文件的访问。

- [虎扑体育](https://www.hupu.com/)

```
User-agent: *
Allow: /

Sitemap: https://bbs.hupu.com/sitemap_index.xml
Sitemap: https://bbs.hupu.com/sitemap/sitemap_boards.xml
Sitemap: https://voice.hupu.com/sitemap_index.xml
Sitemap: https://nba.hupu.com/players/index.xml
```

解读：欢迎任何爬虫来爬我。此外，我还提供了 Sitemap（站点地图）来为爬取提供便利。

??? "什么是 Sitemap？"

    请参考 [Google for developers 对于 Sitemap 的解释](https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview?hl=zh-cn)

- [虎扑社区](https://bbs.hupu.com/)

```
User-agent: *
Request-rate: 1/1
Disallow: /api/
Disallow: /ajax/
Disallow: /profile.php?*
Disallow: /hack/
Disallow: /template/
Disallow: /attachment/
Disallow: /gearfeedback/
Disallow: /*_*.html$

Sitemap: https://bbs.hupu.com/pc-sitemap.xml
```

解读：注意这里就发生了我们之前提到过的子域名与主域名 `robots.txt` 可能不相同的情况。在这里，虎扑社区限制对于所有爬虫，每秒访问次数不可超过一次，且禁止了对于部分文件的访问。

## 环境配置

本文档中主要使用了以下 Python 第三方库：

- requests：提供了一些 Python 中易于调用的 HTTP 请求接口。
- beautifsoup4：提供了一些解析和遍历 HTML/XML 结构的接口，便于提取所需数据。
- selenium与webdriver-manager：模拟用户在浏览器中的操作。

## 构建爬虫

构建爬虫的关键在于分析服务器请求，分析我们期望获得的数据在哪个或哪些请求中，进而构建“API Chains”。

以下是三种典型的网络请求的响应以及我们解析他们的手段：

- JSON：利用 Python 内部库 json 对文件直接解析。
- HTML：使用 BeautifulSoup4 进行解析。
- 二进制文件：直接保存。

下面我们主要学习有关 HTML 的部分。

### requests

这一部分我们以爬取 [知乎热榜](https://www.zhihu.com/hot) 内容为例来进行分析。

**发送请求**

