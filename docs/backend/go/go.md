# Go

Go 语言是一门由Google开发的**编译型**语言。其语法与 C 较为接近，OOP 水平介于 C 和 C++ 之间，特点是能比较轻易地进行并行编程。Go 被广泛地用于**后端开发**中，相比 Python, Node.js 等解释型语言往往拥有更高的运行效率。

本文我们侧重介绍如何用 Go 搭建一个简易的 Web 服务器，并且完成与后端数据库通信的增删查改功能，对并发安全的内容亦会稍有介绍。

Credit: 基于 @pyz 的2022暑培讲稿，少量修改而成。

<!--more-->

## 前置知识

+ 一种 C-family 语言
+ 数据库的基本操作
+ 对前后端分离架构的初步了解



## 环境配置

### 1. Go 语言编译器安装

推荐访问 Go 语言官方网站的[下载页](https://golang.google.cn/doc/install)。在 Go install 的选项框里选择你的操作系统，下载打包好的安装文件并安装。不推荐install from source，较为繁琐。

!!! note "安装提示"

    如果本地已经安装了 Go 编译器，请检查大版本是否大于等于 `1.18` 。本文章内容全部基于 `1.18.3` 版本的 Go。

    Linux 用户不要用 yum, apt 等方式安装 golang ，可能由于国内换源的原因导致所安装的版本很低。

    Windows用户不要安装在需要管理员权限的文件夹下（这会在后续带来不必要的麻烦）。建议 D盘或 E盘开一个 D:\Golang 之类的文件夹用于安装  golang 以及后续下载的第三方 package 。

确认自己成功安装的方式是在命令行运行 `go version`, 查看是否有提示版本信息。 


### 2. IDE 选择

推荐使用 jetbrains 公司的 Goland 作为IDE，对于大学生而言，可以获取免费的学生版。也可使用 VSCode + go 语言插件。

??? note "验证学生身份"

    对于清华大学的学生，在使用校园邮箱验证 jetbrains 学生身份时，请将邮箱后缀替换为`@mails.thu.edu.cn` .

Go 语言提供了官方的代码风格整理器 gofmt ，简单配置 Goland 可以完成自动格式化代码。



## Go基本语法

本节将会用几个示例程序来展示 golang 的语法, 并对其加以讲解。请认真阅读此处的代码， 并尝试在电脑上运行它们，这会让你对这门语言有最基本的了解和直观感受。

### 1. Hello, world!

```go
//声明一个名叫main的package 
package main

import (
    "fmt"
)

func main(){
    fmt.Println("Hello world")
}
```

在文件所在路径下使用命令 `go run <文件名>.go` ，即可运行程序。

示例代码中的文件是 main.go 这一段代码由三部分组成，它们之间的顺序是不可调换的，一般而言一段可运行的go程序都是由这三部分组成，下面讲解它们的意义。 

- package声明部分

    package 是 Go 语言管理代码的方式，我们一般把 package 成为“包”。package 和其它语言中的库或者模块(module)的地位类似。一般而言，一个包由一个或多个 .go 源文件组成。特别地，我们不以 *_test.go 的形式命名源文件，所有这样结尾的文件会被视为测试文件。
    
    每一个 package 的名字描述了这个 package 的功能，一般而言和这个 package 所处的文件目录的最后一级名字相同。main package是特殊的package，用来定义一个可执行的程序，这个可执行程序的执行起点是 main package 里面的 main 函数。 
    
- import部分

    Go 以 package 管理代码，相应的，想使用标准库或者第三方库中提供的方法或对象，就需要使用 import 来导入它们。import 的语法有两种，它们都是可以通过编译的。一般而言，第二种写法是被推荐的，它在格式上显得较为整齐。

    ```go
    import "fmt" //单行import 
    import(
        "fmt"
        "os" 
        //一系列其他包
    )
    ```

    通过 import 导入包的顺序是无关紧要的，但是有几点仍是需要关注的:

    1. import 的优化: Go 语言不允许出现导入但未使用的包。

    2. import cycle 问题: Go 语言不允许两个 package 互相 import。这为写 Go 代码的人带来了一定的麻烦(不能像使用C一样定义很多头文件后随意调用了)，但这也给 Go 语言带来了一个优势——编译时间较短。
        
        解决import cycle的办法多种多样，但这不是本教程的重点，有兴趣的同学可以自行搜索各类方法。

    3. import 两个名字一样的包会发生什么: 我们注意到第三部分的 main 函数使用了 `fmt` 来调用 `fmt` 包内的方法，但是如果很不巧，我需要调用一个别人写的包，它的名字也叫 `fmt`，该如何做到?
    
        可能很多人第一眼觉得这个问题不是问题，毕竟没人会和自己过不去，用一个和标准库同样的名字，但事实上在标准库里这个问题已经发生了。标准库包含两个涉及随机的package，分别 是math和crypto包中的rand包。

        ```go
        package main
        import(
            "crypto/rand"
            "math/rand"
        )
        //...
        /*
        试图编译会有如下输出
        # command-line-arguments
        ./main.go:4:2: imported and not used: "crypto/rand" 未使用
        ./main.go:6:2: rand redeclared in this block
                ./main.go:4:2: other declaration of rand
        declaration
        ./main.go:6:2: imported and not used: "math/rand"
        未使用
        */
        ```

        解决方法其实也很简单，比如我们在python中可以使用 `import xxx as xxx` 的语法， Go 也为 import 提供了类似的机制:

        ```go
        import(
            crand "crypto/rand"
            mrand "math/rand"
        ) //crand和mrand可以理解为一个“别名”

        func foo(){ 
            //使用crand.xxx和mrand.xxx调用其中的方法即可
        }
        ```

- 主体代码部分

    我们可以看到，main函数里调用了fmt包的Println方法进行输出。
    
    !!! notes
    
        一个语言中的方法有很多，即使是熟悉标准库中的常用方法都需要大量的编程练习。作为一篇入门教程，掌握基本的语法即可，无需纠结自己为何记不住相应的方法。多上网冲浪，和他人沟通，就能逐渐掌握各类方法。

    在这里讲一下 go 的运行机制: 
    
    1. 并发

        可以简单地把并发理解为多个程序的同时执行。在代码层面，我们一般会认为并发是几段代码在同时执行，它们之间可以有交互。在后端的编程中，并发是必要的，因为每时每刻，访问网站的用户不能都局限到1个人。

        关于并发有两个相关的概念是进程和线程。打开windows任务管理器，我们就可以看到 许多“进程”，这些进程相互之间独立，一般而言不共享一块地址空间。一个进程包含多个线程，线程均可以访问进程内公共的数据。

    2. goroutine

        Go 语言中，每一个并发活动被称为 goroutine ，你可以把他理解为一个较为智能的线程，能自己完成调度，合理占用多核的CPU。当启动一个go程序的时候，会有一个 goroutine 调用 main 函数，这个 goroutine 被称为主 goroutine ，通过 `go <函数名>(参数)` 的方式新建 goroutine，执行调用的函数。

        需要注意的是，一旦主 goroutine 结束，所有的 goroutine 都会相应的结束。

### 2.常见语句的格式

下面这个示例展示了基本的变量声明和函数声明的格式。

```go
package main
import(
    "fmt"
    "log"
)
func main() { 
    //输入两个数，输出它们的和 
    /*
        两种声明变量的办法，var <变量名> <类型> 或是:=运算符 
    */
    var a int
    b := 0
    _, err := fmt.Scanf("%d %d",&a,&b)
    // _ 代表该函数有这一个返回值，但程序并不需要使用它，因此使用 _ 来忽略这一返回值。
    if err != nil {
        //if 后的条件没有小括号，要和大括号在同一行内
        log.Fatal("Bad Input")
        //当输入格式不匹配时，会输出Bad Input，程序exit status 1. 
    } else {
        fmt.Println("Good Input")
        fmt.Println("Result is",sum(a,b))
    }
}
//声明函数以func开始，格式为:func <函数名>(参数名) 返回参数 
func sum(a, b int) int {
    return a + b 
}
```

### 3.json数据处理

读取一个json文件，将其中的学生信息取出，打印。

```go
package main

import(
	"encoding/json"
	"fmt"
	"io/ioutil"
	"log"
	"os"
)

type (
	StudentInfo struct {
		Name string `json:"name"`
		Score int `json:"score"`
	}

	InfoList struct {
		Title string `json:"title"`
		Infos []StudentInfo `json:"info"`
	}
)

func main() {
	var data InfoList
	jsonFile, err := os.Open("info.json")
	if err != nil {
		log.Fatal(err)
	}
	bytedata, _ := ioutil.ReadAll(jsonFile)
	err = json.Unmarshal(bytedata, &data)
	if err != nil {
		log.Fatal(err)
	}
	for _, info := range data.Infos {
		fmt.Printf("student name: %s, score is %d\n", info.Name, info.Score)
	}
}
```

以下为配套的`info.json`

```json
{
    "title":"student-Info-2022",
    "info":[
        {
            "name":"a",
            "score":100
        },
        {
            "name":"b",
            "score":99
        },
        {
            "name":"c",
            "score":60
        },
        {
            "name":"d",
            "score":120
        },
        {
            "name":"e",
            "score":100
        },
        {
            "name":"f",
            "score":40
        }
    ]
}

```

## 搭建Web后端服务器

什么是 Web 后端？这个问题很复杂，我们可以把它简单地理解为一系列和前端交互，和数据交互的逻辑，它一般而言不会部署在用户一侧。你甚至可以说，除去网络传输这一步，剩下用户不可见的都可以称为后端。

对后端的要求是不断变化的，相应地，后端的架构、功能、设计思路是不断更新的。这也就是为什么很多大厂正在用go代码逐步取代django，重构之前的代码。

### 1. naive 版本

go语言的标准库提供了支持简单web服务器的方法，我们可以用它来搭建一个简单的web server，它向访问者返回一个“hello visitor”。

```go
package main

import (
    "net/http"
    "fmt"
    "log"
)

func main() {
    http.HandleFunc("/", html_handle)
    log.Fatal(http.ListenAndServe(":8080", nil))
}

func html_handle(w http.ResponseWriter, r *http.Request){
    fmt.Fprintf(w,"<h1>"+"hello visitor"+"</h1>")
}

```

上面代码的大致思路是这样的：对路径为"/"的http请求，使用函数html_handle做处理，我们一般把这种处理请求的函数称作handler function，它运行在8080端口上。直接用浏览器访问127.0.0.1:8080，就可以看见 hello visitor 字样。

!!! notes "对于WSL用户"

    如果是在wsl中运行程序，用浏览器访问8080端口并不能直接获取结果。需要在wsl中使用ifconfig，获得wsl的网卡地址，然后用浏览器访问地址和对应的端口号。

### 2. fancy一点的版本

Go 提供的标准库并不是那么的好用，我们更希望使用一些轻量级的 web 框架来辅助我们搭建网络的后端。这里我们使用的框架是 Gin 。这个框架是最为流行的，效率较高，使用范围很广，而且有十分完善的文档。

Gin 是一个第三方的 package ，如何使用它?我们需要下载第三方的源码，然后用 `go mod` 管理这个项目所需要的第三方包。我们将 Gin 包导入，尝试使用它。使用步骤如下：

1. 在源代码中添加

    ```go
    import(
        "github.com/gin-gonic/gin"
    )
    ```

2. 使用 `go mod init <module名字>` 来初始化项目，一般而言这个 module 名字就是你的项目根目录名字(即你的项目的名字)，这时你应该可以看到一个go.mod文件。

3. 使用 `go mod tidy` 来自动拉取缺少的 module ，移除多余的 module 。

    !!! notes "网络问题"

        如果出现拉取 connection timeout 的情况，这是因为访问 github 等国外网站出现了问题，可以使用更改 go 拉取代理的方式来解决。使用 `go env -w GOPROXY=https://goproxy.cn,direct` ，如无意外此时再运行 `go mod tidy` 将会出现一系列下载提示，并可以看到一个 go.sum 文件，同时 go.mod文件也被修改了。

下载的第三方包一般会在 $GOPATH/pkg/mod 文件夹下，而 go标准库 一般在 $GOROOT/src，这两个环境变量都可以通过 `go env` 命令获知，一般情况下不要改动。

以下为完整的服务器代码：

```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func main() {
	//完成一个接受POST方法的计算器，接收两个数，输出它们的和
	//不考虑溢出，输入的数字超出int的问题
	r := gin.Default()
	r.POST("/calculator", HandleCal)    //使用函数HandleCal处理 /calculator下的POST请求
	r.Run(":8080")                      //在8080端口运行
}

type AddPost struct {
	A int `json:"a"`
	B int `json:"b"`
}

func HandleCal(c *gin.Context) {
	var addpost AddPost
	if err := c.ShouldBindJSON(&addpost); err != nil {
		c.String(http.StatusBadRequest, "Invalid Form")
		return
	}
	c.JSON(http.StatusAccepted, gin.H{
		"answer": addpost.A + addpost.B,
	})
}
```

## gin + gorm 进阶

gin 在上文中已经提到，是一个 Web 后端框架。 gorm 是一个数据库连接及交互框架,一般用于连接关系型数据库。接下来将详细介绍两者的核心用法。

### 1. 为路径添加不同种的http方法

代码示例：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()
	r.GET("/some-get", HandleGet)
	r.POST("/some-post", HandlePost)
	r.Any("/any", func(g *gin.Context) {
		g.JSON(http.StatusOK, gin.H{
			"method": g.Request.Method,
		})
	})
	//也可以使用r.PATCH() r.PUT()等一系列http方法
	r.Run(":8080")
}

