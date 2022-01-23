# 函数对象和智能指针

## 函数对象

### 什么是函数指针

函数本质上存在于代码段，因此，每个函数在代码段中，也有着自己的入口地址。

函数指针是一个指针类型的变量。**它指向代码段中函数入口的地址**。

### 函数指针的声明

声明格式如下：

```c++
ret (*func)(args, ...);
```

其中，`ret` 是函数指针指向的函数的返回类型，`func` 是该指针的名字，而 `args, ...` 是该指针指向的函数的参数列表。

也就是说，这个名为 `func` 的指针只能指向返回值类型、参数类型都相同的**那些函数**。

函数指针由于返回值、参数不同而有很多种类型。每种类型只能指向特定的**一些函数**。

由于函数的类型比较难写，常常用 `auto` 关键字**推断函数指针类型**，直接令函数指针等于某一函数名。注意如果用 `auto`，在对函数指针声明的同时必须对其初始化，否则编译器无法自动推导指针类型。例如：

```c++
#include <iostream>
int arr[5] = {5, 2, 3, 1, 7};
void increase(int &x) { x++; }
void decrease(int &x) { x--; }
int main() {
    int flag; 
    std::cin >> flag;

    auto func = flag == 1 ? increase : decrease;
    for (int &x: arr) {
        func(x);
        cout << x;
    }
    return 0;
}
```

### 函数指针的初始化

给你一个函数，怎么让一个指针指向它呢？

与数组类似，在数组中，**数组名就是**该数组的**首地址**，函数也是一样，函数名就是**该函数的入口地址**，因此，函数名就是该函数的函数指针。也就是说，函数名 `A` 是个指针，该指针指向的内存空间和储存该指针的内存空间是同一块，就是储存该函数的内存空间的头部位置。但是如果用别的指针 `B` 指向了函数，那么储存 `B` 的内存空间和 `B` 指向的元素的内存空间不是同一块。

```cpp
#include <iostream>
using namespace std;
void func() { }

int main() {
    int arrow[10];
    auto p = func;
    cout << arrow << endl;
    cout << &arrow << endl;
    cout << func << endl;
    cout << &func << endl;
    cout << p << endl;
    cout << &p << endl;
}

/*
output:
0x7fff7a33d6b0
0x7fff7a33d6b0
1
1
1
0x7fff7a33d6a8
*/
```

### 函数指针的用途

函数指针主要有两个用途：做函数的参数、调用函数。

#### 函数指针调用函数

用函数指针调用函数有两种方法，都不要忘记参数列表的圆括号 `()`。

```c++
#include <iostream>
#include <string>
using namespace std;

string func() { return "I love my darling, mua!"; }

void fun() { cout << "love you too!" << endl; }

int main() {
    string zcy = (*func)(); // 这种写法更能体现指针
    cout << zcy << endl;
    auto x = fun;
    x();
}
```

#### 函数指针做函数参数

回忆来自 `<algorithm>` 的 `sort` 函数。它有两种调用方法：

```c++
sort(arr,arr + 5);
sort(arr,arr + 5, comp); // 用自定义的 comp 函数来 sort
```

第二种定义方式里，我们把 `comp` 这个函数名（**函数名也就是函数指针**）作为参数传了进去。我们知道函数名实际上储存着函数地址。所以这实际上就是传进了函数指针做参数。

用自定义的 `comp` 函数实现逆序 `sort`：

```c++
#include <algorithm>
#include <iostream>
using namespace std;

bool comp(int a, int b) { return a > b; }

int main() {
	int arr[5] = {5, 2, 3, 1, 7};
	std::sort(arr, arr + 5, comp);
	for (int x: arr) {
		cout << x << " ";
	}  
	return 0;
}

/*
output:
7 5 3 2 1
*/
```

对 `sort` 函数来说，它的第三个参数是函数指针，因为我们传入的是 `comp()` 函数的头指针，即 `comp`，而不是 `comp()`。

这个函数指针的类型是：

```c++
bool (*)(T, T);
```

这是一种叫 `Compare` 类型的特殊函数指针类型。

### 函数对象

除了自定义的 `comp`，我们也可以用 STL 提供的预定义的比较函数（需要 `#include <functional>`）。

