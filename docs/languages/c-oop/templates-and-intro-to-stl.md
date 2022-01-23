# 模板进阶与 STL 入门

## 命名空间

### 定义

为了避免在大规模程序的设计中，以及在程序员使用各种各样的 C++ 库时，标识符的命名发生冲突，标准 C++ 引入了关键字 `namespace`（命名空间），可以更好地控制标识符的作用域。

标准 C++ 库（不包括标准 C 库）中所包含的所有内容（包括常量、变量、结构、类和函数等）都被定义在命名空间 `std`（即标准命名空间）中。例如：`cout`、`cin`、`vector`、`set`、`map`。

也就是说，`namespace` 本身是一种关键字，这就是所谓的命名空间，而 `std` 仅仅是已经被定义的一个命名空间。

### 例子

#### 自定义命名空间

```cpp
namespace A {
    int x, y;
}
```

#### 使用命名空间

```cpp
A::x = 3;
A::y = 6;
// 对人为定义的命名空间 A 内的 x、y 进行赋值
```

#### `using` 申明

使用 `using` 声明简化命名空间使用。

```cpp
// 使用整个命名空间：所有成员都直接可用
using namespace A;
x = 3;
y = 6;
```

以及：

```cpp
// 使用部分成员：所选成员可直接使用
using A::x;
x = 3; A::y = 6;
```

任何情况下，都不应出现命名冲突。

## STL 初步

### 定义

标准模板库（英文：Standard Template Library，缩写：STL），是一个高效的 C++ 软件库，它被容纳于 C++ 标准程序库 C++ Standard Library 中。其中包含4个组件，分别为**算法**、**容器**、**函数**、**迭代器**。这些都是基于模板编写而成。

关键理念：将“在数据上执行的操作”与“要执行操作的数据”分离。

STL 的命名空间是 `std`，一般使用 `std::name` 来使用 STL 的函数或对象，也可以使用 `using namespace std` 来引入 STL 的命名空间。

```cpp
using std::stringc; // 不推荐在大型工程中使用，容易污染命名空间
```

关于 STL 的文档和例子可以在以下网址查询 http://www.cplusplus.com/。

多写多查多用，是学习 STL 库的最好方法。

### STL 容器

容器是包含、放置数据的工具。通常为数据结构。包括：

1. 简单容器（simple container）
2. 序列容器（sequence container）
3. 关系容器（associative container）

#### `pair` 容器

```cpp
// 最简单的容器，由两个单独数据组成
template<class T1, class T2>
struct pair {
	T1 first;
	T2 second;
	// 若干其它函数
};

// 通过 first、second 两个成员变量获取数据
std::pair<int, int> t;
t.first = 4;
t.second = 5;
```

一些辅助函数：

```cpp
// 创建：使用函数make_pair
auto t = std::make_pair("abc", 7.8);
// 自动推导成员类型
// 此处推导出的 t 的类型就是 pair 类型
```

```cpp
// 支持小于、等于等比较运算符。
// 先比较 first，后比较 second
// 要求成员类型支持比较
std::make_pair(1, 4) < std::make_pair(2, 3);
std::make_pair(1, 4) > std::make_pair(1, 2);
```

```cpp
#include <string>
int main() {
    std::pair<std::string, double> p1("Alice", 90.5); // 手工指定类型初始化
    std::pair<std::string, double> p2;
    p2.first = "Bob";
    p2.second = 85.0; // 先定义，再赋值
    auto p3 = std::make_pair("David", "95.0"); // 自动推导类型初始化
    return 0;
}
```

#### `tuple` 容器

C++11 新增，表示由若干成员组成的元组类型。

```cpp
template<class Types, ...>
class tuple;
```

通过 `std::get` 函数获取数据。

```cpp
v0 = std::get<0>(tuple1);
v1 = std::get<1>(tuple2);
```

其下标需要在编译时确定：不能设定运行时可变的长度，不能当做动态数组。

```cpp
int i = 0;
v = std::get<i>(tuple); // 编译错误
```

示例代码：