func HandleGet(g *gin.Context) {
	g.String(http.StatusOK, "This is a get request from %s\n", g.Request.Host)
}

func HandlePost(g *gin.Context) {
	g.String(http.StatusOK, "This is a post request from %s\n", g.Request.Host)
}

```

这段代码中分别使用 `.GET()` ， `.POST()` ， `.Any()` 等方法为对应的路径下添加相应的http方法，如果用错误的http方法访问了路径，会被返回404。

运行如下的bash脚本，得到以下结果，如果你在观察运行时的输出，往往也能得到相应的结果。

```sh
curl -X GET 127.0.0.1:8080/some-get
curl -X POST 127.0.0.1:8080/some-get
echo
curl -X GET 127.0.0.1:8080/some-post
echo
curl -X POST 127.0.0.1:8080/some-post
curl -X GET 127.0.0.1:8080/any
echo
curl -X POST 127.0.0.1:8080/any
echo


#This is a get request from 127.0.0.1:8080
#404 page not found
#404 page not found
#This is a post request from 127.0.0.1:8080
#{"method":"GET"}
#{"method":"POST"}
```

这里在为 /any 路径添加 handle function 时使用了匿名函数的语法。很多教程中会使用这种匿名函数的语法，但笔者不建议在工程中使用，它会降低代码的可读性。匿名函数的大致语法是这样的:

`func(参数列表)返回值 {函数体} (调用参数)`

```go
c := func(a,b int) int {return a+b} (1,2) 
//这里c=3，为int类型
```

!!! notes "为什么要区分方法"

    区分 GET, POST 等方法是为了更好的设计后端的接口(api)，比如同一路径下，GET方法往往用于获取资源，而POST往往用于添加资源。(当然，其他http方法也有对应的动作，不过它们常常都被POST及其不同的参数取代了)

### 2. 接收前端传来的参数

我们已经在前文 fancy版本的Web服务器 中展示了一种向后端传递请求参数的办法，这种json传参的方式一般搭配post方法使用。下文主要讲解另外2种传参的方式，其中 query string 和 json传参 是较为重要的，而 路径传参 作为知识补充。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
	"strconv"
)

func main() {
	r := gin.Default()
	r.GET("/add", HandleGetAdd)         //使用 query string 的路由
	r.Any("/:name/:id", HandleName)     //设置路径传参
	r.Run(":8080")
}

func HandleGetAdd(g *gin.Context) {
	a, err := strconv.Atoi(g.Query("a"))
	if err != nil {
		g.String(http.StatusBadRequest, "What are you doing?\n")
		return
	}
	b, _ := strconv.Atoi(g.Query("b"))
	g.String(http.StatusOK, "The answer is %d\n", a+b)
}

func HandleName(g *gin.Context) {
	name := g.Param("name")
	id := g.Param("id")
	g.JSON(200, gin.H{"name": name, "uuid": id})    //返回200状态码与相关信息
}
```