```c++
sort(arr, arr + 5, less<int>()); // 从小到大
sort(arr, arr + 5, greater<int>()); // 从大到小
```

注意到函数对象本质上是重载了 `()`，但是 `greater<int>()` 表示的是默认构造函数，这里传入的参数是一个对象。`greater<int>()()` 的第一个括号是调用默认构造函数，第二个括号是重载 `()`。

`less<int>()`、`greater<int>()` 和 `comp` 一样，它们有 `Compare` 类型函数指针的功能，但它其实是**一个函数对象**！

以 `greater<int>()` 为例，其内部实现机制如下。

注意到，`greater` 的实现已经内置在了 `<functional>` 中，即以下代码在实际应用时不必写出。此处为了避免关键字冲突，将 `greater` 写为 `Greater`。这就好比 `std` 里的 `swap` 和自己手写的 `swap` 一样。

```c++
#include <iostream>
using namespace std;
template<class T>
class Greater {
public:
    Greater() { };
	bool operator()(const T &a, const T &b) const {
		return a > b;
	}
};
```

通过在 `public` 中完成对 `operator()` 的重载，当该模版被实例化为一个对象后，可以通过 `()` 调用该对象，看起来就像一个函数。这样的**重载了 `()`** 的对象称为“函数对象”。

调用函数对象：

```c++
#include <iostream>
using namespace std;

template<class T>
class Greater {
public:
    Greater() { };
	bool operator()(const T &a, const T &b) const {
		return a > b;
	}
};

int main() {
	auto func = Greater<int>();
	cout << func(2, 1) << endl; // true
	cout << func(1, 1) << endl;	// false
	cout << func(1, 2) << endl;	// false
	return 0;
}
```

注意，这里我们可以很**优雅**地写 `func(1,2)` 是因为之前已经把名叫 `Greater` 的类模版实例化为了一个对象。实际上，也可以不实例化而直接调用。但这时一定不要忘记 `()`，第一个 `()` 代表构造函数，第二个 `()` 才代表对括号的重载。

```c++
#include <algorithm>
#include <iostream>
#include <functional>
using namespace std;
int main() {
    cout << greater<int>()(1, 2);
    return 0;
}
```

以 `greater<int>()` 为参数调用 `sort`：

```c++
#include <functional>
#include <iostream>
#include <algorithm>
using namespace std;
int main() {
	auto func = greater<int>();
	int arr[5] = {1, 4, 2, 8, 3};
	sort(arr, arr + 5, greater<int>());
	for (int i: arr) { cout << i << ' '; }
	return 0;
}

/*
output:
8 4 3 2 1
*/ 
```

所以，`std::sort` **既可以接受函数指针，又可接受函数对象**作为第三个参数。实际上，`sort` 是一个函数模板，其模版参数是待 `sort` 的数组的类型和一个 `Compare` 类型的函数指针。调用 `sort` 的格式如下：

```c++
sort(Iterator first, Iterator last, Compare comp);
```

调用时，系统会自行推导函数模版参数，因此我们不必手工指定。

最后，关于 `sort` 函数的一个细节：`sort` 函数不仅可以用来 `sort` 数组，还可以 `sort` 任何**有序的**容器。比如 `vector`。无论是数组、`list` 还是 `vector`，`sort` 的第一个参数都应该指向第一个元素，而第二个参数则指向**最后一个元素之后**的那个位置，**而不是最后一个元素**。

当然，`vec` 里面的数据得先能 compare。

```c++
int arr[5] = {1, 3, 2, 6, 4};
vector<int> vec = {1, 3, 2, 6, 4};
sort(arr, arr + 5);
sort(vec.begin(), vec.end());
```

更具体的例子：

```c++
#include <functional>
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;
int main() {
    auto func = greater<int>();
    vector<int> vec = {1, 3, 2, 6, 4};
    sort(vec.begin(), vec.end());
    for (int i: vec) { cout << i << ' '; }
    cout << endl;
    sort(vec.begin(), vec.end(), func);
    for (int i: vec) { cout << i << ' '; }
    return 0;
}

/*
output:
1 2 3 4 6 
6 4 3 2 1
*/ 
```