```cpp
#include <iostream>
#include <tuple>
using std::tuple;
using std::string;

int main() {
    std::tuple<int, double, string> t3 = {1, 2.0, "3"};
    std::get<0>(t3) = 4;
    std::cout << std::get<1>(t3) << std::endl;
    std::cout << std::get<0>(t3) << std::endl;

    int i;
    double d;
    string s;
    std::tie(i, d, s) = t3;
    i = 10;
    std::cout << i << std::endl;
    std::cout << std::get<0>(t3) << std::endl;
}

/*
output:
2
4
10
4
*/
```

从上述代码可以看出，实际上 `get` 函数返回的是 `tuple` 的某个元素的左值引用，可以赋值，也可以被输出。而 `tie` 函数实际上没有返回值，仅仅是完成了对应的简单赋值，并不是生成左值引用，改变 `i` 并不能改变 `tuple`。

`tuple` 的意义，用于函数多返回值传递：

```cpp
#include <tuple>
#include <iostream>

std::tuple<int, double> f(int x) { 
    return std::make_tuple(x, double(x) / 2);
}

int main() {
    int xval; 
    double half_x;
    std::tie(xval, half_x) = f(7);
    std::cout << xval << std::endl;
    return 0;
}

/*
output:
7
*/
```

作为 `tuple` 的特例，`pair` 可用于两个返回值的传递。除此之外，`pair` 在 `map` 中大量使用。

#### `vector` 容器

会自动扩展容量的数组，以循序的方式维护变量集合。		

```c++
template<class T, class Allocator = std::allocator<T>> 
class vector;
```

STL 中最基本的序列容器，提供有效、安全的数组以替代 C 语言中原生数组。允许直接以下标访问，访问速度快：

```c++
#include <vector>
using namespace std;
int main() {
	std::vector<int> x; // 创建
	x.size(); // 获取当前数组长度
	x.clear(); // 清空
	x.push_back(1); // 在末尾添加 1
	x.pop_back(); // 删除
	x.insert(x.begin() + 1, 5); // 在中间添加（低速）
	x.erase(x.begin() + 1); // 在中间删除（低速）
	return 0;
}
```

#### 迭代器

一种检查容器内元素并遍历元素的数据类型。

+ 提供一种方法顺序访问一个聚合对象中各个元素, 而又不需暴露该对象的内部表示。

+ 为遍历不同的聚合结构（需拥有相同的基类）提供一个统一的接口。

+ 对遍历元素使用类似指针。

以 `vector` 为例：

```c++
vector<int>::iterator iter;
```

定义了一个名为 `iter` 的变量，它的数据类型是由 `vector<int>` 定义的 `iterator` 类型。

```cpp
x.begin();
x.end();
```

`begin` 返回 `vector` 中初始位置的迭代器（指针），即 `x[0]`。

`end` 函数返回 `x[x.size()]`，注意到 `x.size()` 对应的不是最后一个元素，而是最后一个元素之后。

故而结合 `begin()` 和 `end()` 能够实现遍历所有元素，即遍历一个 `[0, size())`​ 左闭右开区间。

迭代器一般允许的操作：

```c++
++iter;
--iter;
iter += n;
iter -= n; // 迭代器移动
int dist = iter1 - iter2; // 返回元素位置差
*iter = 5; // * 是解引用运算符，返回的是左值引用
```

那么，如何利用 `iterator` 遍历 `vector` 呢？

因为 `end()` 不是最后一个元素，而是最后一个元素之后的位置，该代码可以遍历 `vec` 中所有元素：

```c++
for (vector<int>::iterator it = vec.begin(); it != vec.end(); ++it) { }

// 也可以用 auto 简化代码，编译器自动推导 it 是 vec 的 iterator
for (auto it = vec.begin(); it != vec.end(); ++it) { }

// 还可以直接这样遍历
for (auto x: vec) { }
```

补充：反向遍历器迭代整个 `vector`：

```cpp
for (auto zex = vec.rbegin(); zex != vec.rend(); zex++) {
    std::cout << *zex << std::endl;
}
```

这个语法利用 `rbegin` 与 `rend` 实现了从后往前遍历整个 `vec` 函数，注意对于 `zex` 仍然是采用 `++` 运算符。

