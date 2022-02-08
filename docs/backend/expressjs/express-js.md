# ExpressJS

Express 是一个简洁而灵活的 Node.js Web 应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。

使用 Express 可以快速地搭建一个完整功能的网站，但是，我们一般更倾向于使用 Express 来快速搭建**网站后端**。

!!! note "服务器端渲染与客户端渲染"

    注意，这里我们将网站分为前端和后端，事实上与我们在《程序设计训练》课程中对于使用 Django 来搭建网站的方式不同。Django 的页面渲染是**服务器端渲染**（Server-side Rendering），也就是说，在收到用户发来的请求后，服务器端按照设计的逻辑读取相应的页面模板，在完成页面渲染（也就是在页面模板的对应处填充对应字段）后，将渲染后的 HTML 页面直接发给用户。
    
    而这里我们将网站分为“前端”和“后端”，这种设计模式和上述服务器端渲染有本质的区别，我们一般称为**客户端渲染**（Client-side Rendering）。其运作模式是，网站提供服务器直接将未渲染的 HTML 模板（我们称为“前端代码”）发送给用户，而这模板中包含了类似于 `fetch` 的函数，可以在浏览器将其渲染时向我们的“后端”发送请求。“后端”在收到请求后，一般以 Json 格式返回所请求的数据，前端再通过 Javascript 脚本将收到的数据渲染在页面上。
    
    在《软件工程》课程中我们推荐使用后者这种设计模式，即进行前后端分离。虽然我们也可以继续利用 Django 来作为后端，但是我们这里给大家提供一种业界也很常用的选项 —— Express。其优点在于，相比于 Django 而言，其编写起来更加方便，也更加快捷。<s>而且，后端更加靠近 `npm` 这个轮子工厂，对于我们进行大调库也更加的方便。当然，因为 Javascript 令人**的设计，也会收获更加酸爽的 Debug 体验。</s>

本文我们侧重介绍如何配置一个 Express 项目，并完成一个最基本的与后端数据库通信的增删查改功能.

<!--more-->

## 前置知识

+ JavaScript 语言
+ Node.js 相关知识，包括 npm / yarn 的使用
+ RESTful API 的有关知识（事实上后文也有介绍） https://www.runoob.com/nodejs/nodejs-restful-api.html
+ （至少一种）数据库的使用操作
  + 可以是直接使用 SQL 语句操作
  + 可以是使用 ORM 来操作

## 初始化项目