## `std::function` 类

由对 `sort` 的讨论，我们发现函数指针和函数对象有高度的一致性。但另一方面，它们的形式又不统一。

在下面这个例子中，从屏幕、文件读取这两个函数不能被同一个数组统一。

```c++
#include <iostream>
#include <fstream>
#include <functional>
#include <cstring>
using namespace std;

string readFromScreen() {
    string input;
    getline(cin, input);
    return input;
}

class ReadFromFile {
public:
    string operator()() {
        string input;
        getline(ifstream("input.txt"), input);
        return input;
    }
};

int main() {
    auto readArr[] = {readFromScreen, ReadFromFile()}; // 推导失败
    return 0;
}
```

解决方法：`std::function` 类，来自 `<functional>` 头文件。

### `function` 类是一种特殊的类模版

`function` 类是一种特殊的**类模板**，模板参数是函数返回值类型和参数类型。但是特殊的是，`function` 只有成员函数，无数据成员。

#### `function` 类的实例化

`function` 类为函数指针与对象提供了统一的接口。实际上，`function` 的实例可以存储、复制和调用的不仅仅是函数指针与对象，还包括 lambda 表达式，绑定表达式和指向成员函数和指向数据成员的指针。不管采用哪种方式，只要调用形式一样（返回值类型、实参类型），我们就可以用 `function` 类型来统一。

`function` 类的实例化格式如下：

```c++
function<ret(args...)> Func;
```

其中 `ret` 是函数返回值类型，`()`中是参数类型列表，`Func` 是该 `function` 类对象的名字。

#### `function` 类的应用

上面的两个读取函数可以统一在 `function` 类中：

```c++
#include <iostream>
#include <fstream>
#include <functional>
#include <cstring>
using namespace std;

string readFromScreen() {
    string input;
    getline(cin, input);
    return input;
}

class ReadFromFile {
public:
    string operator()() {
        string input;
        getline(ifstream("input.txt"), input);
        return input;
    }
};

int main() {
	function<string()> readArr[] = {readFromScreen, ReadFromFile()};
	function<string()> readFunc;
	readFunc = readFromScreen; 
	readFunc = ReadFromFile();

	string (*readFunc2)();
	readFunc2 = readFromScreen;
	// readFunc2 = ReadFromFile(); // 不能被函数对象赋值
	return 0;
}
```

### 用 `function` 实现多态

正如 `sort` 函数所实现的那样，通过将 `function` 类型作为函数参数，可以统一函数对象和函数指针。以 `function` 类型作为函数参数的函数可以仅仅改变参数，获得不同的功能。

!!! note "例子"

    - 例：
    
    ```c++
    #include <iostream>
    #include <fstream>
    #include <functional>
    using namespace std;
    
    // 省略 readFromScreen、ReadFromFile、calculateAdd、writeToScreen
    void process(
        function<string()> read,
        function<string(string)> calculate,
        function<void(string)> write) {
        string data = read();
        string output = calculate(data);
        write(output);
    }

    int main() {
        process(readFromScreen, calculateAdd, writeToScreen);
        process(ReadFromFile(), calculateAdd, writeToScreen); // 多态
        return 0;
    }
    ```

    - 另一个例子:
    
    当 `process` 的参数类型是 `function<string()>` 时，可以同时接受 `func1`、`func2`、`func3` 作为函数参数。
    
    如果 `process` 的参数类型是 `string (*func)()`，就无法接受 `func2`、`func3`。
    
    如果 `process` 的参数类型是 `Func2` 类对象，就无法接受 `func1`、`func3`。
    
    ```c++
    #include <string>
    #include <iostream>
    #include <functional>
    using namespace std;
    
    string func1();
    class Func2 {
    public:
        string operator()();
    };
    Func2 func2;

    function<string()> func3;
    
    void process(function<string()> func) {
        string str = func();
        cout << str;
    }

    int main() {
        process(func1);
        process(func2);
        process(func3);
        return 0;
    }
    ```

STL 中大量函数用到了函数对象。以下这些函数都调用了函数指针或者函数对象作为参数。