##### 迭代器失效

当迭代器不再指向本应指向的元素时，称此迭代器失效。这并不是指指针本身出了问题，而是指向不再正确。

+ 调用 `insert`、`erase` 后，所**修改位置**的迭代器、以及**该位置之后**的所有迭代器失效。

+ 调用 `push_back` 等修改 `vector` 大小的方法时，如果 `vector` 恰好达到了 `capacity` 上限，编译器会给 `voctor` 扩容。扩容后整个 `vector` 会发生空间整体迁移，但是迭代器仍然指向之前的内存，从而会使**所有**迭代器失效。

!!! note "自动扩容"

    `vector` 是会自动扩展容量的数组，除了 `size`，另保存 `capacity`：最大容量限制。

    如果 `size` 达到了 `capacity`，则另申请一片两倍于 `capacity` 的空间，并整体迁移 `vector` 的内容。

    这一过程时间复杂度为均摊 $O(1)$，并且整体迁移过程使所有迭代器失效。

    每次以两倍为单位扩容实际上是满足了堆优化，是一个很优的扩容选择，将在数据结构中具体学习。

+ 因为扩容导致的迭代器失效​

在遍历的时候增加元素，可能会导致迭代器失效：

```c++
#include <iostream>
#include <vector>
using namespace std;
int main() {   
vector<int> vec = {1, 2, 3, 4, 5};
    for (auto it = vec.begin(); it != vec.end(); ++it)
        vec.push_back(*it);
    return 0;
}
```

这段代码会使 `vec` 变成 `123451234512345...`。直到 `vec` 达到 `capacity` 上限，此时 `it` 失效，出现 error。

其实这种情况蛮好理解的，因为每一次循环都会判定 `it` 是否等于 `vec.end()`，而 `vec.end()` 不断在变化。

故而导致循环不停止，直到迭代器因为容量的迁移而失效。

+ 因为删除导致的迭代器失效

```cpp
#include <iostream>
using namespace std;
#include <vector>

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    for (auto it = vec.begin(); it != vec.end(); it++) {
        cout << *it << endl;
    }
    cout << "=====================" << endl;

    auto first = vec.begin();
    auto second = vec.begin() + 1;
    auto find = vec.begin() + 6;
    vec.erase(second);
    auto ret = vec.erase(second);
    cout << *second << endl;
    cout << *ret << endl;
    cout << "my size is " << vec.size() << endl;
    cout << "how could I find " << *find << endl;
    for (auto it = vec.begin(); it != vec.end(); it++) {
        cout << *it << endl;
    }
    return 0;
}

/*
output:
1
2
3
4
5
=====================
4
4
my size is 3
how could I find 4113
1
4
5
*/
```

从上述代码可以见得：

1. `erase` 函数传入的参数和返回值类型都是 `iterator`，但是你可以选择不用新的 `iterator` 接受返回值，比如第 15 行。

2. `erase` 返回的迭代器指向的必定是被 `erase` 掉的所有元素之后的第一个元素。

3. 我们已经知道，对于 `vector` 使用 `erase`，会使得被 `erase` 的元素与随后的元素的迭代器全部失效，故而这些访问都没有意义，比如上文对于 `find` 的访问，由于 `find` 已经是野指针了，这个访问没有分析的意义。