!!! notes "关于http状态码"

    http状态码的作用是对http请求处理做一个概括，2~5开头的状态码分别对应:请求被正常接收和 理解(2xx)、请求需要客户端进一步执行操作(3xx)、请求有错误(4xx)、处理请求出现了服 务器侧的问题(5xx)。

    具体到本届课涉及的代码，都应该返回200(statusOK)，202这样的返回一般用于异步接口。


运行如下脚本，可看到注释中的输出。

```sh
#GET求和，注意bash中’&‘是有特殊意义的
curl -X GET '127.0.0.1:8080/add?a=1234&b=2333' #路径参数
curl -X GET 127.0.0.1:8080/pyz/114514
echo
#输出:
#The answer is 3567 #{"name":"pyz","uuid":"114514"}
```

- query string 传参

    形如 127.0.0.1:8080/add?a=1234&b=2333 的请求就是在用 query string 传参。

    query string传参主要使用于GET方法，在某些前端的js库里面，它可能会禁止你在GET方法时使用请求体中的json传参数。


- 路径传参

    访问自己的b站，可以看到 `https://space.bilibili.com/xxxxxxx` 这样形式的url。这种id，名称的参数在主页形式的路径上用得较多。(从性质上讲，它可以被看作是强制请求者给出的参数)

传参数的方法多种多样，在项目接口设计的时候，我们一般需要保持**设计的一致性**，尽量合乎大众的设计准则。