+ `for_each`。对序列进行指定操作
+ `find_if`。找到满足条件的对象
+ `count_if`。对满足条件的对象计数
+ `binary_search`。二分查找满足条件的对象

并且也有许多预置的函数对象：

+ `less`。比较 `a < b`
+ `equal_to`。比较 `a == b`
+ `greater`。比较 `a > b`
+ `plus`。返回 `a + b`

**熟练使用函数对象**有助于实现复杂的功能。以 `for_each` 为例：

```c++
#include <iostream>
#include <algorithm>
#include <vector>

void myfunction(int& i) {
    i++;
    std::cout << i << ' ';
}

struct myclass {
    void operator()(int& i) {
        i += 2;
        std::cout << i << ' ';
    }
} myobject;

int main() {
    std::vector<int> myvector;
    myvector.push_back(10);
    myvector.push_back(20);
    myvector.push_back(30);

    std::cout << "after myfunction, now myvector contains: ";
    for_each(myvector.begin(), myvector.end(), myfunction); // 传进函数指针作为参数
    std::cout << std::endl;

    std::cout << "after myobject, now myvector contains: ";
    for_each(myvector.begin(), myvector.end(), myobject); // 传进函数对象作为参数
    // 完全等价于 for_each(myvector.begin(), myvector.end(), myclass())
    return 0;
}

/*
output:
after myfunction, now myvector contains: 11 21 31 
after myobject, now myvector contains: 13 23 33
*/
```

两次调用了 `for_each` 函数，分别对 `vector` 里面的每一个元素执行了 `myfunction` 和 `myobject`。因为这两个函数的**函数参数都是引用**，主函数中的变量也会被修改。

另一个 `count_if` 的例子：

```c++
#include <iostream>
#include <algorithm>
#include <vector>

bool IsOdd (int i) { return ((i % 2) == 1); }

int main () {
    std::vector<int> myvector;
    for (int i = 1; i < 10; i++) myvector.push_back(i); // myvector: 1 2 3 4 5 6 7 8 9

    int mycount = count_if(myvector.begin(), myvector.end(), IsOdd);
    std::cout << "myvector contains " << mycount << " odd values.\n";

    return 0;
}

/*
output:
myvector contains 5 odd values.
*/
```

`count_if` 函数需要传进一个返回值为 `bool` 类型的函数作为参数。

#### 与其他多态实现方式的对比

- 使用虚函数实现

对基类的指针或者引用，在运行时通过虚函数表确认该**指针或引用**的实际类型，并调用实际类型的重写覆盖后的函数，以实现多态。是晚绑定（运行时绑定）。

- 使用模板实现

上文中的 `greater<int>()` 就是模版实现多态的例子。通过传入不同的模版参数，自动实现重载，可以实现函数对象和函数指针的多态。是早绑定（编译期绑定）。

- 使用 `std::function` 实现

也可以支持函数指针和函数对象（通过 `function` 的多态）。是晚绑定（运行时绑定）。

#### 意义

将函数也对象化。函数可以作为参数传递，函数也可以作为变量储存，并且只要函数的参数和返回值相同，就可以被视为同一种类型的变量，不再需要模板来调用不同的函数。

## 智能指针

当两个指针 `A`、`B` 同时指向一个变量 `C` 的时候，我们希望只有 `A`、`B` 均被析构的时候，`C` 才被析构。

如何做到？引入智能指针（包含在头文件 `<memory>` 中）。

### 构造智能指针

智能指针的创建：

```c++
shared_ptr<int> p1(new int(1)); // 指向内容为 1 的一块内存
shared_ptr<int> p2; // 空指针（不初始化）
auto p3 = make_shared<int>(3); // make_shared<T>(args) 是一个函数，返回值是一个 share_ptr
int a;
shared_ptr<int>p4(a); // 用已有对象创建智能指针，p4 指向 a
```

让两个指针指向同一个位置：