!!! note "此处纠正对于指针的长期错误理解"

    指针、迭代器指向的一定是一个变量（左值，在内存中有对应空间即有地址），而不能只是一个值（右值）。
    
    故而指针指向地址这个说法本身就不准确，因为储存某个变量的内存空间的地址只是一个右值，而不是变量。
    
    变量 `A` 构造之后，储存 `A` 的内存空间的地址就固定了，此时如果指针 `P` 指向 `A`，等价于指向了这块地址，然而我们仍然能够让 `P` 指针指向新的变量 `B`，却无法改变储存 `A` 的内存空间。
    
    回到 `vec` 的 `erase` 函数，我们已经明确了 `erase` 函数返回的新迭代器将会指向被 `erase` 掉的元素之后的第一个元素。
    
    `erase` 操作有两种可能，第一种不会发生 shrink，被清除的数据之后的元素往前移动，填补之前被清除的元素。比如我传入 `vec[2]` 的迭代器，只 `erase` 掉 `vec[2]`，那么原本位于 `vec[3]` 的元素就移动到之前 `vec[2]` 所在的内存空间，而我返回的迭代器一定指向 `vec[3]`，所以看上去像是返回的迭代器和传入的迭代器都指向了同一个内存空间，但是这是显然错误的。不过是指向的元素碰巧先后占用了同一块内存空间。
    
    情况二，`erase` 导致了 shrink 的发生。也就是说 `vec` 容量减少的时候可能会发生 shrink，可能整个 `vector` 被搬到另外一个较小的内存空间。这个时候，`erase` 返回的指针仍然指向被 `erase` 掉的所有元素之后的第一个元素，储存这个元素的内存空间地址可能和之前的完全没有关系。
    
    一个绝对安全的准则：
    
    + **理解清楚迭代器所指的对象，在修改过容器后，换用新的迭代器，不使用之前的迭代器。**
    
    在 C++ Reference 中，容器的修改操作有一项 Iterator validity，表示该操作是否会引发迭代器失效。若修改容器后仍要使用原来的迭代器，查文档确定迭代器是否有效。
    
    http://www.cplusplus.com/reference/algorithm/find/?kw=find

#### `list` 容器

链表容器（底层实现是双向链表）。

```c++
#include <list>
#include <algorithm>

std::list<int> l; // 定义
l.push_front(1); // 插入前端
l.push_back(2); // 插入末端
std::find(l.begin(), l.end(), 2); // 返回迭代器
l.insert(it, 4); // it 为迭代器，将 4 插入到 it 指向的元素前面
```

注意，对 `list` 使用的 `find` 函数，需要包含头文件 `<algorithm>`。如果 `find` 函数没有搜索到想要的对象，会返回指向 `end` 的指针。

```cpp
#include <iostream>
using namespace std;
#include <list>
#include <algorithm>

std::list<int> l; // 定义
l.push_front(1); // 插入前端
l.push_back(2); // 插入末端
l.push_back(5); // 插入末端
l.push_front(100);// 插入前端
l.push_back(100); // 插入末端
l.push_back(2); // 插入末端
auto it = std::find(l.begin(), l.end(), 2); // 返回迭代器
l.insert(it, 4); // it 为迭代器，将 4 插入到 it 指向的元素前面

cout << *it << endl;
cout << "==========================" << endl;

for (auto it = l.begin(); it != l.end(); it++) {
    cout << *it << endl;
}

/*
output:
2
==========================
100
1
4
2
5
100
2
*/
```

上述例子再次验证了之前对于迭代器的理解。我的 `it` 迭代器指向的永远是第一个 `2` 这个元素。虽然我在 `it` 前面插入了 `4`，然后 `2` 这个元素往后移走了，但是 `it` 仍然指向 `2`。另外，我们也可以发现，`list` 的 `find` 功能遇见多个相同元素时，会默认返回从前到后第一个符合条件的元素的迭代器。

如果 `list` 的成员是自定义的 `class` 或 `struct`，可以通过重载 `operator==` 和自己定义构造函数来构造 `find`。

+ 注意：`==` 运算符重载的参数必须是常量左值引用​

```c++
class myint{
    int i;
    bool operator==(cosnt myint& other) { return i == other.i; }
    myint(int t): i(t) { };
};

int main() {
  list<myint>test;
  std::find(test.begin(), test.end(), myint(2)); // 查找 test 里面有没有成员 i 为 2 的 myint
}
```

结合链表的原理很容易就能理解以下特点：

+ 不支持下标等随机访问，即无法使用类似 `test[2]` 的语句。
    + 故而想要遍历 `list`，必须用迭代器。
+ 支持高速的在任意位置插入、删除数据（链表常常在需要不断插入删除的情况下使用）。
    + 其访问主要依赖迭代器。
    + 操作不会导致迭代器失效（除指向被删除的元素的迭代器外）。

关于 `list` 的更多函数参见：https://blog.csdn.net/yas12345678/article/details/52601578

