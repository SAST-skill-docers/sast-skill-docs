## JavaScript 的异步

!!! note "异步的必要性"

    正常我们编写的代码都是同步代码，也就是说代码执行的顺序是从上到下，上面的操作还没有执行完，下面的操作就不会被执行到。但是在前端代码之中，我们会经常向后端或者网络请求数据，即使现在网络已经很快，在请求数据量较大的时候，请求数据的操作依然会占据相当长的时间。如果我们还依然使用同步代码，那么就会导致页面卡顿（因为这个时候代码卡顿在了请求数据这一步）。

    另外一方面，有些操作完全可以在前端请求页面的时候完成，比如说渲染出页面的模板。我们可以在数据完全获取之后再把这些数据填入页面。

    总而言之，前端需要使用异步代码，需要允许多段代码同时执行而非严格按照顺序一步步走。这种允许多段代码同时执行的代码就是**异步（英语：Asynchronous）**。

### 事件循环与消息队列机制

首先我们需要明确，JavaScript 是单线程语言，这也就代表 JavaScript 并不能像 C++ 或者 Java 那样实现多线程并发的异步。

!!! note "为何禁用多线程"

    JavaScript 的设计初衷就是浏览器的脚本语言，其作用就是修改文档树。如果 JavaScript 允许多线程，那么其很有可能因为多线程并发修改文档树导致冲突。

但 JavaScript 使用了另外一种方式实现异步，也就是事件循环和消息队列机制。

在详细叙述这个机制之前，我们首先需要重新认识一下回调函数。当主线程派遣出一个异步过程，比如说触发了网络请求，一般而言需要设定一个这个异步过程的回调函数。这个回调函数描述的，是这个异步过程执行完毕后需要做的事情。

需要定义这个回调函数的原因也很简单，主线程没有办法了解到异步过程何时才能结束，甚至并不清楚这个过程能否结束。在这样的情况下，主线程完全不能知晓什么时候做这个异步过程的善后工作（比如说从网络获取数据后，主线程应该把这些数据加载到页面上）。那么不如定义一个回调函数，具体什么时候调用，根据异步过程来确定。

回到事件循环和消息队列。JavaScript 的主线程上会有一个死循环，称为**事件循环**。其执行逻辑为在空闲的时候不断检查消息队列是否有消息，如果有，则执行，如果没有，则等待。而空闲的含义是当前函数栈空。

而需要异步执行的代码则会负载一个回调函数，当异步过程执行完毕后，则会把回调函数放入消息队列末尾。当主线程空闲且检测到消息队列之中有回调函数的时候，主线程就会执行回调函数，做好异步过程的善后工作。

通过这样的机制，JavaScript 依然是单线程的，对文档树的操作依然是同步的、不会出现冲突的。但依靠这个机制，JavaScript 实现了异步。

!!! note "宏消息队列和微消息队列"

    实际上 JavaScript 的消息队列有两种，分为宏消息队列和微消息队列。`setTimeout, setInterval` 这类引发的回调会放入宏消息队列，而 `Promise` 的 `then` 会放入微消息队列。

    JavaScript 的事件循环在查看消息队列的时候，会首先查看微消息队列，如果存在回调则**执行每一个回调直到微消息队列清空**。之后再去查看宏消息队列，并且**只执行队列第一个回调**。

现在我们编写一个异步的代码。这里会用到 `setTimeout` 函数，其接受两个参数，第一个参数为一个回调函数，第二个参数为多长时间后执行上述回调函数。比如下列代码：

```javascript
setTimeout(() => {
    console.log("1s has passed!");
}, 1000);
```

这个代码将会延迟一秒后输出 `"1s has passed!"`。这个函数的等待过程会异步于主线程执行，而其通过第一个参数接受的回调就会在等待完毕后放入消息队列。现在我们利用这个函数写出一个需要耗时的操作，以此模拟网络请求：

```javascript
const fetchData = () => {
    setTimeout(() => {
        console.log("Data got!");
    }, 1000);
};
```

这个函数调用后会立刻返回，因为它的任务是派遣一个异步过程，而不负责等待异步结束和善后，善后工作应该写在回调函数之中等待主线程执行。

在等待异步的过程中，主线程可以完成其他的任务：

```javascript
const fetchData = () => {
    setTimeout(() => {
        console.log("Data get!");
    }, 1000);
};

fetchData(); // Dispatch async task
console.log("Rendering template...");
console.log("Loading local storage..."); // Main thread doing other tasks
```