### 3. 中间件与Cookie

当我们想完成一系列Handle function的时候，我们经常发现，我们需要重复很多的逻辑，比如，我们的网站要求登录的用户才有权访问，那么大量的页面都要有登录鉴权的逻辑，所以我们想把一些统一 的逻辑放在一起。更进一步的，我们希望通过鉴权机制完成更为高级的逻辑。

```go
package main

import (
	"crypto/sha256"
	"encoding/hex"
	"github.com/gin-gonic/gin"
	"net/http"
)

func Hash(ip string) string {
	hasher := sha256.New()
	hasher.Write([]byte(ip))
	hash := hex.EncodeToString(hasher.Sum(nil))
	return hash
}

func main() {
	r := gin.Default()
	r.GET("login", HandleLogin)
	v := r.Group("/verified", Verify)
	{
		v.GET("/accumulate", Accumulate)
		v.GET("/multiply", Multiply)
	}
	r.Run(":8080")
}

//使用make声明动态大小的空map
var record = make(map[string]int)

func Verify(g *gin.Context) {
	cookie, err := g.Cookie("count")
	if err != nil {
		g.String(http.StatusForbidden, "You did not login!\n")
		g.Abort()
	}
	if _, ok := record[cookie]; !ok {       //_下划线用于丢弃不使用的变量。尽管golang要求接受所有返回变量，并且使用它们，但可以用_这个 占位符来无视掉那些不想用的变量

		g.String(http.StatusOK, "You did not login!\n")
		g.Abort()
	}
	g.Next()
}

func Accumulate(g *gin.Context) {
	cookie, _ := g.Cookie("count")
	record[cookie]++
	times := record[cookie]
	g.String(http.StatusOK, "You've logged in %d times", times)
}

func Multiply(g *gin.Context) {
	cookie, _ := g.Cookie("count")
	record[cookie] *= 2
	times := record[cookie]
	g.String(http.StatusOK, "You've logged in %d times", times)
}

func HandleLogin(g *gin.Context) {
	ipAddr := g.Request.RemoteAddr
	ipAddrHash := Hash(ipAddr)
	if _, ok := record[ipAddrHash]; ok {
		g.String(http.StatusOK, "You've Already Logged In!\n")
	} else {
		record[ipAddrHash] = 0
		g.SetCookie("count", ipAddrHash, 3600, "/", ".", false, true)
		g.String(http.StatusOK, "You logged in!")
	}
}
```

