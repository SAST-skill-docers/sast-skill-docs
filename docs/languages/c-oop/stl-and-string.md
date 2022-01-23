# STL 和字符串处理

## `string` 字符串类

STL 为我们提供了方便的 `string` 类型：

+ **允许简洁的拼接操作**

```cpp
string fullname = firstname + " " + lastname;
```

+ **也能够使用惯用的输入输出方法**

```cpp
std::cout << fullname << std::endl;
```

+ **构造方式**

```cpp
string s0("Initial string"); // 从 C 风格字符串构造
string s1; // 默认空字符串
string s2(s0, 8, 3); // 截取，index 从 8 开始，长度为 3
string s3("Another character sequence", 12); // 截取
string s4(10, 'x');	// 复制字符
string s5(s0.begin(), s0.begin() + 7); // 复制截取
str.c_str(); // 转换为 C 风格字符串
```

+ **迭代器**

```cpp
// 方式一
#include <string>
#include <iostream>
int main() {
    std::string zcy = "Darling I'll love you till we are senventy";
    for (auto it = zcy.begin(); it != zcy.end(); it++) {
        std::cout << *it << std::endl;
    }
    return 0;
}

// 方式二
for (char c: zcy) {
    std::cout << c << std::endl;
}
```

+ **常见函数**

```cpp
cout << str[1]; // 访问元素
str[1] = 'a'; // 修改元素
str.size() // 查询长度
str.clear() // 清空 
str.empty() // 查询是否为空
str.push_back('a');
str.append(s2); // 向尾部增加

// 直接按照字典序来比较
string a = "alice", b = "bob";
a == b // false
a < b  // true
```

+ **类型转换**

```cpp
// 数值类型字符串化
to_string(1); // "1"
to_string(3.14); // "3.14"
to_string(3.1415926); // "3.141593"，注意精度损失
to_string(1 + 2 + 3); // "6"

// 字符串转数值类型
int a = stoi("2001"); // a = 2001
std::string::size_type sz; // size_t alias
int b = stoi("50 cats", &sz); // b = 50 sz = 2
int c = stoi("40c3", nullptr, 16); // c = 0x40c3，十六进制
int d = stoi("0x7f", nullptr, 0); // d = 0x7f，自动检查进制
double e = stod("34.5"); // e = 34.5
```

+ `std::string` 和 `std::vector<char>` 的区别

	+ `string` 可以 `length()` 或者 `size()` 来获取长度，但是 `vector` 只有 `size()` 函数。

	+ `string` 可以通过 `cin >> str` 从标准输入输入字符串。

	+ `string` 可以通过 `str += "abc"` 向字符串尾部添加字符。

	+ 都可以使用 `for (char c: str)` 遍历字符串中的所有字符。

## `iostream` 输入输出流

```cpp
cout << str << endl;
cin >> str;
ostream& operator<<(ostream& out, const Test& src) {
	out << src.id << endl;
	return out;
} 
```

基类：`istream` 和 `ostream`，而 `iostream` 是派生类。`cin`、`cout` 是具体的对象。`ifstream`、`ofstream`、`fstream` 都是面向文件的输入输出流。也可以通过字符串来定义交互方式，比如 `sstream`。

+ **`ostream` 即 output stream，是 STL 库中所有输出流的基类**

它重载了针对基础类型的输出流运算符，接受不同类型的数据，再调用系统函数进行输出。统一了输出接口，改善了 C 中输出方式混乱的状况，比如 C 中需要用户关心非常多的内容，手动设置输出类型，常常出错。

```cpp
printf("%d %f %s", 1, 2.3, "hello");
```

`cout` 是 STL 中内建的一个 `ostream` 对象，它会将数据送到标准输出流（一般是屏幕）。

```cpp
class ostream {
public:
	ostream& operator<<(char c) {
		printf("%c", c);
		return *this;
	}

	ostream& operator<<(const char* str) {
		printf("%s", str);
		return *this;
	}
} cout;
```

语句 `cout << "hello" << ' ' << "world"` 先执行 `cout << "hello"` 调用第二个函数，返回 `c1`（`cout` 的引用）。再执行 `c1 << ' '` 调用第一个函数，返回 `c2`（`cout` 的引用）。最后执行 `c2 << "world"` 调用第二个函数。

## 格式化输出