### 回调函数的缺陷

回调函数不需要引入很多的其他语法就可以方便地使用到异步之中，但是其问题也是很突出的。回调函数本身可读性就不是很好，而且也并不能很好贴合我们的直观思维逻辑。而其最大的问题就是可能造成回调函数过分嵌套，导致代码难以维护。这一般被称为**回调地狱**。

真正的异步业务逻辑可能并不会很单一，比如说前端要从多个数据源加载数据，但是后一个数据的加载需要依靠前一个数据的结果。这样就不能在主线程中同时派遣多个异步过程，而必须在前一个异步过程的回调之中派遣下一个异步过程。这里我们依然用 `setTimeout` 来模拟耗时操作：

```javascript
setTimeout(() => { // Get data #1
    console.log("Data #1 get!");
    setTimeout(() => { // Get data #2
        console.log("Data #2 get!");
        setTimeout(() => { // Get data #3
            console.log("Data #3 get!");
            setTimeout(() => { // Get data #4
                console.log("Data #4 get!");
            }, 1000);
        }, 1000);
    }, 1000);
}, 1000);
```

这样的代码很难阅读，也很难维护，而实际上的业务逻辑也不可能是 `setTimeout` 这样简单。另一方面，我们还需要处理异步异常的情况，如果发生错误，就应当立刻跳出异步，交由主线程做异常处理。而显然，我们很难给这样的代码添加异常处理功能。

### `Promise` 对象

为了解决回调函数不直观的问题，新版本的 JavaScript 设立了 `Promise` 对象，可以说这个对象完全改变了 JavaScript 异步代码编写的规范。

创建一个 `Promise` 对象可以使用其构造函数：

```javascript
new Promise((resolve, reject) => {
    let asyncSucceeded = Math.random() > 0.2;
    setTimeout(() => {
        if (asyncSucceeded) {
            resolve("Async succeeded!");
        } else {
            reject("Async failed!");
        }
    }, 1000);
});
```

其构造函数接受一个回调函数，这个回调函数的两个参数是另外的两个回调函数。`resolve` 代表异步成功的回调函数，`reject` 代表异步失败的回调函数。而构造函数接受的回调函数的函数体则是需要执行的耗时操作。

!!! note "为什么要叫这个名字，它的构造函数为什么长成这样"

    Promise 的中文一般是“承诺”，那异步和承诺究竟有什么关系？

    我们来设想这样的一个情景。小明是一位五年级的小学生，他的妈妈为了让他好好学习，**承诺**小明如果他期末考试考到 100 分，就给他买新手机。而等待期末考试出成绩，就是异步过程。在这段时间内，作为主线程的小明的妈妈可以去做其他的工作，而负责执行异步过程的小明则要好好学习。

    等到异步过程执行完毕，也就是期末考试出成绩了，就到了**承诺**兑现的时候了。小明和妈妈就会核对小明的学习成果（异步过程的执行状态），如果小明考到了 100 分（异步过程执行成功），作为主线程的小明的妈妈就会去买新手机。而如果小明没考到 100 分（异步过程执行失败），小明的妈妈就不会买新手机。无论买不买，作为主线程的小明的妈妈都做好了异步过程的善后工作。

    换到程序设计的情景。一个**承诺**要成立，就需要规定好，异步过程执行完毕，主线程需要做什么，其实这就是指派异步过程的回调函数。在 `Promise` 的构造函数之中，`resolve` 实际上就是“考到 100 分，小明的妈妈要买新手机”，`reject` 实际上就是“考不到 100 分，小明的妈妈不买新手机”，函数体就是“小明努力学习”。

    但是构造函数之中虽然有了 `resolve` 和 `reject`，但实际上我们还没有具体规定这两个回调到底是啥。而指定这两个回调需要调用 `Promise` 的 `then` 和 `catch` 方法。这个我们在下面讲解。

给 `resolve` 和 `reject` 指定具体的函数体需要使用 `then` 和 `catch` 方法。这两个方法都会接受回调函数，并且返回 `Promise` 对象：

```javascript
new Promise((resolve, reject) => {
    let asyncSucceeded = Math.random() > 0.2;
    setTimeout(() => {
        if (asyncSucceeded) {
            resolve("Async succeeded!");
        } else {
            reject("Async failed!");
        }
    }, 1000);
})
    .then((res) => {
        console.log("Then");
        console.log(res);
    })
    .catch((err) => {
        console.log("Catch");
        console.log(err);
    });
```