上述代码设计了很多个知识点，下面一一进行说明

1. 路由组的概念和中间件

    我们使用了 `r.Group()` 声明一个路由组 /verified ，在这个路径下所有的访问都需要先执行 `Verifiy()` 这一 Handle Function。

    Handle function是递归执行的，可以认为 `.Next()` 是这个递归向深层的一个标志，而 `Abort()` 则 是结束整个访问，直接返回，类似递归中的`return`。


2. 登录赋予cookie

    这里，示例代码使用访问者 ip 的 SHA256 分配 cookie 值。赋予用户这个 cookie 的同时，服务 器端也要保存这个 cookie 到内存中，从而实现快速的信息获取。在这里，这个信息是由 `record` 这一 map 数据结构存储的。

    !!! notes "关于Cookie"

        使用访问者 ip 的 SHA256 作为 cookie ，这显然是不妥当的，但作为示例未尝不可。在实际场景中会有更稳妥的算法和合适的cookie过期机制。

3. cookie鉴权和存储信息

    `Verify()` 中间件使用 cookie 存在、且在 record 中作为合法登录的标志，实际应用中我们可能要考虑 cookie 是否过期等其他因素。两个数值操作 `accumulate` 和 `multiply` 则对用户的信息作了进一步更改与存储。

    这看起来是一个“有记忆的”后端了，但这还远远不够——存储在内存里的信息随着掉点就将丢失。我们不希望b站服务器一停机，收藏夹里的东西没了，学校也不希望服务器一停电，学生成绩没了。

    当然cookie信息还有可能又被篡改的危险，我们需要更鲁棒的方式分级存储不同敏感程度的用户信息。我们需要我们的后端和数据库做交互。

