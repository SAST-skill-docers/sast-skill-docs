## Python 爬虫

### 前置知识

+ 懂得 Python 基本语法，知道如何使用 Python 第三方库
+ 知道 HTML 的基本结构
+ 会使用 Chrome 等现代浏览器的开发者工具来查看网页源代码和网络请求信息

### 环境配置

需要用到以下一些库：

+ IPython：理论上没有也行，但是使用 IPython 会提高大家测试的效率
+ requests：我装 Python 的时候好像自带了但是这似乎不是标准库
+ BeautifulSoup4：解析 HTML 的库，注意**不要**尝试用正则表达式来处理 HTML，虽然小学期可能会这样做，但是正则表达式并不适于解决 HTML 这种格式
+ fake_useragent：生成 User-Agent 的库

**关于 fake_useragent**

这个库似乎会尝试自动获取某些在线资源，但是好像需要代理，如果不能征程运行，请下载 [fake_useragent.json](https://cloud.tsinghua.edu.cn/f/289c2fd88d39469e8395/) 放在一个你能找到的地方，最好放在我们要使用的项目文件的目录下

在使用时应当添加参数指定资源地址如下：

```python
ua = UserAgent(path='./fake_useragent.json')
```

### 网络请求

**终极哲学三大问（服务器视角下的网络请求）**

爬虫为了（自动化地、常常还需要是高速地）获取服务器资源，首先应当了解向服务器发送请求时发生了什么。通常来说一次网络请求的主要内容包括：

+ 你是谁：User-Agent | Cookies
+ 你从哪里来：IP
+ 你来这里干什么：Method 与 Parameter （如果是 POST 请求的话还会有更多内容，但是作为爬虫的话可以略微简化）

在这一过程中服务器可能面临一些攻击（爬虫可能无意间导致这样的问题）：

+ DoS (Denial of Service) attack：拒绝服务攻击，通过构造大量的（尤其是繁重的，也就是 CC 攻击）请求使得服务器“忙不过来”，让服务器无法正常服务。
+ DDoS (Distributed Denial of Service) attack：分布式拒绝服务攻击，在 DoS 攻击的基础上改为多个客户端（~~肉鸡~~ 用户）同时对目标网站发起大量请求。

尽管谈论攻击有点遥远，但是不妨考虑以下两个情景：

1. 当你准时到达查询高考成绩时
2. 当你双十一午夜十二点下单时

所以写爬虫时应该全力避免这种情况！

**网络抓包基本功**

!!! info "一线主播的名言"

    浏览网页的本质是基于 HTTP 协议的一次或者多次网络请求，这些请求用户是可以直接查看的，比如按下 F12 再看看“网络”。

网络抓包通常有如下用途：

1. 查看真正的数据来源（常为 XHR）
2. 查看下载视频等数据
3. 模拟请求完成登录
4. ~~测试网站安全性~~

其实对于写爬虫来说，主要用途只有一个，就是找到我们需要的数据的真正来源（当然如果使用 selenium 等方法加载网页内容可以不用分析数据来源，但是这样显然消耗更多资源且慢），稍后我们将会看到这一点。

### 爬虫初步

**何为爬 & 为何爬**

这个想必大家都是懂的，写爬虫归根结底还是为了替代人工进行重复大量的操作，如果非常不熟悉爬虫这种程序可以去看 rls 的课。

**孰可爬**

这部分是我们最重要的背景知识，爬网站之前请务必关注 `robots.txt`（即域名后接 `/robots.txt`，要注意子域名的要求未必和主域名一样，切不可看到主域名可以爬就随便搞），这是网站与爬虫之间的“君子协议”，此处规定了什么可爬什么不可爬，下面是几个例子。

- 百度

```text
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

# 中间略去若干内容

User-agent: *
Disallow: /
```

解读：除了我钦点的爬虫之外，其他拒不接待。

- 淘宝 / 天猫

```text
User-agent: *
Disallow: /
```

解读：莫挨老子。

- 虎扑

注意这里就出现了我们之前提到过的问题，https://www.hupu.com/robots.txt 如下：

```text
User-agent: *
Allow: /

Sitemap: https://bbs.hupu.com/sitemap_index.xml
Sitemap: https://bbs.hupu.com/sitemap/sitemap_boards.xml
Sitemap: https://voice.hupu.com/sitemap_index.xml
Sitemap: https://nba.hupu.com/players/index.xml
```

解读：任何人都可以任意爬，还提供了站点地图供参考。

但是 https://bbs.hupu.com/robots.txt 则是这样的：

```text
User-agent: *
Request-rate: 50/1
Disallow: /api/
Disallow: /ajax/
Disallow: /profile.php?*
Disallow: /hack/
Disallow: /template/
Disallow: /attachment/
Disallow: /gearfeedback/
Disallow: /*_*.html$

Sitemap: https://bbs.hupu.com/sitemap_index.xml
Sitemap: https://bbs.hupu.com/sitemap/sitemap_boards.xml
```

解读：法无禁止皆可为，但是速度不超过 50 次每秒（注意不同的爬虫其实对于一次爬取的理解是不同的，但是这是君子协议，所以对于我们自己写的爬虫也没有必要那么纠结，如果要仔细考虑的话，可以研究一下不同搜索引擎的爬虫对于这一问题的理解，可以参考 [知乎的 robots.txt](https://www.zhihu.com/robots.txt) 以及 [相关提问](https://www.zhihu.com/question/264161961)）。

虽然虎扑社区比主站多了一些限制，但是我们看到这仍然是限制最少的（虽然限制 `*_*.html$` 但是新闻页并不是这样，而且 50 次每秒其实也不少），加上 ~~众所周知的原因~~ 虎扑前两天很出圈（至少在我写文档的时候是这样），所以我们将主要爬取虎扑的体育新闻作为演示。

**如何爬**

理想中的高性能爬虫架构：

![](https://cloud.tsinghua.edu.cn/f/e5dd665d16bb4bf9b3fb/?dl=1)

本文用的入门版爬虫架构：

![](https://cloud.tsinghua.edu.cn/f/6fe764ccffab48998bca/?dl=1)

### 爬虫实战

**自学内容**

环境配置中写了最重要的工具，下面还有些可能会用到的自学内容：

+ js2py：在 Python 中执行 JavaScript 脚本
+ pyppeteer：通过 headless 的方法直接渲染网页
+ selenium：控制浏览器进行自动化测试的工具
+ requests.Session：保存会话，适用于需要 Cookie 的场景
+ requests 添加代理 IP
+ scrapy：真正的高性能爬虫框架

**爬取演示**

以爬取 [虎扑某板块新闻列表](https://bbs.hupu.com/502-1) 为例：

- 引入必要的库

```python
from requests import get, post
from bs4 import BeautifulSoup
from fake_useragent import UserAgent
from urllib.request import urljoin
from json import dump
from time import sleep # 如果网站要求以很低的频率爬，那么大概率会需要 sleep
```

- Cookie?

如果你点开了前面那个链接（而且你最近没有看过虎扑的话），你大概率会发现报了个错，不要怀疑，我没有写错 URL，造成这样问题的原因大概是虎扑在切换新旧版面，直接点进去会莫名被转入旧版的页面，而旧版页面的 URL 大概不是这样写的。

那么这就要提到之前的一个问题，也就是“你是谁”的问题，虎扑会根据 Cookie 来判断你是否已经进入了新版页面，而这个 Cookie 显然是在首页被设置的，因此直接访问这一板块是行不通的。

从浏览器中获取 Cookie 信息如下（因为内容太长所以我们直接保存在 Python 变量中）：

```python
Cookie = 'smidV2=20210720104859f6c99288e1bccb891e9ca9ce9d7bb6fe0055f24f4e677eec0; csrfToken=iGeSrk669wqQBcY5fKk2Diue; sensorsdata2015jssdkcross=%7B%22distinct_id%22%3A%2217ac1d102691c24-05465c49cc1a3d8-3e62684b-1296000-17ac1d1026a2685%22%2C%22%24device_id%22%3A%2217ac1d102691c24-05465c49cc1a3d8-3e62684b-1296000-17ac1d1026a2685%22%2C%22props%22%3A%7B%22%24latest_referrer%22%3A%22%22%2C%22%24latest_traffic_source_type%22%3A%22%E7%9B%B4%E6%8E%A5%E6%B5%81%E9%87%8F%22%2C%22%24latest_search_keyword%22%3A%22%E6%9C%AA%E5%8F%96%E5%88%B0%E5%80%BC_%E7%9B%B4%E6%8E%A5%E6%89%93%E5%BC%80%22%7D%7D; acw_tc=2f624a6816268527794733860e071fa5c73b1f702b456ad494e04ecfb5dc53; bbs_2020=1'
```

- 获取并解析新闻列表页

我们注意到，虎扑的新闻列表页 URL 大致可以表示为：

```text
https://bbs.hupu.com/502-{PAGE}
```

注意到其中每一项的格式大致如下：

```html
<a href="/44314069.html" target="_blank" class="p-title">[流言板]字母哥：要相信！我曾经上顿不接下顿，如今我是冠军</a>
```

那么假如我们获取前 10 页的新闻详情链接：

```python
url = 'https://bbs.hupu.com/'
news_list = []

for i in range(1, 11):
    response = get(f'https://bbs.hupu.com/502-{i}', headers={'Cookie': Cookie})
    soup = BeautifulSoup(response.content.decode('utf-8'), 'lxml')
    news = soup.find_all('a', class_='p-title')
    for n in news:
        news_list.append(urljoin(url, n.get('href')))
    sleep(0.02) # 按照 robots.txt 要求休息 1/50 秒
```

- 获取并解析新闻详情

更好的做法是这里写一个类，但是一个自定义类写入文件又涉及到许多问题，所以我们简单的用字典表示，类似的，我们获取并解析网页：

```python
news_content = []

for news_url in news_list:
    response = get(news_url, headers={'Cookie': Cookie})
    soup = BeautifulSoup(response.content.decode('utf-8'), 'lxml')
    title = soup.find('h1', class_='name').text.strip()
    content = soup.find('div', class_='thread-content-detail').text.strip().replace('\r', '\n')
    news_content.append({
        'url': news_url,
        'title': title,
        'content': content
    })
    sleep(0.02)
```

接下来我们将获取的内容保存为 JSON：

```python
dump(news_content, open('./hupu.json', 'w'), ensure_ascii=False)
```

**关于反爬**

接下来我们演示一个糟糕的反爬示例，[北京卫健委](http://wjw.beijing.gov.cn/) 网站。

如果我们直接执行：

```python
response = get('http://wjw.beijing.gov.cn/')
```

我们会发现 `response.text` 很不对劲，而且其中比较明显能看到的是（当然我经过了格式化）：

```html
<div id='notice-jiasule'>
  <p>当前网址：<%- url %></p>
  <p>客户端特征：<%- user_agent %></p>
  <p>拦截时间：<%- now %>&nbsp;&nbsp;本次事件ID&nbsp;<%- rule_id %></p>
</div>
```

还有下面的：

```javascript
var content = _.template(document.getElementById('content_tpl').innerHTML)({
    error_403: '' || '当前访问疑似黑客攻击，已被网站管理员设置为拦截',
    url: document.URL.replace(/\</g,"%3C").replace(/\>/g,"%3E"),
    user_agent: navigator.userAgent,
    now: new Date(new Date() - -8 * 3600000).toISOString().substr(0, 19).replace('T', ' '),
    rule_id: parseInt('[80001]'.replace(/\[|\]/g, '')) || '',
    from: encodeURIComponent(document.referrer.substr(0, 1024)),
    client_ip: '59.66.16.88',
    ref: encodeURIComponent(document.URL.substr(0, 1024))
});
```

不难发现这个网站所托管的“创宇云防御”屏蔽了我们的访问，而引起这一问题的原因，我们可以查看：

```python
>>> response.request.headers
{'User-Agent': 'python-requests/2.25.1', 'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive'}
```

其中显然有一条 `'User-Agent': 'python-requests/2.25.1'`，不打自招的爬虫行为，被屏蔽也并不奇怪（题外话，这个网站并没有设置 `robots.txt`，因此双方都很难说谁对谁错）。好在现在这个网站不像去年一样会封 IP 地址，所以我们可以先演示错误示例。

我们当然可以模仿前面的做法，从浏览器中抄一个 User-Agent 过来，但是如果我们快速爬很多东西，最好还是每次换一个新的 User-Agent 减少被当作同一个用户的概率，因此我们使用如下方法：

```python
ua = UserAgent(path='./fake_useragent.json')
response = get('http://wjw.beijing.gov.cn/', headers={'User-Agent': ua.random})
# 没错，ua.random 虽然是成员变量，但是每次使用的时候会改变自己，而不是用户调用一个类似于 ua.random() 的函数才会改变
```

除此之外可能还会需要使用代理 IP，如何获取代理地址请自学，使用方法如下：

```python
proxies = {
    "http": "http://10.10.1.10:3128",
    "https": "http://10.10.1.10:1080",
}
requests.get("http://example.org", proxies=proxies)
```

使用这两种方法可以避免很多反爬的系统（当然如果检查 Cookie 大概会很糟糕，可能需要使用 Session 来解决，像上述直接设置 Cookie 实际上并不很通用，在此不做很具体的说明），但是还有更多的情况可能真实的数据并不能轻易获取，这个时候就要善用浏览器的开发者工具审查所有的网络请求。

### 资源链接

+ 2021 科协暑培 <https://www.xuetangx.com/course/THUSAST08091234567890/8571842?channel=i.area.manual_search>