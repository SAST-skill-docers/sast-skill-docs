# Python 语言基础

<center>
	<img src="https://i.loli.net/2021/09/27/Cha4mLVvf6qbnlU.png" />
</center>

!!! tip "如何高效地学习编程语言"

    当我们在说学习一门新的编程语言的时候，我们到底在说什么？
    
    C++, Java, Python, JavaScript, Typescript...
    
    一门又一门的编程语言同中有异，异中有同。虽说“实现目的”是我们学习新的编程语言的直接驱动力，可如何高效地掌握一门编程语言，使其为我所用，是个值得我们思考的问题。
    
    我们尝试用过往的经验来对这个问题给出某种意义上的解答。也许你已经修过了《程序设计基础》或是《计算机程序设计基础》等等课程，我们不妨回忆在入门第一门编程语言时的流程。
    
    我们先介绍了一些最简单的**数据类型**，然后引入了一些加加减减的**操作符**。然后，我们学习了基本的**程序流程控制语句**，如**顺序语句**、**条件语句**和**循环语句**，以掌握程序执行的具体流程。然后，我们学习了一些较为方便的**数据组织方式**，如 C++ 中的**数组**、**结构体**、**STL** 等等，进而用**面向对象**的思维来思考问题。此外，我们还要了解使用这门编程语言所具有的独特的优势，比如一门编程语言的**语法糖**，或是使用这门语言能让我们极为便利地完成什么功能。
    
    因此，我们在学习一门新的编程语言时，不妨就仿照着这个思路，将学到的内容按照上述主题进行归类整理。