```c++
#include <iostream>
#include <memory>
using namespace std;

class Myclass {
public:
    int myint;
    Myclass(int i): myint(i) { };
};

int main() {
    shared_ptr<Myclass> p2 = make_shared<Myclass>(2);
    shared_ptr<Myclass> p3 = p2; // p2 和 p3 指向同一块内存
    // p2、p3 都是智能指针，指向同一块内存。
    // auto p3(p2) 和上面的句子等价
    shared_ptr<Myclass> p4;
    p4 = p3; // 对已有的智能指针，也可以直接进行赋值
             // 如果 p4 原本指向某个内存空间，该内存空间的所有智能指针的引用计数减一
             // 然后 p3 指向空间的所有智能指针引用计数加一
             // 此处 p4 构造之后是个空指针，所以只会给 p3 的内存空间引用计数加一
    cout << p2 << endl;
    cout << p3 << endl;
    cout << p4 << endl;
} 

/*
output:
0x14b0e80
0x14b0e80
0x14b0e80
*/
```

### 引用计数 `use_count()`

用 `obj.use_count()` 函数，可以得到智能指针 `obj` 此刻指向的物体（包括 `obj` 自己在内）共有几个智能指针指向它。

- 代码示例：

```c++
#include <memory>
#include <iostream>
using namespace std;
int main() {
	shared_ptr<int> p1(new int(4));
	cout << p1.use_count() << ' '; // 1
	{
		shared_ptr<int> p2 = p1;
		cout << p1.use_count() << ' '; // 2
		cout << p2.use_count() << ' '; // 2
	} // p2 出作用域
	cout << p1.use_count() << ' '; // 1
}
```

如果函数参数是智能指针类型，则在调用函数时，由于调用了拷贝构造构造智能指针作为参数，新构造出的智能指针指向同一对象，会发生 `count` 增加。如果想要避免，可以将函数参数类型改为智能指针的引用。

不用考虑的太复杂，**引用传参本来就不发生构造和析构**。

```c++
#include <memory>
#include <iostream>
using namespace std;
void f1(shared_ptr<int> p1) { // 拷贝构造了智能指针作为参数
    cout << p1.use_count(); // 此时 count 为 2
}

void f2(shared_ptr<int> &p1) { // 参数是智能指针引用
    cout << p1.use_count(); // 此时 count 仍为 1
}

int main() {
    shared_ptr<int> p1(new int); // 此时 count 为 1
    f1(p1);
    cout << p1.use_count(); // 出 f1 函数体时，参数被析构，count 回归到 1
    f2(p1);
    cout << p1.use_count();
	return 0;
}

/*
output:
2111
*/
```

### 智能指针的实现原理

智能指针指向的是辅助指针 `Uptr`，辅助指针再指向真正指向数据存放位置的指针 `p`。

辅助指针 `Uptr` 有两个功能：一方面，它指向真正指向数据存放位置的指针 `p`；另一方面，它具有成员 `count`，记录有几个智能指针指向自己。每个指向“真正的”数据的指针 `p` 都只被一个辅助指针指向，`p` 和 `Uptr` 一一对应。

基于上图原理给出以下结论：

- 不能直接用智能指针给普通指针赋值，因为普通指针没有辅助指针。
- 一个普通指针最多给一个智能指针赋值，否则会出现多个辅助指针。
- 智能指针之间可以相互赋值，共同指向辅助指针即可。

```c++
#include <memory>
#include <iostream>
using namespace std;
int main() {
	shared_ptr<int> p1(new int(4));
	cout << p1.use_count() << ' '; // 1
	int *normal = p1; // 我们希望 normal 可以和 p1 指向相同的内存
	return 0;
} 

/*
output:
.code.tio.cpp:8:7: error: no viable conversion from 'shared_ptr<int>' to 'int *'
        int *normal=p1;
            ^      ~~ 
*/
```

那如果想让普通指针指向智能指针真正指向的位置该怎么办呢？利用 `p.get()` 函数！`p.get()` 返回智能指针 `p` 真正指向的数据的**裸指针**。

```c++
int *normal = p1.get();
```

更具体的示例：

```c++
#include <memory>
#include <iostream>
using namespace std;

int main() {
    int *pi = new int(2);
    shared_ptr<int> ptr(pi); // 用已有对象初始化
    cout << "ptr now points to ";
    cout << ptr.get() << endl;
    int *p = ptr.get();
    cout << "p now points to ";
    cout << p << endl;
    cout << "But the count of ptr is " << ptr.use_count();
    return 0;
}

/*
output:
ptr now points to 0x1557e70
p now points to 0x1557e70
But the count of ptr is 1
*/
```