### 4. gorm和gorm的automigrate

我们介绍 gorm 作为我们与数据库交互的框架， gorm 框架保留了 go 的并发性(如果出了bug，一般而言报错的 goroutine 不会是1号 goroutine ，即主进程)，效率较高，同时较为人性化的维护了和数据库的连接，使编程时不必考虑保存问题。 gorm 采用了默认事务操作的机制，并发安全性较高，gorm 还提供了读写分离的支持，更适合大规模的业务。如果对数据库的“事务”概念不了解的话，可以把它理解为不受其他并行指令干扰的一系列指令。

下面提供一个简单的示例，展示gorm的使用。如果没有数据库相关的基础知识，可以先去阅读 MySQL 相关教程。

```go
package main

import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
)

type StudentInfo struct {
	Name   string
	ID     uint
	secret uint
}

func main() {
	dsn := "root:YOUR_USERNAME@(192.168.0.108:3306)/goservertest?charset=utf8mb4&parseTime=True&loc=Local"  //数据库连接信息
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})   //建立连接
	if err != nil {
		log.Fatal(err)
	}
	if err = db.AutoMigrate(&StudentInfo{}); err != nil {   //migrate 模型，不成功则 Fatal
		log.Fatal(err)
	}
	pyz := StudentInfo{
		Name:   "some",
		ID:     2020123456,
		secret: 10,
	}
	db.Create(&some)
}
```

上述代码完成了与数据连接，迁移模型，新建一个条目三件事情。但是我们注意到，建立的 `student_infos` 数据表只有两列，`secert`一项丢失了，这是由于 Go 的 OOP 特性，小写成员变量私有，这直接导致这一项将不会被gorm访问，存放到数据库中。

### 5. 增删查改和一些小技巧

这里仅仅展示最基本的增删查改，gorm提供了 `gorm.Model` 来支持软删除等高级模型操作，这里不做涉及。`Ops`函数中是增删查改操作。需要特别注意的一个方法是`update`，不要采用选出数据库中条目，再使用`save`，依靠主键冲突更新的方法完成。

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
	"strconv"
)

type FindPerson struct {
	Name   string
	Gender string
}

type ShortInfo struct {
	Name string
	ID   uint
}

type Person struct {
	Name   string `gorm:"type:varchar(20)"`
	ID     uint   `gorm:"not null;autoIncrement"`
	Age    uint
	Gender string `gorm:"type:varchar(20)"`
}

func main() {
	dsn := "root:YOUR_USERNAME@(192.168.0.108:3306)/goservertest?charset=utf8mb4&parseTime=True&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db.AutoMigrate(&Person{})
	CreateFromStudent(db)
	Ops(db)
}