通过这样的方式，我们就在 `Promise` 构造函数接受的回调函数的函数体中规定了异步过程的具体任务，在 `then` 和 `catch` 方法之中指派了异步过程的回调函数。这样，主线程就完整完成了异步过程的派遣。

`Promise` 比直接写回调模式优越的地方就是这种写法解决了回调地狱。我们依然考虑依次获取多数据源数据的业务场景，由于 `then` 方法依然返回 `Promise` 对象，所以实际上我们可以在 `then` 后面接着调用 `then`。而上一个 `then` 之中的回调函数的返回值会被传入下一个 `then` 之中的回调函数的第一个参数：

```javascript
new Promise((resolve, reject) => {
    // Get Data #1
    resolve("Data #1");
})
    .then((res) => {
        console.log(res);
        // Get Data #2
        return "Data #2";
    })
    .then((res) => {
        console.log(res);
        // Get Data #3
        return "Data #3";
    })
    .then(console.log)
    .catch(console.log);
```

这种写法被称为 `then` 链，其好处是比回调函数写法更易读且易维护。

另外，对于多依赖问题（需要多个异步全部结束后才能派遣下一个异步），`Promise` 提供了 `all` 方法：

```javascript
Promise.all(
    [1, 2, 3].map((val) => {
        return new Promise((resolve, reject) => {
            resolve(`Promise #${val}`);
        });
    })
)
    .then((res) => {
        console.log(res); // ["Promise #1", "Promise #2", "Promise #3"]
    })
    .catch((err) => {});
```

这里 `then` 之中的回调函数接受的参数是所有 `Promise` 对象 `resolve` 回调所接受的参数拼成的数组，`catch` 同理。

---

另外，注意两点：

- `Promise` 一旦创建，内部的异步过程就开始执行，且开始执行后就不会取消
- 创建 `Promise` 会写很多很长的回调，写完这些回调后很容易给编写者一种“异步已经执行结束”的错觉。实际上这么长的语句仅仅是**定义了 `Promise` 对象**，也就是仅仅只是派遣了异步。

关于第二点，可以看下面的代码：

```javascript
let i = 1;

new Promise((resolve, reject) => {
    resolve(2);
})
    .then((res) => {
        i = 2;
    })
    .catch(console.log);

console.log(i); // 1
```

这里创建完 `Promise` 对象之后立刻输出 `i`，由于此时异步还没有执行完毕，也就是还没有将 `i` 重新赋值，所以只能得到原先值 `1`。

!!! caution "编者曾经踩过的坑不能让读者再踩"

    在实际工程中，创建 `Promise` 对象的语句可以比这个示例长很多，甚至可以超出一个屏幕显示的范围。此时一定要保持清醒，这么长的语句，**仅仅是派遣了异步**，不要紧接着后面使用一些会被异步过程修改的值。

    跟着我念，**再长的语句也仅仅是派遣了异步**，**再长的语句也仅仅是派遣了异步**，**再长的语句也仅仅是派遣了异步**。

    此外，实际工程之中，一般会设计一个标记表示某个异步是否还在执行。这个标记会在创建 `Promise` 对象派遣异步的时候置真，然后在 `then` 链最后的回调中置假。这样主线程就可以根据这个标记确定是否可以使用一些敏感变量（比如上面例子中的 `i`）。

    这种思想其实类似于读写锁的思想。

### `async` 与 `await`

在最新标准之中，JavaScript 引入了 `async, await` 这两个关键字，这两个关键字的作用是能够让异步代码写得和同步代码一样自然。

我们可以用 `async` 关键字将一个函数声明为异步函数。调用异步函数的时候，其会立刻返回并派遣一个异步：

```javascript
const foo = async () => {
    setTimeout(() => {
        console.log("Async over!");
    }, 1000);
};

foo();
console.log("Sync code here!");
```

而 `async` 关键字的的另外一个作用就是将函数的返回值包装为 `Promise` 对象：

```javascript
const foo = async () => {
    setTimeout(() => {
        console.log("Async over!");
    }, 1000);
};

typeof foo(); // "object", note "undefined"
```

那么我们也可以按照 `Promise` 的 `then` 链写法使用异步函数：

```javascript
const foo = async () => {
    return "Data #1";
};

foo()
    .then((res) => {
        console.log(res);
        return "Data #2";
    })
    .then(console.log)
    .catch(console.log);