!!! info "什么是语法糖？"

    语法糖（英语：Syntactic sugar）是由英国计算机科学家彼得·兰丁发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能没有影响，但是更方便程序员使用。语法糖让程序更加简洁，有更高的可读性。
    
    Excerpted from [Wikipedia](https://zh.wikipedia.org/wiki/%E8%AF%AD%E6%B3%95%E7%B3%96).

!!! info "Checkpoint!"

	我们推荐初学 Python 的读者按顺序阅读本文档。文档中有若干 Checkpoint，每个 Checkpoint 都是一个简单的 Python 编程任务，可以快速检验你是否理解了文档讲解的知识。

## 前置知识

+ 基本的代数思维
+ 一定的逻辑能力
+ 先修程序设计课程的程序逻辑，对数据组织形式的一定理解和一定的面向对象思想

## Python 可以用来做什么

+ 开发网页前端与后端
+ 在数据统计与分析中极具优势
+ 在机器学习领域有极为重要的作用
+ 胶水型语言，简洁灵活
+ <s>拯救你的大一小学期</s>

## 安装与 Hello World

### Python 的安装

!!! info "Python 版本的选择"

	Python 现在的主流版本是 **Python3**，目前的最新版本是 3.10，官方会对该版本进行 Bug 修复，而较为稳定的版本是 3.9 与 3.8，针对其仅会推出安全性相关的修复。
	
	目前在网络上找到的很多教程都还是 **Python2** 风格的，而 Python 官方在 2020/1/1 已经停止了对 Python2 的官方支持。因此，尽快升级并熟悉 **Python3** 的语法，是很有必要的。

!!! info "编译型语言与解释型语言"

	我们已经熟悉的 C++ 是一种**编译型语言**。对于编译型语言，开发完成以后需要将所有的**源代码**都转换成**可执行程序**，而这种转换一般是不能跨平台的。也就是说，我们要针对不同的平台生成不同的可执行文件。
	
	而我们将要介绍的 Python 是一种**解释型语言**。每次执行程序都需要一边转换一边执行，用到哪些源代码就将哪些源代码转换成机器码，用不到的不进行任何处理。每次执行程序时可能使用不同的功能，这个时候需要转换的源代码也不一样。相比于编译型语言，解释型语言几乎都能跨平台，“**一次编写，到处运行**”是真实存在的。也就是说，可以轻松实现在 Windows 上编写 Python 源代码，然后丢到 Linux 上去运行，只要双方配置了相同的解释器环境即可。
	
	参考自 http://c.biancheng.net/view/4136.html.

针对 Windows 用户，请访问 https://www.python.org/downloads/windows/。针对 macOS 用户，请访问 https://www.python.org/downloads/macos/。下载对应的包然后安装即可。

在安装时记得勾选 Add To Path，将安装路径保存为系统环境变量。

!!! note "什么是环境变量？"

	可参考 https://zhuanlan.zhihu.com/p/82011100，在了解环境变量的基本概念后，如果你忘记了在安装时将 Python 所在路径添加到环境变量中，可以自行尝试将其添加入 Path。

### 交互式窗口与脚本文件

基于解释型语言的特点，Python 代码可以分为**交互式窗口**和**脚本文件**两种**执行方式**。

+ 交互式窗口：如在命令行输入 `python`，即可进入交互式窗口，此时如同“问答”一般，输入一行代码后，Python 立刻执行该行代码并给出返回值。退出界面的方法是按 `Ctrl+D` 或者执行 `exit()`（Windows 用户请使用 `Ctrl+Z`）。

+ 脚本：经典运行方法 `python xxx.py`，Python 会将整个脚本按照语句的顺序依次从头到尾执行。

<center>
	<img src="https://i.loli.net/2021/09/28/x2OUFItBZC4oN7R.png" />
	<p style="text-align: center; margin-top: 0px; color: gray; padding: 0px">交互式窗口的例子</p>
</center>

<center>
	<img src="https://i.loli.net/2021/09/28/Ti2VR4DtbgcdlkM.png" />
	<img src="https://i.loli.net/2021/09/28/Dvpkbd7KjLTH2yl.png" />
	<p style="text-align: center; margin-top: 0px; color: gray; padding: 0px">脚本文件的例子</p>
</center>

对于各种工程项目来说，我们自然是选择用脚本文件来存储代码，方便我们进行每一次调用。而针对初学者来说，我们大可尝试用交互式窗口来入门。后续附代码的部分，如果出现了顶格 `>>>` 的情况，则暗示这是一个交互式窗口，有 `>>>` 的是代码，其他的是执行代码的输出。

### Hello world

!!! question "[Checkpoint Python-01] Hello World"

	好，现在你已经理解了 Python 程序是如何运行的 —— 试试看！
	
	1. 请尝试在控制台使用交互式窗口方式，使程序输出欢迎信息。如 `"Hello, c7w!"`。
	
	2. 请尝试编写脚本文件使其能正确输出 1. 中的欢迎信息。并思考，C++ 中你是怎样实现对于不同的用户，可以呈现不同的欢迎信息的呢？
	
	提示：

	- 你可以通过仔细观察上面的图片来解决问题
	- 接下来我们将讲解变量的定义与程序的输入输出

## 变量类型与运算

与 C 和 C++ 大致相同，一些需要特别注意之处或有区别之处**用加粗表示**。

### 基本变量类型

+ `int`。变长整数，默认是 4 字节，有需要时自动增长。注意**整除运算符是 `//`**，而普通除法 `/` 会得到 `float` 类型（注意与 C 系语言区分）。除了十进制外，还支持十六进制、八进制、二进制的表示。
+ `float`。8 字节浮点数，相当于 C 系语言的 `double`。
+ `bool`。即众所周知的 `True` 与 `False`，注意**首字母大写**，用作数值计算时与 C 系语言一样分别视作 `1` 与 `0`。
+ `string`。字符串是以**单引号 `'` 或双引号 `"`** 括起来的任意文本，比如 `'abc'`、`"xyz"`。
+ `NoneType`。即 `None`、空值。后面会介绍到用处，常用于返回值、特判。

```python
>>> a = 124
>>> print(a)
124
>>> a = 'c7w'
>>> print(a)
c7w
>>> a = None
>>> print(a)
None
>>> if a:
...     print (a) # Here a == None, do not print
...
>>> a = 'c7w'
>>> if a:
...     print (a) # Here a == 'c7w', do print
...
c7w
```

!!! note "Python 是一种动态类型语言"

	我们尝试与熟悉的 C++ 语言对比，解释静态类型语言与动态类型语言的区别。
	
	**静态类型语言**中，每个变量都有确定的数据类型。变量的类型在编译期就被完全确定，且此后变量的类型无法改变。
	
	与其相对应的是**动态类型语言**：变量并没有确定的数据类型，这意味着变量的类型可以在运行期改变。
	
	比如，在 C++ 中：

	```cpp
	int c = 1;
	char m = 'x';
	std::string a = "";
	```
	我们不能尝试使用 `a = 1`，因为我们已经规定了 `a` 是 `std::string` 类型。
	
	而在 Python 中，我们可以尝试：

	```python
	a = 123
	a = 100.0
	a = "string"
	a = [1,2,3] # List, would be explained later
	```
	我们并没有对变量 `a` 进行过声明，而可以直接使用，并且在使用过程中可以随意地变换其类型。
	
	Reference: https://blog.csdn.net/alashan007/article/details/100742591.

!!! note "Python 是一种强类型语言"

    注意区分“强/弱类型”与“静态/动态类型”，这是一对相互正交的概念。

	**强类型**指值拥有确定的类型且不可（隐式地）相互转换，**弱类型**则指不同类型的值之间可以隐式转换。

	类型系统的强/弱并不像动态/静态那样有明显的界限。一般认为，JavaScript、Visual Basic 等是典型的弱类型语言，允许一部分隐式转换的 C 和 C++ 也被认为是弱类型语言，而 Java、C#、Python 等则是典型的强类型语言。

	例如，在 JavaScript 中：

	```javascript
	var a = "5";
	var b = 4;
	var c = [3];
	var d = null;
	console.log(a - b + c - d);
	// Output:
	// 13
	```

	可以看到即使这些变量的类型完全不同，它们也能进行运算并产出不太符合直觉的结果。

	而在 Python 中：

	```python
	a = "5"
	b = 4
	print(a + b)
	// Output:
	// Traceback (most recent call last):
	// File "<stdin>", line 1, in <module>
	// TypeError: can only concatenate str (not "int") to str
	print(a + str(b))
	// Output:
	// 54
	```

	可见将不同类型的值直接进行运算会导致错误，必须先将其中的一些值显式转换为正确类型的值。

### 运算符

下列是 Python 语言之中的合法运算符，其含义与 C/C++ 语言基本一致。其后列出了相应的重载函数，这些重载函数的用法在后续的面向对象章节之中再详细介绍。

**算术运算符**

+ 加法运算符 `+`：`__add__`
+ 减法运算符 `-`：`__sub__`
+ 乘法运算符 `*`：`__mul__`
+ 真除运算符 `/`：`__trudiv__`
+ 整除运算符 `//`：`__floordiv__`
+ 模运算符 `%`：`__mod__`
+ 幂运算符 `**`：`__pow__`

**比较运算符**

+ 小于运算符 `<`：`__lt__` 
+ 小于等于运算符 `<=`：`__le__`
+ 大于运算符 `>`：`__gt__`
+ 大于等于运算符 `>=`：`__ge__`
+ 相等运算符 `==`：`__eq__`
+ 不等运算符 `!=`：`__ne__`

**位运算符**

+ 按位与运算符 `&`：`__and__`
+ 按位或运算符 `|`：`__or__`
+ 按位异或运算符 `^`：`__xor__`
+ 按位取反运算符 `~`：`__invert__`
+ 左移运算符 `<<`：`__lshift__`
+ 右移运算符 `>>`：`__rshift__`

**逻辑运算符**

+ 逻辑与运算符 `and`
+ 逻辑或运算符 `or`
+ 逻辑非运算符 `not`

注意，对于 `and` 或 `or`，得到的结果不会直接转换为 `bool`，而是能够得出结果的最后一个变量。

注意，混用 `and`、`or` 时需要考虑 `and` 的优先级更高，为了代码的可读性建议不要混用，或者显式地加上小括号。

**三目运算符**

+ 三目运算符 `a if condition else b`

如 `abs_x = x if x >= 0 else -x`。

**其它运算符**

+ 包含运算符 `in`，用于判定某个变量是否包含于容器中
+ 不包含运算符 `not in`
+ 同地址运算符 `is`，用于判定两个变量的地址是否相同
+ 不同地址运算符 `is not`

!!! question "[Checkpoint Python-02] Operators"
	
	1. 【精度测试】Python 的“高精度”体现在何处？尝试使用 Python 进行以下运算，并输出其结果。
	
		1. $a = 10^{2000} - 1$
	
		2. $a = 11451419198101919810114514 \times 11451419198101919810114514$
	
		3. $a = 10^{-200} \times 10^{-400}$
	
		【提示】程序中可以使用科学计数法表示：我们可以用 `1e-5` 来表示 $10^{-5}$。
	
	2. 请尝试解释以下表达式的输出结果。
	
		1. `3 or 4`
	
		2. `3 and 4`
	
		3. `0 or 1 and 2`

		4. `0 and 1 or 2`
	
		5. `1 or 0 and 2`

## 程序控制语句

### 顺序语句

代码块（英语：Code block）是个比较重要的层级概念，众所周知 Python 没有大括号进行显式地分块，因此它分块需要特别的手段：**冒号配合缩进**。

缩进有一套很完备的规则，推荐统一使用 Tab 或者四个空格作为一级，每当多一层层级嵌套时便会多一个 Tab 或者四个空格。而不要混用 Tab 和空格，不然会出现错误。

注释的写法：

1. 单行注释。代码行以 `#` 开头

2. 多行注释。使用三个单引号，或者三个双引号将其要注释的内容扩起来（后续我们会提到，这是多行字符串的处理方法）

### 条件语句

`if-elif-else` 条件判断结构：

```python
age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')
```

### 循环语句

`while` 循环：

```python
a = 1
while a <= 100:
    print(a)
    a += 1 # 注意没有 a++ 的写法
```

`for` 循环：

```python
for i in range(0, 100): # range(0, 100) = [0, 1, 2, ..., 99]
	print(i)            # 本质上是在一个可迭代对象中遍历所有元素
    
```

### 函数的写法

定义函数的写法：

```python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```

注意第一行固定格式 `def <func_name>(params, ...):`，其他和 C/C++ 差别不大。作为动态类型语言，函数返回类型不固定，可以有多个 `return` 以在不同情况下返回不同内容，或者没有 `return`（等价于 `return None`）。

!!! question "[Checkpoint Python-03] Runtime logic"
	
	请定义一个函数 `fib`，接受一个整型变量 `n` 作为输入，返回斐波那契数列的第 `n` 项。
	
	请给出问题解法的递归版本与迭代版本。

## 数据及其组织形式

### 再谈字符串

!!! note "什么是 Unicode"

	最早只有 127 个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为 ASCII 编码，比如大写字母 `A` 的编码是 `65`，小写字母 `z` 的编码是 `122`。
	
	但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和 ASCII 编码冲突。所以，中国制定了 GB2312 编码，用来把中文编进去。
	
	你可以想得到的是，全世界有上百种语言。日本把日文编到 Shift_JIS 里，韩国把韩文编到 Euc-kr 里，各国有各国的标准，就会不可避免地出现冲突，结果就是，在多语言混合的文本中，显示出来会有乱码。
	
	因此，Unicode 字符集应运而生。Unicode 把所有语言都统一到一套编码里，这样就不会再有乱码问题了。	
	
	新的问题又出现了：如果统一成 Unicode 编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用 Unicode 编码比 ASCII 编码需要多一倍的存储空间，在存储和传输上就十分不划算。
	
	所以，本着节约的精神，又出现了把 Unicode 编码转化为“可变长编码”的 UTF-8 编码。UTF-8 编码把一个 Unicode 字符根据不同的数字大小编码成 1-6 个字节，常用的英文字母被编码成 1 个字节，汉字通常是 3 个字节，只有很生僻的字符才会被编码成 4-6 个字节。如果你要传输的文本包含大量英文字符，用 UTF-8 编码就能节省空间。

- 单个元素是 Unicode 字符。在最新的 Python 3 版本中，字符串是以 Unicode 编码的。也就是说，Python 的字符串支持多语言。

```python
>>> a = "你好世界"
>>> a[0] # 取 a 的第 0 个元素
'你'
>>> a[1]
'好'
>>> a[2]
'世'
>>> a[3]
'界'
```

- 字符转义。如果字符串内部既包含 `'` 又包含 `"` 怎么办？可以用转义字符 `\` 来标识，比如：

```python
'I\'m \"OK\"!'
```

- 多行字符串。如果字符串内部有很多换行，用 `\n` 写在一行里不好阅读。为了简化，Python 允许用 `'''...'''` 的格式表示多行内容。当然，这也是一种撰写代码注释的方式。

```python
>>> print('''line1
... line2
... line3''')
line1
line2
line3
```

- 格式化字符串。使用 `fstring`，其在形式上是以 `f` 或 `F` 修饰符为修饰前缀的字符串（即 `f'xxx'` 或 `F'xxx'`），以大括号 `{}` 标明被替换的字段。`fstring` 在本质上并不是字符串常量，而是一个在运行时运算求值的表达式。

```python
>>> r = 2.5
>>> s = 3.14 * r ** 2
>>> print(f'The area of a circle with radius {r} is {s:.2f}')
The area of a circle with radius 2.5 is 19.62
```

- 字符串运算

```python
>>> a = 'Hello'
>>> a + a
'HelloHello'
>>> a * 10 # 重复输出
'HelloHelloHelloHelloHelloHelloHelloHelloHelloHello'
>>> a[1: 3] # 获取 index 在 [1, 3) 中的元素构成的字符串
'el'
```

- 常用特殊函数

	+ `strip()` 去除前后的空字符

	```python
	>>> a = '   <-backspace->   '
	>>> a.strip()
	'<-backspace->'
	```

	+ `replace(str1, str2, num)` 批量替换

	```python
	>>> a = "我爱北京大学，北京大学是世一大，北京大学yyds"
	>>> a.replace("北京大学", "清华大学")
	'我爱清华大学，清华大学是世一大，清华大学yyds'
	```

### 扩展数据类型

**列表 `list`**

Python 内置的一种数据类型是列表。`list` 是一种类似于向量的结构，可以随时添加和删除其中的元素。

```python
>>> a = [2, 3] # Init a list

>>> a.append(4) # Append elements to list
>>> a
[2, 3, 4]

>>> b = ['a', 'c', 114514]
>>> b
['a', 'c', 114514]

>>> a + b # List "plus"
[2, 3, 4, 'a', 'c', 114514]

>>> a[0] = "c7w,yyds" # Change value of elements
>>> a
['c7w,yyds', 3, 4]

>>> a[-1] # Find the last element
4

>>> a.reverse() # Reverse the list
>>> a
[4, 3, 'c7w,yyds']

>>> p = [5, 4, 3, 2, 1]
>>> p.sort() # Sort the list
>>> p
[1, 2, 3, 4, 5]
```

**元组 `tuple`**

Python 的元组与列表类似，不同之处在于元组的元素不能修改。元组使用小括号，列表使用方括号。

元组创建很简单，只需要在括号中添加元素，并使用逗号隔开即可。

```python
tup1 = () # 空元组
tup1 = (50, ) # 元组中只包含一个元素时，需要在元素后面添加逗号
tup1[0] = 20 # Error! Elements of tuple cannot be changed.
```

** 字典 `dict`**

字典是另一种可变容器模型，且可存储任意类型对象，采用**键值对**存储方式。

字典的每个键值对用冒号 `:` 分割，每个键值对之间用逗号 `,` 分割，整个字典包括在花括号 `{}` 中。

```python
>>> d = {'name': 'c7w', 'gender': 'M', 'id': 2020000000} # Init the dict
>>> d
{'name': 'c7w', 'gender': 'M', 'id': 2020000000}

>>> d['gender'] # Get the value of certain key
'M'

>>> d['department'] = 'CST' # New a key-value pair
>>> d
{'name': 'c7w', 'gender': 'M', 'id': 2020000000, 'department': 'CST'}

>>> d['girlfriend'] # Would raise KeyError if key not found
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'girlfriend'
   
>>> d['id'] += 2 # Change value of certain key
>>> d
{'name': 'c7w', 'gender': 'M', 'id': 2020000002, 'department': 'CST'}
```

可以使用 `in` 关键字判断某个特定的 key 是否在字典中，也可以使用内置的 `dictObj.get()` 函数在 key 不存在时提供默认获取值。

**集合 `set`**

`set` 和 `dict` 类似，也是一组 key 的集合，但不存储 value。由于 key 不能重复，所以，在 `set` 中，没有重复的 key。

```python
>>> myList = [1, 1, 1, 2, 3, 3, 3, 3, 4]
>>> s = set(myList)
>>> s
{1, 2, 3, 4}
>>> s = list(s)
>>> s
[1, 2, 3, 4]
```

**面向对象语法**

如果采用面向对象式的思维，我们将具有共同属性与方法的一组个体抽象成一个类，在 Python 中的表达方式为：

```python
class Student(object):
    def __init__(self, name, score):
        self.name = name
        self.score = score

    def get_grade(self):
        if self.score >= 90:
            return 'A'
        elif self.score >= 60:
            return 'B'
        else:
            return 'C'
```

更多有关 Python 面向对象编程的知识请阅读：

+ https://www.liaoxuefeng.com/wiki/1016959663602400/1017495723838528
+ https://www.liaoxuefeng.com/wiki/1016959663602400/1017501628721248

### 文件读写

**`open()` 函数**

你必须先用 Python 内置的 `open()` 函数打开一个文件，创建一个 file 对象，相关的方法才可以调用它进行读写。

```python
file object = open(file_name [, access_mode][, buffering])
```

其中常用的读取模式有 `'r'`（只读）、`'w+'`（清空并重写）、`'a+'`（追加写入）。

**`read()` 函数**

`read()` 方法从一个打开的文件中读取一个字符串。

```python
# 打开一个文件
fo = open("foo.txt", "r")
str = fo.read()
print("读取的字符串是: ", str)
# 关闭打开的文件
fo.close()
```

**`write()` 函数**

`write()` 方法可将任何字符串写入一个打开的文件。需要重点注意的是，Python 字符串可以是二进制数据，而不是仅仅是文字。注意，`write()` 方法不会在字符串的结尾添加换行符即 `'\n'`。

```python
# 打开一个文件
fo = open("foo.txt", "w")
fo.write("www.runoob.com!\nVery good site!\n")
 
# 关闭打开的文件
fo.close()
```

!!! note "[Checkpoint Python-04] Numbers"
	
	1. 【数据结构】
	
		1. 分别找出 $1 \leq i \leq 1000000$ 内所有的是 $2, 3, 5$ 的倍数的数的对应的 `list`
	
		2. 将这些列表连接起来
	
		3. 将这些数去重
	
	2. 【文本写入】
		
		1. 将 1. 中的答案，输出为 `ans.txt`

## 语法糖

Python 的一大特色在于其丰富的语法糖，这些语法糖让 Python 代码与其他语言相比展现出了完全不一样的风格，这个风格也称作 Pythonic。熟练运用这些语法糖，可以简化代码编写并提高代码可读性。

### 自动生成元组与解包

```python
a = 1
b = 2
a, b = b, a # 自动生成了 Tuple 并交换次序，不需中间变量
```

### 多重比较

```python
a = 30
if 20 <= a <= 40:
    print(a)
```

### 可迭代对象的切片

```python
>>> a = list(range(0, 100))
>>> a
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95, 96, 97, 98, 99]