```cpp
#include <iomanip>
cout << fixed << 2018.0 << " " << 0.0001 << endl; // 固定小数位数
cout << scientific << 2018.0 << " " << 0.0001 << endl; // 科学计数法
cout << defaultfloat;  // 还原默认输出格式
cout << setprecision(2) << 3.1415926 << endl; // 输出精度设置为 2
cout << oct << 12 << " " << hex << 12 << endl; // 八进制输出与十六进制输出
cout << dec; // 还原十进制
cout << setw(3) << setfill('*') << 5 << endl; // 设置对齐长度为 3，对齐字符为 *
```

自己实现：

```cpp
class setprecision {
private:
	int precision;
public:
	setprecision(int p): precision(p) { }
	friend class ostream;
};

// 函数类
class ostream {
private:
	int precision; // 记录流的状态
public:
	ostream& operator<<(const setprecision &m) {
		precision = m.precision;
		return *this;
	}
} cout;

cout << setprecision(2);
```

借助辅助类，设置成员变量。这种类叫流操纵算子。

为什么重载流运算符要返回引用，而不是直接返回 `cout`？

始终在用同一个输出流对象 `cout`，没有构造新的 `ostream`。

```cpp
ostream(const ostream&) = delete;
ostream(ostream&& x);
```

禁止复制、只允许移动，仅使用 `cout` 一个全局对象。

+ **为什么只能使用一个对象？**
	+ 减少复制开销
	+ 一个对象对应一个标准输出，符合 OOP 思想
	+ 多个对象之间无法同步输出状态
+ **是否能做得更好？**
	+ 全局对象往往引入初始化顺序问题
	+ 单件模式（Singleton Pattern），在之后的设计模式中会介绍

## 文件输入输出流

以文件输入流作为例子：`ifstream` 是 `istream` 的子类，功能是从文件中读入数据。

打开文件：

```cpp
ifstream ifs("input.txt");
ifstream ifs("binary.bin", ifstream::binary); // 以二进制形式打开文件
ifstream ifs; // 先创建一个 sfstream 类的对象
ifs.open("file");
ifs.close();
```

例子：

```cpp
#include <iostream>
#include <string>
#include <cctype>
#include <fstream>
using namespace std;

int main() {
	ifstream ifs("input.txt");
	while (ifs) { // 判断文件是否到末尾，利用了重载的 bool 运算符
		ifs >> ws; // 除去前导空格 ws 也是流操纵算子
		int c = ifs.peek(); // 检查下一个字符，但不读取
		if (c == EOF) break;
		if (isdigit(c)) {
			int n;
			ifs >> n;
			cout << "Read a number: " << n << endl;
		} else {
			string str;
			ifs >> str;
			cout << "Read a word: " << str << endl;
		}
	}
}
```

辅助函数：

```cpp
getline(cin, str);

/* 其他操作 */
get(); // 读取一个字符
ignore(int n = 1, int delim = EOF); // 丢弃 n 个字符，或者直至遇到 delim 分隔符
peek(); // 查看下一个字符
putback(char c); // 返还一个字符
unget(); // 返还一个字符
```

+ 为什么 C++ 使用流输入取代了 `scanf`？

`scanf` 不友好，不同类型要使用不同的标识符。

对比：

```cpp
scanf("%d %hd %f %lf %s", &i, &s, &f, &d, name);
cin >> i >> s >> f >> d >> name;
```

其中：

+ 安全性：`scanf("%d %d", &a)` 可能写入非法内存；
+ 可拓展性：`cin >> obj` 可重载流操作运算符
+ 性能：`scanf` 在运行期间需要对格式字符串进行解析，`istream` 在编译期间已经解析完毕

## 字符串流

以输入输出流作为例子：

+ `stringstream` 是 `iostream` 的子类
+ `iostream` 继承于 `istream` 和 `ostream`
+ `stringstream` 实现了输入输出流双方的接口

```cpp
#include <sstream>
using namespace std;

int main() {
    stringstream ss;
    ss << "10"; // 将 10 存入 ss
    ss << "0 200";
    int a, b;
    ss >> a >> b; // a = 100, b = 200
}
```

+ 什么是输入，什么是输出？

主体是你的程序，客体是你的 `sstream`、`ifstream`。输入输出是相对程序而言的。