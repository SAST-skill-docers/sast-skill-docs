# 环境配置

## 前置环境配置

在继续下面的环境配置之前，本文档默认了你已经安装了高于 v12 的 `node`，并且可以使用 `npm install`。

如果安装 Node.js 或者 npm 遇到困难，可以参考 [Node.js 的引导文档](/languages/node.js)。

## 使用 `yarn` 代替 `npm`

这是一个**可选项**，在实际工程之中两者并没有本质上的差别。与常用的 `npm` 相比，`yarn` 大多情况下具有更快的下载速度和更为清晰的命令行输出。所以我们更加推荐使用 `yarn`。

这里不具体叙述安装过程，可以参考 [官网](https://yarn.bootcss.com) 上给出的方式进行安装，也可以使用 `npm install yarn -g` 命令安装（后者可能不适用于 Linux 用户）。

`yarn` 的命令和 `npm` 的命令大致具有以下对应关系，更细节的关系则可以参考 [npm 的引导文档](/languages/node.js/npm)：

|                  | `npm`                          | `yarn`                 |
| ---------------- | ------------------------------ | ---------------------- |
| 安装指定第三方库 | `npm install react --save`     | `yarn add react`       |
| 移除指定第三方库 | `npm uninstall react --save`   | `yarn remove react`    |
| 开发模式下安装   | `npm install react --save-dev` | `yarn add react --dev` |

## 安装 TypeScript

TypeScript 是 JavaScript 的超集。简而言之，TypeScript 就是带有类型系统的 JavaScript。TypeScript 代码可以直接被编译为 JavaScript 代码并执行。

安装 TypeScript 是简单的：

```shell
# yarn
yarn global add typescript

# npm
npm install -g typescript
```

安装后可以在某一目录下新建 `hello.ts` 文件，输入以下代码：

```typescript
let a: string = "Hello world!";
console.log(a);
```

并在这个目录下执行：

```shell
tsc hello.js
```

如果安装顺利，这个目录下应当会出现编译生成的 `hello.js` 文件，使用命令 `node hello.js` 应当就可以看到命令行输出的问候语。

## 安装 React 框架 & 创建 React 应用

React 框架的安装可以通过脚手架 `create-react-app` 完成，一般而言安装框架这一过程可以集成在创建应用的过程中完成。

进入到你想要创建应用的目录下，执行以下命令：

```shell
# npm 5.2+
npm install create-react-app -g
npx create-react-app <app-path> --template typescript

# yarn 0.25+
yarn global add create-react-app
yarn create react-app <app-path> --template typescript
```

这里 `<app-path>` 是你的应用的目录的路径。这些命令会创建一个使用 TypeScript 开发的 React 应用，并安装 React 框架。

如果安装顺利，使用命令：

```shell
# npm
npm run start

# yarn
yarn start
```

即可编译整个应用，随后应当就可以通过本地 3000 端口访问到 React 的欢迎界面了。

!!! note "使用我们提供的框架"

    为了更好地阅读本文档，我们推荐下载我们提供的简化版 React 框架。我们提供的框架中仅保留了最基础、最必要的代码文件。

    使用我们提供的框架之前，我们希望你已经通过上述步骤成功初始化了一个新的 React 应用并能够在 3000 端口访问到欢迎界面。如果已经确认初始化成功，可以通过 <a href="../../../static/frontend/react/src.zip" target="_blank" download="src.zip">该链接</a> 下载压缩包，请在解压后使用压缩包内的 `src` 文件夹替换你的 React 应用的 `src` 文件夹，并再次使用 `npm run start` 或者 `yarn start` 命令运行 React 应用。

    如果你现在能够在 3000 端口访问到一个写有 `Hello, React!` 字样的页面，则已经完成了配置。

## 使用 ESLint 规范码风

这是一个**可选项**，但是我们认为开发较大工程的时候，尤其是需要多人协作的时候，统一的码风是必要的。

在应用目录下执行下列命令，即可初步配置一个 ESLint：

```shell
# npm
npm install eslint --save-dev
./node_modules/.bin/eslint --init

# yarn
yarn add eslint --dev
./node_modules/.bin/eslint --init
```

执行第二条命令后，ESLint 会询问你的配置。对于前几个问题我们建议选择“强制调整码风” “使用 JavaScript modules(import/export) ” “使用 React 框架” “使用 TypeScript ” “在浏览器上运行代码”。

后续则是对具体码风规则的配置，你可以回答具体问题或选择使用模板，也可以暂且不回答而选择后续手动配置 `.eslintrc` 文件中的码风规则。

下面我们希望能够一键调整码风，比如说终端执行 `yarn fix` 就可以修复所有源代码的码风。现在打开 `package.json` 文件，可以注意到这样的一段：

```json
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject"
}
```

在最后添加一行：

```json
"scripts": {
  ...
  "fix": "./node_modules/.bin/eslint src --ext .js,.jsx,.ts,.tsx --fix"
}
```

这一行的意思是执行 `yarn fix` 的时候会使用 ESLint 将 `src` 目录下的所有扩展名为 `js / jsx / ts / tsx` 的文件修复码风。

不过要注意的是，自动修复能力有限，所以更多的时候还需要手动对文件进行一些调整。自动修复也有可能会出错，这个时候可以自己手动添加让 ESLint 忽略这一部分的注释。