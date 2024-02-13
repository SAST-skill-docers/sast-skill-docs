# TypeScript 语言基础

## 前置知识

- 具有 JavaScript 语言基础，能够编写简单的 JavaScript 语言程序
- 具有面向对象程序设计的基础，能够理解类封装和方法重写（语言最好是 C++）

## 开发环境

JavaScript 是一种高级编程语言，其标准由 ECMA 制定，即 ECMAScript （用于版本称呼时常简称为 ES，如 ES 6、ES 2020 等）。通常而言 JavaScript 在浏览器中运行，由于 ECMA 只规定了 ECMAScript 的语言规范，并没有在具体实现上作出约束，因而在不同的浏览器内核中有不同的实现，如 Google Chrome 中使用 V8 引擎，而 Safari 则使用 JavaScriptCore。

在 V8 引擎的基础上，增加系统库和一系列相关的支持，就得到了可以脱离浏览器环境使用的 JavaScript 运行时，也就是我们开发时主要依赖的环境 Node.js。

Node.js 有自己的包管理工具 npm（即 Node.js package manager），但在日常使用中我们更倾向于使用第三方包管理器 Yarn。

## 课前准备

环境配置主要包括安装 Node.js 和包管理器，关于包管理的具体方式和不同包管理器的区别可以参考 [Node.js 包管理](../node.js/npm)，以下内容将不涉及这些细节。

此外，为了方便展示关于 JavaScript 对象的更多细节，我们将会使用 Chrome 浏览器运行部分代码，请确保你安装了足够新版本的 Chrome 浏览器，并尝试按 F12 打开浏览器调试工具。

### Windows

访问 [Node.js 官网](https://nodejs.org/en/) 下载最新 LTS 版本安装包并打开根据提示安装。

### macOS

如果你没有安装过 Homebrew，请参考 [TUNA](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/) 的说明安装。

使用 Homebrew 安装 Node.js 和 npm：

```bash
brew install node
```

### Linux

建议通过包管理器安装 Node.js，参考[通过包管理器方式安装 Node.js](https://nodejs.org/en/download/package-manager/)，以下为常见发行版安装方法：

#### ArchLinux

```bash
sudo pacman -S nodejs npm
```

#### Debian

```bash
# as root
curl -fsSL https://deb.nodesource.com/setup_16.x | bash -
apt-get install -y nodejs
```

#### Ubuntu

```bash
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 安装 Yarn

完成上述操作后应该可以在环境中找到 `node` 和 `npm`，运行：

```bash
npm i -g yarn
```

### 开发环境

建议使用 VSCode 并安装来自 Microsoft 的插件 JavaScript and TypeScript Nightly，可以提供较好的 TypeScript 语法检查。

## Why TypeScript?

同学们可能注意到，在上文中我们一直在讲 JavaScript 和它的开发环境，而我们要讲的主要内容是 TypeScript。
TypeScript 是 JavaScript 的超集，为 JavaScript 提供了缺失的类型检查，可以帮助开发者写出更加可靠的代码，同时节省开发者的调试时间。

由于 TypeScript 并没有普及的运行时（例如 ts-node、deno 等可以支持直接运行 TypeScript 代码，但使用相对较少，且没有在浏览器中实现），我们需要在 Node.js 环境下开发，并将 TS 代码**等价地**编译成 JS 代码运行。

新建项目并添加 TS 支持：

```bash
mkdir ts-learning
cd ts-learning
yarn init
yarn add -D typescript
```

创建文件 `tsconfig.json` 并写入以下内容：

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "esModuleInterop": true,
        "strictNullChecks": true,
        "target": "ES2020",
        "moduleResolution": "node",
        "outDir": "dist",
        "rootDir": "src"
    }
}
```

这样当执行 `yarn tsc` 时，`tsc` 会根据上述配置编译我们的 TS 代码。
在 `package.json` 中添加以下内容方便运行：

```json
{
    "main": "dist/index.js",
    "scripts": {
        "build": "tsc",
        "start": "node ."
    }
}
```

以上配置的含义可参考 [Node.js 包管理](../node.js/npm#typescript) 中的 TypeScript 相关部分。

### TypeScript vs JavaScript

上文提到 TS 是 JS 的超集，因此任何有效的 JS 代码也是有效的 TS 代码（如果告诉编译器忽略类型缺失），而 TS 代码去掉所有的类型标注后就是合法的 JS 代码（但编译器会为我们做更多，尤其是使用 Babel 等工具时，会将代码转换为更低级的语法），因此我们只讲述 TypeScript 的语法，如果需要直接编写 JS 代码（如在浏览器中直接执行），只需略去所有类型即可，但**请务必谨慎判断变量类型**。

## Hello World

根据前面写的配置，`tsc` 会编译 `src` 目录下的文件并输出到 `dist` 目录下，因此我们创建文件 `src/index.ts` 并编写如下代码：

```typescript
console.log('Hello, world!');
```

编译并运行：

```bash
yarn build
yarn start
```

看到终端中输出 `Hello, world!` 时，你已经编写并运行了你的第一个 TypeScript 程序。

这里 `console` 是内置的控制台对象，`console.log` 是向控制台（终端）输出内容的函数，可以接收任意多的参数。

## 后续拓展

TypeScript 作为目前较为热门的 JavaScript 语言“补丁”，可以被广泛应用于各种 Web 前端框架或者移动端框架。目前类似 React、React Native、Vue 等常用前端框架均提供了 TypeScript 语言支持。

---

可以应用 TypeScript 语言相关知识的课程：

- 软件工程

## 参考资料

- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [JavaScript](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)（中文翻译相当完善）
- [Node.js API Reference](https://nodejs.org/api/)（[中文翻译](http://nodejs.cn/api/)相当完善，但收费 ¥199/年）
- [ESLint](https://eslint.org)
- [Babel](https://babeljs.io)
- [Webpack](https://webpack.js.org)