func CreateFromStudent(db *gorm.DB) {
	var info ShortInfo
    //从一个指定表中寻找信息
	tx := db.Table("student_infos").Where("1=1").First(&info)
	if tx.Error != nil {
		fmt.Println(tx.Error)
	} else {
		db.Create(&Person{
			Name:   info.Name,
			Age:    20,
			Gender: "Male",
		})
	}
}

func Ops(db *gorm.DB) {
	var stus []Person
	//batch create
	for i := 1; i < 10; i++ {
		p := Person{
			Name: "some" + strconv.Itoa(i),
			Age:  uint(i),
		}
		if i%2 == 0 {
			p.Gender = "Male"
		} else {
			p.Gender = "Female"
		}
		stus = append(stus, p)
	}
	db.Create(&stus)
	//删除操作，语法有很多种，详见官网
	db.Model(&Person{}).Where("age=?", 5).Delete(&Person{})

	//切片查找，运用和模型相同名但更小的条目，更准确的查找所需要的条目
	var PersonList []FindPerson
	db.Model(&Person{}).Where("age < ?", 15).Find(&PersonList)
	fmt.Println(PersonList)
	//修改
	db.Model(&Person{}).Where("gender=?", "Male").Updates(&map[string]interface{}{
		"gender": "qwq",
	})
}
```

## 如何完成一个并发任务

### 1. 并发安全性

我们来运行以下程序。你可以预测一下，输出结果会是多少？

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	PrintTask1()
	time.Sleep(time.Second * 10)
	fmt.Println(shared)
}

var shared = 0

func Add(name int) {
	for i := 0; i < 10000; i++ {
		shared = shared + 1     //尝试把 shared 的值 + 1
	}
	fmt.Printf("%dth done!\n", name)
}

func PrintTask1() {
	for i := 0; i < 10; i++ {
		go Add(i)               //使用go <函数名称>开启一个执行函数的goroutine
	}
}
```

运行程序，发现尽管所有Add函数都执行完成，但结果却远不及100000，而且每次的结果不一致。 这便是出现了**并发问题**！

如果使用 `go run -race main.go` ，go编译器会启动检测运行时竞争的编译模式。 -race 报警如下:

```sh
==================
WARNING: DATA RACE
Read at 0x0000005cf5c0 by goroutine 7:
main.Add() /mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6-
concurrency/demo1/main.go:18 +0x3b
  main.PrintTask1.func1()
/mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6- concurrency/demo1/main.go:25 +0x39
Previous write at 0x0000005cf5c0 by goroutine 8:
  main.Add()
/mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6- concurrency/demo1/main.go:18 +0x53
main.PrintTask1.func1()
/mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6- concurrency/demo1/main.go:25 +0x39
Goroutine 7 (running) created at:
  main.PrintTask1()
/mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6- concurrency/demo1/main.go:25 +0x6f
main.main() /mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6-
concurrency/demo1/main.go:9 +0x29
Goroutine 8 (finished) created at:
  main.PrintTask1()
/mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6- concurrency/demo1/main.go:25 +0x6f
main.main() /mnt/c/Users/lipin/Desktop/year_2/暑培/Golang-上课示例代码/6-
concurrency/demo1/main.go:9 +0x29
==================
```

并且以 exit status 66 退出。

简单来说，多个 goroutine 尝试修改内存中同一个变量的值。然而，修改某一变量的值并不是一个 “原子操作” ，对于最底层的处理器而言，需要多个步骤来完成（例如取值、对值进行运算、存储新值），因此会出现如下情况：

1. goroutine 1 想要将变量的值加 1， 因此取出了目前的变量值 a.

2. goroutine 2 也想要将变量的值加 1，而此时 goroutine1 还没有完成对变量值的修改， 因此goroutine2也取出了目前的变量值 a.

3. 两个 goroutine 先后完成了运算、存储新值的过程，但是最终得到的变量值只是 a + 1, 与预期的 a + 2 不符。

其他编程语言没有 goroutine 这一概念，但并发安全的问题也广泛存在，往往是各个线程间的竞争。

!!! notes "随机数？"

    尽管这个结果有一定的随机性，但请**不要**拿它做随机数生成器，因为这种竞态访问还是有一定规律可循的。


### 2. 锁