>>> a[10: 19] # [10, 19)
[10, 11, 12, 13, 14, 15, 16, 17, 18]
>>> a[-10: -2] # [-10, -2)
[90, 91, 92, 93, 94, 95, 96, 97]
>>> a[-1]
99
```

### 列表生成式

```python
>>> L = [x * x for x in range(100) if x % 10 == 0]
>>> L
[0, 100, 400, 900, 1600, 2500, 3600, 4900, 6400, 8100]
```

## 包管理

### 多文件项目

在计算机程序的开发过程中，随着程序代码越写越多，在一个文件里代码就会越来越长，越来越不容易维护。

为了编写可维护的代码，我们把很多函数分组，分别放到不同的文件里，这样，每个文件包含的代码就相对较少，很多编程语言都采用这种组织代码的方式。在 Python 中，一个 `.py` 文件就称之为一个**模块**（英语：Module）。

请注意，每一个包目录下面都会有一个 `__init__.py` 的文件。这个文件是必须存在的，否则，Python 就把这个目录当成普通目录，而不是一个包。`__init__.py` 可以是空文件，也可以有 Python 代码，因为 `__init__.py` 本身就是一个模块。在下列文件树中它的模块名就是 `mycompany`。

```python
mycompany
 ├─ web
 │  ├─ __init__.py
 │  ├─ utils.py
 │  └─ www.py
 ├─ __init__.py
 ├─ abc.py
 └─ utils.py