搜索结果可以找到的大多数编写教程均是基于 `CommonJS` 规范（以 `require` 和 `define` 为特点），而 `Node.js` 则推出了基于语言层面支持的 `ES6 Module` 规范（以 `import` 和 `export` 为特点）。后者必将成为今后较为常用的编写规范。如果想了解更多关于 Javascript 模块化开发的有关知识，可以参考本文初稿作者的[这篇博客](https://c7w.tech/javascript-module-dev/)。

同样，这里我们采用更为广泛使用的 `yarn` 来进行包管理。其安装在 Node.js 简介及 npm 的介绍中已给出，这里我们不再赘述。

首先我们先新建一个项目。（这里采用 Windows 的 CMD 进行操作，Linux 的操作类似，下同）

```bash
D:\Coding>cd D:\Coding\MyDashBoardBackend

D:\Coding\MyDashBoardBackend>yarn init
yarn init v1.22.17
question name (MyDashBoardBackend):
question version (1.0.0):
question description: A sample backend project.
question entry point (index.js):
question repository url:
question author:
question license (MIT):
question private:
success Saved package.json
Done in 20.93s.
```

然后我们安装 express 模块：

```bash
yarn add express
```

为了使用 `ES6 Module` 规范，我们配置 `package.json`，在其中加入 `"type": "module"` 字段：

```json
{
  "name": "MyDashBoardBackend",
  "version": "1.0.0",
  "type": "module",
  "description": "A sample backend project.",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "express": "^4.17.2"
  }
}
```

然后，我们在根目录新建 `index.js`，写入如下内容：

```javascript
import express from "express";
const app = express()

app.get('/', (req, res) => {
    res.send('Hello, baka c7w!')
})

app.listen(3000);
```

然后我们就可以运行 `node index.js`，然后前往浏览器访问 `http://localhost:3000/`，便可以看到我们输出的欢迎信息。

若是感觉如此逼格还不够高，不要紧，我们可以继续进行一些配置：

我们可以继续修改 `package.json`，向其中加入使用 `yarn start` 开启服务端的配置：

```json
{
  "name": "MyDashBoardBackend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js"
  },
  "description": "A sample backend project.",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "express": "^4.17.2"
  }
}
```

之后我们就可以使用 `yarn start` 来开启服务端了。

## 路由 Routing

没错，相信你已经猜到了，我们后端网站的路由便是主要通过 `app.get` 和 `app.post` 两个方法来定义。事实上，如果你对 HTTP 请求方法了解的更多些的话，我们可以使用以下方法：

+ `app.get()`
+ `app.post()`
+ `app.put()`
+ `app.delete()`

事实上还有更多，这里我们不再一一列出，如欲了解可以借助[官方的 Docs](https://expressjs.com/en/4x/api.html#app.METHOD)。

我们这里给出一些示例写法：

```javascript
import express from "express";
const app = express()

app.get('/', (req, res) => {
    res.send('Hello, baka c7w!');
})

app.get('/item', (req, res) => {
    res.send('GET method to /item');
})

app.post('/item', (req, res) => {
    res.send('POST method to /item');
})

// Regular Expression!
app.get('/item/*', (req, res) => {
    console.debug(req.path); // `/item/1`
    console.debug(req.params); // undefined
    res.send("Item/*")
})


// Variables!
app.get('/item2/:item/', (req, res) => {
    console.debug(req.path); // `/item2/1`
    console.debug(req.params); // { item: '1' }
    res.send("Item/*")
})

// Even ReExp + Variables!
app.get("/item3/:item(\\d+)", (req, res) => {
    console.debug(req.path); // `/item3/1`
    console.debug(req.params); // { item: '1' }
    res.send("Item/*")
})

app.listen(3000);
```

### Route Handler

我们可以尝试考虑以下情景：我们要根据用户身份（这里做简要简化，假设用户身份通过 Routing 传入），分别展示不同的页面。事实上这种情景很常见，比如一个网站的管理员登入博客看到的应该是管理页面，而登录作者看到的应该是写作页面，普通用户看到的就是浏览页面。但是这些页面中又会有一些耦合的元素。于是，我们能不能采用 OOP 课程中策略模式的思想来解决这个问题呢？

```javascript
const logger = (req, res, next) => { // Logger 部件，记录用
    console.debug(req.params.user + ' tried to visit...');
    next();
}

const main = (req, res, next) => {
    if(req.params.user == "c7w") {
        res.result = "Welcome!<br />";
        next();
    } else {
        res.send("Permission Denied")
    }
}

const footer = (req, res) => {
    res.result += ('<br /><br />' + new Date());
    res.send(res.result)
}


app.get("/user/:user", [logger, main, footer])
```

事实上我们可以利用这种 Handler “数组”来定义处理一个请求的先后顺序。如果在前面的某个 Handler 调用了 `next()`，那么便会执行下一个 `Handler`。

## req 与 res

这里我们介绍下 `req` 和 `res` 两个参数的主要属性和方法。

### `req`

| Property    | Description                        |
| ----------- | ---------------------------------- |
| req.query   | 在请求后以 `?` 和 `&` 连接的键值对 |
| req.body    | 请求体的内容                       |
| req.cookies | Cookies 中的内容                   |

更多详见：https://expressjs.com/en/4x/api.html#req

### `res`

| Method                                                       | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [res.download()](https://expressjs.com/en/4x/api.html#res.download) | Prompt a file to be downloaded.                              |
| [res.end()](https://expressjs.com/en/4x/api.html#res.end)    | End the response process.                                    |
| [res.json()](https://expressjs.com/en/4x/api.html#res.json)  | Send a JSON response.                                        |
| [res.redirect()](https://expressjs.com/en/4x/api.html#res.redirect) | Redirect a request.                                          |
| [res.render()](https://expressjs.com/en/4x/api.html#res.render) | Render a view template.                                      |
| [res.send()](https://expressjs.com/en/4x/api.html#res.send)  | Send a response of various types.                            |
| [res.sendFile()](https://expressjs.com/en/4x/api.html#res.sendFile) | Send a file as an octet stream.                              |
| [res.sendStatus()](https://expressjs.com/en/4x/api.html#res.sendStatus) | Set the response status code and send its string representation as the response body. |

更多详见：https://expressjs.com/en/4x/api.html#res

## Middleware

Middleware（中间件函数）的概念其实很好理解。就像是我们上面举的那个例子，`logger` 和 `main` 就可以理解成是 Middleware functions。中间件函数是带有了 `req, res, next` 为签名的函数，在 Express 处理请求的时候，事实上会将某个地址对应的路由的所有中间件组织成一个类似链表的结构，随着 `next()` 的调用在中间件间顺序执行。而且，中间件都是有修改 `req` 和 `res` 的所有属性的能力的。

一个需要注意的点是，如果中间件函数没有使用类似于 `res.send()` 的方法将一个请求返回的话，必须要调用 `next()` 函数，不然即使这个中间件函数执行到末尾，也不会自动跳转。这就会导致请求“假死”的现象。

### 全局 Middleware

使用 `app.use([path], <middleware function>)` 我们可以添加供全局使用的 Middleware 函数（Application-level middleware），示例如下：

```javascript
import express from "express";
const app = express()

const logger = (req, res, next) => { // Logger 部件，记录用
    console.debug(`[${new Date()}] ${req.method} ${req.originalUrl}`);
    next();
}



app.get('/', (req, res) => {
    res.send('Hello, baka c7w!');
})

app.get('/item', (req, res) => {
    res.send('GET method to /item');
})

app.post('/item', (req, res) => {
    res.send('POST method to /item');
})

app.get('/item/*', (req, res) => {
    console.debug(req.path); // `/item/1`
    console.debug(req.params); // undefined
    res.send("Item/*")
})

app.get('/item2/:item/', (req, res) => {
    console.debug(req.path); // `/item2/1`
    console.debug(req.params); // { item: '1' }
    res.send("Item/*")
})

app.get("/item3/:item(\\d+)", (req, res) => {
    console.debug(req.path); // `/item3/1`
    console.debug(req.params); // { item: '1' }
    res.send("Item/*")
})

app.use(logger)

app.listen(3000);
```

```bash
[Tue Feb 08 2022 22:16:18 GMT+0800 (China Standard Time)] GET /user/123
[Tue Feb 08 2022 22:16:20 GMT+0800 (China Standard Time)] GET /user/123
[Tue Feb 08 2022 22:16:22 GMT+0800 (China Standard Time)] GET /user/123/123
```

### 异常处理 Middleware

我们可以定义处理异常的中间件函数，方法如下：

```javascript
app.use(function (err, req, res, next) {
  console.error(err.stack)
  res.status(500).send('Sorry, but fatal error occurred meanwhile.')
})
```

### 内置 Middleware

Express 内置了以下中间件：

- [express.static](https://expressjs.com/en/4x/api.html#express.static) 用于提供静态文件，比如图片，文件等等
- [express.json](https://expressjs.com/en/4x/api.html#express.json) 将 req.body 处理为 json (Available with Express 4.16.0+)
- [express.urlencoded](https://expressjs.com/en/4x/api.html#express.urlencoded)

对于 POST 请求，我们推荐使用后两个中间件。

## CRUD 的实现

!!! note "RESTful API"

    首先我们先简单地介绍一下 RESTful API 是什么。
    
    RESTful API 是目前比较成熟的一套互联网应用程序的 API 设计理论。可以参考 https://www.ruanyifeng.com/blog/2014/05/restful_api.html。
    
    TL; DR: 按照一定约定俗成的设计，对于我们所关心的涉及的资源，进行增删查改的操作。RESTful API 更多的是关注在“设计”这一层面。

作为今天的教程，我们只要理解，我们设计出一个后端，能让它和我们的数据库连接，完成增删查改（Create, Read, Update, Delete）就可以了。

作为轮子工厂，我们经过简单的搜索就能找到 JavaScript 与 mysql 连接的工具，其使用教程可以[见此](https://www.runoob.com/nodejs/nodejs-mysql.html)。

!!! note "ORM"

    直接撰写 SQL 语句固然在应用上是高效的，但是切换编程语言或者找一些自动生成 SQL 查询语句的工具也会降低我们的编程效率。在我们**并不是很在意查询效率的前提下**，我们不禁回忆起 Django 中用类撰写 Model 的模式，当时可是十分节省我们的力气。
    
    事实上，这也是一种十分常见的设计模式，叫做 ORM（Object–relational mapping）。之前我们可以将关系型数据库中的一个表看做是一张 Excel 表，而表头规定了这个表的每行记录所应该有的属性。而如果我们把这张表中的表头，视为是一个类在规定它应该具有的属性的话，那么这张表的每行记录，事实上就是这个类所实例化出来的对象。这正是 Django 的 db.models 所采用的设计模式。
    
    再一次，作为轮子工厂，只需要简单的搜索，我们便能找到实现与数据库之间 ORM 设计的库，这里我们以 [Sequelize](https://sequelize.org/v7/) 为例。这里我们采用便携的 `sqlite3` 作为我们的数据库。

实现后的简单项目归档于：https://github.com/c7w/MyDashboardBackend

## 后续拓展

+ 了解更多 ExpressJS 的中间件
+ 了解 ExpressJS 的模板渲染机制
+ 换用其他数据库尝试，包括 fs（本地存储），MySQL，以及非关系型数据库 PostgreSQL

可以应用 ExpressJS 的课程：

+ 《软件工程》

## 资源链接

+ ExpressJS 官网 https://expressjs.com/
+ ExpressJS 中文网 https://expressjs.com/zh-cn/
+ 菜鸟教程 https://www.runoob.com/nodejs/nodejs-express-framework.html
+ MDN 教程 https://developer.mozilla.org/zh-CN/docs/Learn/Server-side/Express_Nodejs/skeleton_website