```

`await` 关键字后面可以接一个变量，如果这个变量不是 `Promise` 对象，那么 `await` 关键字不产生任何效果。

如果是 `Promise` 对象，那么 `await` 关键字会阻塞代码运行，直到这个 `Promise` 对象代表的异步执行完毕。

如果异步成功，这个时候 `await` 语句的返回值是 `Promise` 的 `resolve` 回调接受的参数，无论有没有通过 `then` 方法指定 `resolve` 回调。

如果异步失败，首先确定这个 `Promise` 有没有通过 `catch` 方法规定 `reject` 回调。如果有，`await` 返回 `catch` 之中回调函数的返回值，如果没有，抛出 Uncaught Failure 错误。

比如说：

```javascript
let a = await 1;
a; // 1

let b = await new Promise((resolve, reject) => {
    let succeeded = Math.random() > 0.2;
    if (succeeded) resolve("Success");
    else reject("Failure");
})
    .catch((err) => "Error " + err);
b; // Maybe "Success", maybe "Error Failure"
```

`async, await` 关键字实际上是针对 `then` 链可能过长导致可读性降低的问题而提出的。我们现在还是考虑依次从多个数据源获取数据的问题，事实上通过 `async, await` 关键字，这个异步过程甚至可以写得很像同步代码：

```javascript
const fetchData = async () => {
    let data_1 = await fetchData_1(initData);
    let data_2 = await fetchData_2(data_1);
    let data_3 = await fetchData_3(data_2);
    return [data_1, data_2, data_3];
}
```

这里的 `fetchData` 系列函数均是异步函数。

可以发现这种写法和同步代码几乎没有什么差别，可读性比 `then` 链好一些，更是比回调函数写法好很多。

另一方面，其解决了不定长 `then` 链的问题。如果要依次拉取数据的信息源个数不是固定的，而是通过变量指定，那么 `then` 链就不可用了，因为我们不知道具体要写多少个 `then`。但是通过 `async, await` 结合 `for` 循环就可以解决问题。

---

但是 `async, await` 带来的争议也不小，最大的危险是异步传染。

`await` 关键字的使用有一个要求，也就是如果在函数中使用，那么这个函数必须是异步的。原因也是好理解的，因为 `await` 会阻塞代码运行，如果在主线程上阻塞，则会导致页面卡顿，这本来就和异步的初衷矛盾。

那么进一步，调用了这个函数的外层函数中的语句为了防止异步可能带来的敏感变量问题（和 `Promise` 问题类似，异步函数如果修改了某一个变量，这个变量很有可能不会被及时修改），外层函数也很有可能给这个函数加上 `await`，这又会导致外层函数不可抗力地变为异步函数。这种传染会导致整片函数变为异步，从而让逻辑变得混乱。

另外，`async, await` 关键字会降低我们对阻塞的敏感，从而编写出不必要的阻塞和串行代码。实际上，`async, await` 只是让**依次**拉取信息的代码变得简洁，对于本就可以多并发的信息拉取，反而会有让我们写出不必要的串行代码的可能，从而降低代码效率：

```javascript
const fetchData = async () => {
    let data_1 = await fetchData_1();
    let data_2 = await fetchData_2();
}
```

如果 `data_1, data_2` 本就可以并发获取，这样的写法反而会降低效率，因为这样代码的实际含义是串行地获取数据。真正的写法应该是：

```javascript
const fetchData = async () => {
    let data_1_promise = fetchData_1();
    let data_2_promise = fetchData_2();
    let data_1 = await data_1_promise;
    let data_2 = await data_2_promise;
}
```

只有先获取两个异步的 `Promise` 对象，同时派遣出两个异步后后分别 `await` 才能实现并发。

但是 `async, await` 的目的本来就是用于隐藏掉 `Promise` 的存在，将异步代码写成同步的格式，这样做其实还不如直接把 `Promise` 写出来。

此外，对于多依赖问题，`async, await` 关键字并没有解决，因为 `await` 一次只能等待一个异步。这个时候还是不可避免地需要使用 `Promise` 对象。

!!! note "该如何写好异步代码"

    现代的 JavaScript 已经将异步操作封装得足够易用，借助 `Promise, async, await` 就可以写出很明晰地异步代码。

    但是我们在编写的时候一定需要注意自己写的不是同步代码而是异步代码，以免出现问题。此外，应当根据实际应用要求合理选择直接使用 `Promise` 对象还是 `async, await` 关键字。

    实际上文档能教给读者的很有限，编者还是希望大家多去编写真实的工程代码以真正掌握 JavaScript 异步。