锁是解决上述问题的一个方案。正如它的名字所言，对于全局的变量，我们可以为它上一个“锁”，有锁的人可以对他修改，没有锁的人只能在一边看着。go提供了简单易用的锁，它自动完成了取锁不成的阻塞。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	PrintTask1()
	time.Sleep(time.Second * 10)
	fmt.Println(shared)
}

var shared = 0
var m sync.Mutex    //声明一个锁

func Add(name int) {
	for i := 0; i < 10000; i++ {
		m.Lock()    //上锁，只有我能改!
		shared = shared + 1
		m.Unlock()  //交出锁，允许其他人修改
	}
	fmt.Printf("%dth done!\n", name)
}
func Add2(name int) {
	for i := 0; i < 10000; i++ {
		//m.Lock()
		shared = shared + 1
		//m.Unlock()
	}
	fmt.Printf("%dth done!\n", name)
}

func PrintTask1() {
	for i := 0; i < 10; i++ {
		go Add(i)
	}
}
```

!!! notes "关于锁的理解"

    首先，锁是一个【约定】，你总可以写一段不使用锁，直接访问全局变量的代码。(当然，可以声明一些自带锁的变量，对于他们的访问都会被重载为先获得锁，再访问)

    其次，锁是一个不同线程之间的约定，它代表了执行一个公共操作(代码中，这个“公共操作” 是对shared这个变量加1)的机会，尽管这个操作能被很多个线程(或者是goroutine)执行，但我们希望它在某一个时刻至多只有一个线程在执行。所以可以认为锁的管控范围是执行这个公共操作的机会。

    最后，虽然看起来锁是一个全局变量，和其他的全局变量没什么区别，但不能用一个全局变量替代他，执行它的功能。因为锁的实现依赖于更底层的帮助，比如**线程级别的锁可能依赖操作系统的调度**，来实现互斥的访问。

### 3. produce-consume

这是上述问题的另一个解决方案。

如果我们把为全局变量加一看作一个任务的话，我们可以专门设立一个完成这个任务的worker。其他进程通过channel和worker通信，把他们的任务交给这个worker来做。

尽管在这个场景下，使用 worker 看起来有点多此一举，但是在后端搭建中，这样的思路是十分常见的。比如，我想完成一个从视频网站自动下载视频的存储器，我可能只希望在前端输入视频的名字，视频在后台完成下载，当前的页面应该有一个迅速的返回值。那么这个时候，就可以从我们的handle function中，通过管道，把视频相应的信息传给worker，由worker一个一个完成下载。

```go
package main

import (
	"fmt"
	"time"
)

type task struct {
	Producer int
	Content  int
}

var shared = 0
var work_channel = make(chan task, 10000)   //声明一个容量为10000的非阻塞管道

func main() {
	for i := 0; i < 10; i++ {
		go Produce(i)
	}
	go Work()
	time.Sleep(time.Second * 10)
	fmt.Println(shared)
}

func Produce(i int) {
	for j := 0; j < 10000; j++ {
		work_channel <- task{               //向管道中添加任务
			Producer: i,
			Content:  j,
		}
	}
}

func NotDone(arr [10]int) bool {
	for i := 0; i < 10; i++ {
		if arr[i] != 10000 {
			return true
		}
	}
	return false
}

func Work() {
	var arr = [10]int{0}
	for NotDone(arr) {
		t := <-work_channel
		arr[t.Producer]++
		shared++
	}
	fmt.Println("Done!")
}
```

## 部署

对于 Go 后端而言，有两种常见的部署方式:

1. 直接编译可执行文件，在后端服务器上运行，这是naive的部署方法，主要适用于纯后端的场景。 当前端的负载均衡，前后端沟通在一个内网时，这就足够了。(软工课就是这样的)

2. 结合docker做部署，请参考网站内 docker 教程。


## 后续拓展

+ 了解 restful-api 的概念 https://restfulapi.net/
+ 了解 Go 后端的 redis 
+ 进行单元测试
+ 深入了解 gin, gorm

## 资源链接

+ the go programing language，中文版见 https://github.com/golang-china/gopl-zh
+ go语言官方文档 https://golang.google.cn/doc/
+ gorm中文官网 https://gorm.io/zh_CN/docs/
+ gin框架repo中的readme，文件中有很多使用示例。repo链接 https://github.com/gin-gonic/gin