#### `set` 关联容器

不重复元素构成的无序集合；内部按大小顺序排列，比较器由函数对象 `compare` 完成。无序是指不保持插入顺序（不同于 `vector` 和 `list`），容器内部排列顺序是根据元素大小排列。

```c++
std::set<int> s; // 定义
s.insert(1);
s.find(1); // 返回迭代器（位置）
s.erase(s.find(1)); // 导致被删除元素的迭代器失效（这句话是删除了集合里的元素 1）
s.count(1); // 1的个数，总是 0 或 1（存在性）
```

#### `map` 关联容器

`map` 的值类型为 `pair<key, val>`。

`map` 中的元素 `key` 互不相同，需要 `key` 存在比较器。

可以通过下标访问（即使 `key` 不是整数）。**下标访问时如果元素不存在，则创建对应元素，而不会提示访问错误**。也可使用 `insert` 函数进行插入。

所谓的创建对应元素：比如 `map` 包含的元素为 `pair<key, T>`，那么找不到 `key`，就会构造 `T()`，调用默认构造函数。

```cpp
template<class Key,
   class T,
   class Compare = std::less<Key>,
   class Allocator = std::allocator<std::pair<const Key, T> >
```

每个元素由两个数据项组成，`map` 将一个数据项映射到另一个数据项中。

```c++
#include <string>
#include <map> 
int main() {
    std::map<std::string, int> s; // 创建了 key 为 string 类型，val 为 int 类型的 map
    s["oop"] = 1; // 插入方式 1
    // 如果 "oop" 有对应的 val，那就是赋值，如果没有，可以理解为插入，也可以理解为构造
	// 看起来像是重载了 []，使得下标不是 int 也可以访问，这是 map 类型支持的操作。在 [] 中用 key 直接访问 val
    s.insert(std::make_pair(std::string("oop"), 1)); // 插入方式 2
    return 0;
}

```

`map` 支持用 `key` 直接访问对应迭代器：

```cpp
s.find(key); // 查询，仅需要提供 key 值，返回迭代器。
s.count(key); // 统计，仅需要提供 key 值，返回 0 或 1。
s.erase(it); // it 是迭代器，删除 it 指向的元素，导致 it 失效
s.erase(s.find(key));
```

`map` 常用作过大的稀疏数组或以字符串为下标的数组。

```cpp
#include <string>
#include <map> 

int main() {
    std::map<std::string, std::string> M;
    M["fp"] = "c";
    M["oop"] = M["fp"] + "++"; // M["oop"] = "c++"
	// string 的神奇操作："a" + "b" = "ab"
    return 0;
}
```

`set` 和 `map` 所用到的数据结构都是红黑树（一种二叉平衡树），其几乎所有操作复杂度均为 $O(\log n)$。

## 总结

### 序列容器（有顺序）和关联容器（无顺序）的区别

+ 序列容器：`vector`、`list`
+ 关联容器：`set`、`map`
+ 序列容器与关联容器的区别：
    + 序列容器中的元素有顺序，可以按顺序访问。
    + 关联容器中的元素无顺序，可以按数值（大小）访问。
    + `vector` 中插入删除操作会使操作位置之后全部的迭代器失效。
    + 其他容器中只有被删除元素的迭代器失效。

### 选择依据

在实际应用中，容器的选择可能需要综合考虑多方面因素，包括算法复杂度，功能需求，内存分配策略等，下面提供几个可供参考但不完整的角度（可以进一步阅读《Effective STL》）：

+ 算法复杂度：对于序列容器而言，如果在序列中间存在频繁的插入或删除操作，使用 `list`，否则使用 `vector`（或 `deque`）
+ 元素的顺序：如果需要在容器的任意位置插入新元素，需要选择序列容器而不是关联容器
+ 元素查找速度：如元素的查找速度是关键的考虑因素，可以考虑排序的 `vector` 或关联容器 `set`、`map` 等
+ 迭代器、指针或引用失效：如果希望在元素插入和删除操作后，迭代器、指针或引用失效的情况尽可能少出现，可以考虑使用 `list` 和关联容器 `set`、`map` 等