上面的例子说明， 虽然理论上可以这么做，但因为普通指针不和辅助指针发生关系，无法增加 `count` 的数量。因此一般**不能混合使用普通指针和智能指针**。如果我们希望有一个指向该物体的指针，并且不被记入 `count`，考虑到安全性，可以使用 `weak_ptr` 代替普通指针。

### 智能指针的初始化

可以用智能指针初始化另一个智能指针：

```c++
shared_ptr<int> ptr2(new int(3));
shared_ptr<int> ptr3(ptr2);
```

但是不能使用同一裸指针初始化多个智能指针（这样会出现多个辅助指针）。

下面这个例子是错的。

```c++
int* p = new int(); 
shared_ptr<int> p1(p);
shared_ptr<int> p2(p); 
```

### 智能指针的析构

智能指针析构的时候，其内部的成员辅助指针并不一定会被析构，因为还可能有其他智能指针指向该辅助指针。在智能指针析构的时候时，其辅助指针内记录智能指针数量的成员 `count` 发生 `count--`。

只有 `count--` 后为 `0` 的时候，辅助指针才会被同时析构。

### 智能指针的其他操作

```c++
p.get(); // 获取裸指针，即获得实际指针的情况
p.reset(); // 清除指针并减少引用计数，见下
shared_ptr<myType> q = static_pointer_cast<myType>(p); // 不做类型检查，直接把一个其他类类智能指针转化为 myType 类智能指针
shared_ptr<Base> q = dynamic_pointer_cast<Base>(p); // 把一个其他类智能指针转化为 Base 类智能指针，还做类型检查
```

其中，`dynamic_pointer_cast` 和 `static_pointer_cast` 所遵循的规则和我们在多态与模板中学过的 `dynamic_cast`、`static_cast` 相同。**但是其参数和返回值都是 `shared_ptr`**。

**这里对 `dynamic_cast` 做一个说明**：

- `T2* pObj = dynamic_cast<T2*>(obj_p)` 这一个语句里会出现两个 `T2`，第一个是 `pObj` 的类型，第二个是 `obj_p` 的转换目标。
- `dynamic_cast` 一般用来做向下类型转换，因为向上类型转换直接赋值就可以了。
- `dynamic_pointer_cast` 参数都是智能指针。

```c++
shared_ptr<Base> q = dynamic_pointer_cast<Base>(p); // 该 dynamic_cast 作用类似于下面的语句，但是结果是个 shared_ptr
Base* q = dynamic_cast<Base*>(p.get());
```

备注：指向同一块内存空间的所有 `shared_ptr`，**不论指针本身的类型**，都会给 `use_count++`。

`reset` 函数：将 `p` 指向另外一个对象。当 `()` 为空的时候，`p` 指向 `null`，当使用 `reset` 函数时，原来 `p` 指向的对象的 `count` 会减一。

`reset` 和直接对指针赋值的区别：`reset` 的参数是一个对象，但是直接对指针赋值的参数是一个地址或者别的指针。

```c++
#include <iostream>
#include <memory>
using namespace std;

int main() {
    shared_ptr<int> sp(new int(1)) ;
    cout << "the address is " << sp << '\n';
    sp.reset(new int(2));
    cout << "the address is " << sp << '\n';
    sp.reset();
    cout << "the address is " << sp << '\n';
    return 0;
}

/*
output:
the address is 0x17e1e70
the address is 0x17e1eb0
the address is 0
*/
```

### 智能指针的问题

```c++
#include <memory>
#include <iostream>
using namespace std;

class Child; // 先声明，后定义
class Parent {
    shared_ptr<Child> child;
    public:
    Parent() { cout << "parent constructing" << endl; }
    ~Parent() { cout << "parent destructing" << endl; }
    void setChild(shared_ptr<Child> c) {
        child = c;
    }
};

class Child {
    shared_ptr<Parent> parent;
public:
    Child() { cout << "child constructing" << endl; }
    ~Child() { cout << "child destructing" << endl; }
    void setParent(shared_ptr<Parent> p) {
        parent = p;
    }
};

void test() {
    shared_ptr<Parent> p(new Parent());
    shared_ptr<Child> c(new Child());
    p->setChild(c);
    c->setParent(p);
	// p 和 c 被销毁
}

int main() {
    test();
    return 0;
}

/*
output:
parent constructing
child constructing			
*/
```