```

### 引入外部包

Python 为我们自带了很多内置模块，比如 `os`、`datetime`、`re` 等等，这里不在语言入门篇中赘述。当有运用的需求时，培养面向搜索引擎的问题解决能力是很重要的。

### 安装外部包

<center>
	<img src="https://i.loli.net/2021/10/05/IyFd6qrPpkeL27i.jpg" />
</center>

正是由于开源社区的存在，才使得 Python 能够如此便捷，如此强大。Python 为我们提供了极为方便的包管理器 `pip`，会在安装时附带。

Linux 用户可以使用 `sudo apt update && sudo apt install pip3` 来安装。

在 Python 中，安装第三方模块，是通过包管理工具 `pip` 完成的。例如，我们要安装一个第三方库 Pillow，其是一个高级的图像处理工具。我们就可以通过 `pip install pillow` 来安装。

此外，我们还可以使用 `anaconda`、`virtualenv` 等包来协助我们进行虚拟环境创建或者包管理，这里我们不再展开。

## 拓展学习

Python 的强大之处在于其丰富的第三方库，为了进一步学习、运用 Python 语言，我们推荐读者自行搜索下列第三方库的介绍、文档，并尝试在自己的程序中使用它们。有余力的读者甚至可以去阅读这些第三方库的源代码。

### 图像处理

+ Pillow

### 网站开发

+ Django
+ Flask

### 数据收集

+ requests
+ beautifulsoup4

### 统计分析

+ Numpy
+ Matplotlib
+ pandas

### 机器学习

+ Tensorflow
+ Pytorch

## 资源链接

+ 2020 暑培讲义 by rls
+ Python Docs <https://www.python.org/doc/>
+ Runoob <https://www.runoob.com/python3/python3-tutorial.html>
+ 廖雪峰 <https://www.liaoxuefeng.com/wiki/1016959663602400/>

