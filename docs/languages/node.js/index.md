# Node.js 基础

## 前置知识

- CLI 基本常识
- 对 JavaScript 的基本了解
- 部分内容涉及 TypeScript 的基本信息

## 什么是 Node.js

根据前面一些文档的内容，我们大概了解到 JavaScript 是一种前端语言，可以赋予静态的网页丰富的动态效果，换句话说，JavaScript 运行在浏览器中，能够访问的只有一个页面上的内容，以及浏览器提供的 API。Node.js 基于 V8 引擎和一些系统库，创建了一个 JavaScript 的运行时环境，使得 JavaScript 能够独立于浏览器在系统上直接运行，并且访问系统的多种接口。

V8 引擎的高效使得基于 Node.js 的程序可以有极高的运行效率，同时 JavaScript 的一些特性在编写部分服务端程序时有独特的好处。

Node.js 的运行机制与浏览器中极为相似，主要的区别体现在于运行环境和所能访问的 API 不同，因此以下将尽量**不会**涉及语法细节。

## 安装

!!! info "Node.js 版本的选择"

    Node.js 有多种版本可以选择，目前（2021.12.16）的长期支持（LTS）版本为 v16(.13.1)，上一个版本则为 v14，不同的版本在支持 JavaScript（或者说 ECMAScript）新特性方面有所区别，遇到具体问题时可以使用 `nvm` 来解决，详见资源链接。

访问 [Node.js官方网站](https://nodejs.org/zh-cn/) 可以获取各平台下载地址，也可以 [通过包管理器安装](https://nodejs.org/zh-cn/download/package-manager/)。

在使用包管理器安装时请注意，上述网页给出的命令中可能包含 `npm` 即 `Node.js package manager` 是 Node.js 默认的包管理器，如果此处没有安装也没有关系，我们会在下节中详细解释这个问题。

### Node.js 运行

Node.js 的运行与 Python 十分相似，可以使用 REPL（英文：read-eval-print-loop，即交互式命令行）或者脚本的方式，甚至还有人搞过对标 IPython 的 IJavaScript ~~（不过凉了）~~。

- REPL：在命令行输入 `node` 就可以打开一个交互式界面，就如同你在学习 JavaScript 时使用浏览器调试工具那样，但是 Node.js 并不能像在浏览器中那样对打印出来的对象做复杂的操作，当然考虑到这是在 CLI 中，缺少类似的操作并不奇怪。
  - 与浏览器不同的另一点是，你可以使用类似 `.exit` 的语句进行一些非 JavaScript 的操作，如退出 Node.js（当然也可以使用两次 `Ctrl+C` 或者一次 `Ctrl+D` 来退出）。
- 脚本：与 Python 类似，使用 `node xxx.js` 来执行一个脚本（但是实际操作中这个行为也会被封装，同样会在下一节中解释）。

## Node.js 模块化

在之前的 JavaScript 基础章节中并没有提及 JavaScript 有什么模块化或者包管理的东西，这是因为在前端开发中，要么将用到的模块全部打包（关于打包将在后续提及），要么通过 CDN 引入，前者通过 Node.js 实现，而后者则直接将导出的模块挂在在 `window` 下，也即成为全局变量，这也就是早期 JavaScript 的问题，通过全局变量解决一切问题，大家在程设中想必~~这么做过~~知道这样做是不好的。

这个问题早在 ECMA 制定 ES6 标准引入 ES6 Module 前就广泛存在了，因此社区早已提出了多种解决方案，主要有 CommonJS、AMD、CMD 几种。后两种现在已经很少出现，而 CommonJS 的主要实践者正是 Node.js。因此当运行 Node.js 项目时，如果在版本低于 ES6 的情况下使用 ES6 Module（即使用 `import`、`export`）方法就会报错。不过好在我们可以选择使用 ES6 及以上版本，或者使用 Babel（见资源链接）实现兼容，因此我们将主要使用 ES6 Module 来管理模块。

### ES6 Module

从一个文件中导出变量、函数、类型等内容的方法为：

```javascript
// 文件名 export.js
// 导出值 value
export { value };
// 导出值 value 并重命名为 aliasNameOfValue
export { value as aliasNameOfValue };
// 以下两句等价
export { value as default };
export default value;

// 对于 TypeScript，可以通过以下方式导出类型
export type { type };
export { type };
```

引入模块的方式则为：

```javascript
// 注意这里并不需要指定后缀名，更多的细节请见下节
import { value } from './export';
// 引入值并起别名
import { value as aliasNameOfValue } from './export';
// 以下两句等价
import { default as value } from './export';
import value from './export';
```

如果需要引入再导出，可以简写为：

```javascript
export { value } from './export';
```

## 资源链接

- nvm：<https://github.com/nvm-sh/nvm>
- Babel：<https://babeljs.io/>