`Parent` 和 `Child` 两个对象中的成员分别是指向对方的智能指针，导致 `p`、`c` 被析构之后，`Parent` 和 `Child` 所在内存没有被析构。

解决方法：`weak_ptr`。

### 弱引用 `weak_ptr`

弱引用指针指向对象的时候，不会被 `count` 计数。`weak_ptr` 必须由 `shared_ptr` 构造，但是不会计数，**相当于一个能够被智能指针赋值的“普通指针”**。

#### 构造弱引用指针

```c++
shared_ptr<int> sp(new int(3));
weak_ptr<int> wp1 = sp; // weak_ptr 必须由 shared_ptr 构造
```

#### 弱引用指针的操作

```c++
wp.use_count(); // 获取引用计数，虽然弱引用指针不被 count 计数，但仍然可以获取引用次数 count，count 值为指向同一个对象的智能指针的数量。
wp.reset(); // 清除指针
wp.expired(); // 当弱引用指针的 count == 0 时，失效，返回 true。否则返回 false
sp = wp.lock(); // 从弱引用 wp 获得一个智能指针 sp，该智能指针会增加 count
```

### 独享所有权 `unique_ptr`

`unique_ptr`：每个对象只能由一个 `unique_ptr` 指向它。该对象不能被其他 `shared_ptr`、`weak_ptr` 指向。

**某个时刻只能有一个 `unique_ptr` 指向一个给定对象**，由于一个 `unique_ptr`“拥有”它指向的对象，因此 `unique_ptr` 不支持普通的拷贝或赋值操作。

注意到 `unique` 只是和 `shared` 与 `weak` 不能共享，**但是仍然可以和普通指针和引用共享**。

```c++
#include <memory>
#include <utility>
#include <iostream>
using namespace std;
int main() {
    int a = 1;
    auto u4 = make_unique<int>(a); // 创建方式在之后讲述
    int& b = a;
    int* x = &a;
    cout << *x << endl;
    cout << b << endl;
    cout << *u4 << endl;
    return 0;
}

/*
output:
1
1
1
*/
```

#### `unique_ptr` 的创建

```c++
unique_ptr<int> u1; // 空指针
unique_ptr<int> u2(new int(3)); // 新分配一块内存给 unique_ptr
int a = 1;
unique_ptr<int> u3(a); // 用已有对象 a 创建 unique_ptr
auto u4 = std::make_unique<int>(20); // 也是新分配一块内存给 unique_ptr
                                     // make_unique 函数自动返回 unique_ptr 类型的返回值
                                     // 供编译器推导 u4 的类型
```

和 `weak_ptr` 不同，`unique_ptr` 不需要和 `shared_ptr` 共同使用。实际上，`unique_ptr` 不能和 `shared_ptr` 同时指向一个对象，也不能用 `shared_ptr` 构造 `unique_ptr`。

下面这个例子就是错的。

```c++
shared_ptr<int> sp(new int(1));
unique_ptr<int> up(sp);
```

**先给出结论**，`unique_ptr` 相比于普通指针具有如下的特点：

+ **不可用 `unique_ptr` 给其他指针赋值**

```c++
int* p = new int(20);
int& b = *p; // 可以
auto p = make_unique<int>(20);
int* g = *p; // 不可以
```

+ **不可以输出**

```c++
auto p = make_unique<int>(20);
cout << p << endl; // 不可以
```

+ **不可以 `delete`**

```c++
auto p = make_unique<int>(20);
delete p;

/*
output:
1152177172/source.cpp: In function 'int main()':
1152177172/source.cpp:7:12: error: type 'class std::unique_ptr<int, std::default_delete<int> >' argument given to 'delete', expected pointer
     delete p;
            ^
*/
```

#### `unique_ptr` 的操作

```c++
unique_ptr<int> u1; // 创建空 unique_ptr
u1 = nullptr; // 此时会释放 u1 指向的对象的权限，但是对象不会被析构
u1.release(); // 这个函数的返回值是一个指向该对象的普通指针，此时 u1 指针被破坏，具体见下
u1.reset(); // 在改变 u1 指向的同时，会释放 u1 指向的对象
```

对于 `release` 的说明（这些说明都是基于 `retexter`，我发现 `glot` 根本不支持 `make_unique`，而 `tio` 不会报错。再次体现了三个平台都很有用，都得用惯）：

```c++
#include <memory>
#include <utility>
#include <iostream>
using namespace std;
int main() {
    int a = 1;
    auto u4 = make_unique<int>();
    cout << *u4 << endl;
    cout << "====================" << endl;
    u4.release();
    // cout << *u4 << endl;
    return 0;
}

/*
output:
0
====================
*/
```

**如果我们把第 11 行的注释去掉，试图打印出 `release` 后的 `unique_ptr`**。

```c++
#include <memory>
#include <utility>
#include <iostream>
using namespace std;
int main() {
    int a = 1;
    auto u4 = make_unique<int>();
    cout << *u4 << endl;
    cout << "====================" << endl;
    u4.release();
    cout << a << endl;
    cout << *u4 << endl;
    return 0;
}

/*
output:
Invalid memory reference (SIGSEGV)
0
====================
1
*/
```

可以见得，最后那个输出语句一定是非法的，**`release` 相当于破坏了 `u4`**，返回了一个裸指针，`u4` 将无法被复用。**值得注意**的是，如果我们交换 11、12 行，先打印已经被 `release` 的 `u4` 再打印 `a`，无法把 `a` 打印出来，因为当我们试图打印 `*u4` 的时候，程序就结束了，因而输出是这样：

```c++
#include <memory>
#include <utility>
#include <iostream>
using namespace std;
int main() {
    int a = 1;
    auto u4 = make_unique<int>();
    cout << *u4 << endl;
    cout << "====================" << endl;
    u4.release();
    cout << *u4 << endl;
    cout << a << endl;
    return 0;
}

/*
output:
Invalid memory reference (SIGSEGV)
0
====================
*/
```

#### 将一个对象的所有权在两个 `unique_ptr` 之间转换的方法

```c++
// 将 p1 指向的对象转移给 p2
unique_ptr<int> p1(new int (1));
unique_ptr<int> p2(p1.release()); // release 将 p1 置为空，并且返回裸指针


// 将 p3 指向的对象转移给 p2
unique_ptr<int> p3(new int(3));
p2.reset(p3.release()); // reset 释放了 p2 原来指向的内存
```

`release` 成员返回 `unique_ptr` 当前保存的指针并将其置为空。因此，`p2` 被初始化为 `p1` 原来保存的指针，而 `p1` 被置为空。

`reset` 成员接受一个可选的指针参数，令 `unique_ptr` 重新指向给定的指针。

调用 `release` 会切断 `unique_ptr` 和它原来管理的的对象间的联系。`release` 返回的指针通常被用来初始化另一个智能指针或给另一个智能指针赋值。

```c++
// 注意这里非常神奇，我们在之前给的构造方法中，要么是用构造新指针指向某个对象，比如：
int a = 20;
unique_ptr<int> p2(a);
// 要么是先构造新指针，再用别的指针给他赋值，比如
unique_ptr<int> u1; // 空指针
int a = 20;
u1 = &a;
```

例子：

```c++
#include <memory>
#include <utility>
using namespace std;
int main() {
    auto up1 = std::make_unique<int>(20);
    // unique_ptr<int> up2 = up1; // 错误，不能复制 unique 指针
    unique_ptr<int> up2 = std::move(up1); // 可以移动 unique 指针
    int* p = up2.release(); // 放弃指针控制权，返回裸指针
    delete p;
    return 0;
}
```

### 总结

智能指针可以帮助管理内存，避免内存泄露。在手动维护指针不可行、复制对象开销太大时，智能指针是唯一选择。

缺点：

+ 引用计数会影响性能

+ 智能指针不总是智能，需要了解内部原理

+ 需要小心环状结构和数组指针

