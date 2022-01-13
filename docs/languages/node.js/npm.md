## Node.js 包管理

### 前置知识

- CLI 基本常识
- Node.js 的运行
- Node.js 模块化

### npm

npm 即 Node.js package manager，是 Node.js 默认的包管理工具，按照上节所说的方法安装 Node.js 时，应当已经安装了 npm，可以通过 `npm -v` 来检查 npm 是否已安装成功。

### Node.js 的包管理

新建一个文件夹并执行 `npm init` 命令，根据提示填写字段后，我们看到文件夹中多了一个名为 `package.json` 的文件，这个文件记录了当前项目的基本信息，如项目名称、描述、版本、证书，以及可能用到的一些操作，还有当前项目的依赖信息，这些信息的作用将在后面说明。

### Node.js 的模块查找

上节说到了如何在 Node.js 中引用其他文件导出的模块，但是上节给出的例子都是从相对路径中引入，引入内置或第三方模块时，应当按照如下方式：

```javascript
import fs from 'fs';           // 内置文件系统模块
import express from 'express'; // 一个 web 服务器模块
```

这并不是相对或绝对的路径，这就要提到 Node.js 寻找模块的逻辑。

- 对于内置模块，在编译阶段已经确定了它们的位置，因此没有寻址的问题；
- 对于指定路径的模块：
    1. 首先查找该文件，缺省的后缀名按照 `.js`, `.node`, `.json` 的顺序尝试；
    2. 然后尝试作为文件夹名查找该文件夹下的 `package.json` 并引用其中 `main` 字段指定的文件；
    3. 最后尝试作为文件夹名查找该文件夹下的 `index` 文件，缺省后缀名逻辑与 1. 相同。
- 对于其他没有指定路径的模块，也即通过包管理器安装的模块，会尝试在当前文件夹下的 `node_modules` 文件夹下查找，如果查找失败，则依次从上级文件夹中（可能的） `node_modules` 文件夹下查找。

### 其他包管理器

上一部分引出了一个重要的问题，当我们需要很多依赖的项目时，如何组织这些项目就成为一个问题，因为 `node_modules` 当中的每一个项目都可能有自己的依赖，如果所有项目的依赖都分别安装，显然可能存在许多重复，但是又必须尽量避免不同项目的依赖互相干扰，这就涉及到包管理器的策略问题。

目前常见的包管理器主要有 npm、yarn、pnpm。（cnpm 本质上不过是 npm 自动换源的快捷方式，没有必要使用，后面会给出手动换源的方式）

以下简单介绍几种包管理器的策略区别：

1. 安装
    - npm 的安装是串行的，也就是说只有安装完一个 `package.json` 中声明的依赖后才会安装下一个，而且会把所有的信息打在终端上，而且每次安装同样的内容都是重新下载；
    - yarn 和 pnpm 会同时进行所有的安装，同时优化了输出信息，可以使用缓存减少重复下载。
2. 组织方式
    - 早期 npm 安装的依赖是逐层嵌套的，其结果就是文件路径极长而且不可共享（首先是会重复安装同样的东西浪费空间，其次是不同的模块分别引用两份模块并不能共享内存空间，造成不可预知的 bug）；
    - 现在的 npm 和 yarn 都采用扁平化结构，把所有项目的依赖都装在最上层的 `node_modules` 中，当遇到版本冲突时才单独安装在内层；
    - pnpm 对于同一个项目的同一个版本全局只保留一份，存储在自己的目录中，任何项目引用时都通过硬链接指向同一份文件，仍然使用嵌套结构。

安装的区别自然不用说，npm 比 yarn 和 pnpm 慢很多，值得一提的是第二点，扁平化带来的影响是项目可以非法访问没有声明的包（如果依赖 A 声明了依赖 B，那么依赖 C 不需要声明 B 就可以在上级文件夹中访问到 B），这就造成了潜在的不安全性。其余的差别可以参考 [为什么现在我更推荐 pnpm 而不是 npm/yarn?](https://jishuin.proginn.com/p/763bfbd3bcff)。

因此三者的排序应当是 pnpm > yarn > npm。pnpm 在一些项目中会引起错误，具体原因我没有研究过，如果遇到这种情况，建议换用 yarn，以下将主要以 yarn 为例介绍。

安装两种包管理器的方法是：

```bash
npm install -g yarn
npm install -g pnpm
```

### 包管理器的使用

三种包管理器支持的功能比较类似，语法也比较接近，常用的有：

| 操作                                 | npm                       | yarn                   | pnpm                   |
| ------------------------------------ | ------------------------- | ---------------------- | ---------------------- |
| 自动安装依赖                         | `npm install`               | `yarn install / yarn`    | `pnpm install`           |
| 添加依赖                             | `npm install lodash`        | `yarn add lodash`        | `pnpm add lodash`        |
| 添加开发依赖（不用于生产环境） | `npm install -D typescript` | `yarn add -D typescript` | `pnpm add -D typescript` |
| 移除依赖                             | `npm uninstall lodash`      | `yarn remove lodash`     | `pnpm remove lodash`     |
| 执行 `start` 脚本                             | `npm run start`             | `yarn start`             | `pnpm start`             |


注：在不引起歧义的情况下 npm 允许简写，如 `npm i` 代替 `npm install`。

### Node.js 脚本

前面提到的执行脚本，是指在 `package.json` 中声明的脚本，如 `package.json` 中有如下字段：

```json
{
  "scripts": {
    "start": "node dist/index.js",
  }
}
```

那么执行 `yarn start` 就相当于执行 `node dist/index.js`。但脚本的功能不止于此，例如 `prettier` 是一个代码格式化工具，安装在项目中后并没有安装在 `$PATH` 中，因此直接在 Shell 中运行 `prettier` 不会得到任何结果，此时应该执行 `yarn prettier --write src/**/*.js`，yarn 会找到适当的可执行文件来完成这一命令，我们可以在 `package.json` 中写：

```json
{
  "scripts": {
    "lint": "prettier --write src/**/*.js",
  }
}
```

那么 `yarn lint` 就相当于上述命令。

**npx**

在另一些情况下（比如创建 React 项目），我们虽然需要执行一些类似于 `prettier` 的命令，但以后并不需要安装这个模块，更没有必要把这个模块写在 `package.json` 中，此时我们可以执行 `npx create-react-app example-app`，npx 会自动下载这一模块并运行，但并不会保留在当前文件夹中，也不会对 `package.json` 造成影响。

yarn 和 pnpm 也支持这一语法。

### TypeScript 相关

前面已经讲过 TypeScript 的语法，但 TypeScript 并不能直接在浏览器或 Node.js 中解析（倒是可以在 deno 中解析，但是毕竟 deno 生态还没发展起来），实际上要编写 TypeScript 程序，需要安装 Node.js 模块 `typescript`。这一模块中包含了命令 `tsc` 可以对 TypeScript 语法进行检查，并且生成相应的 JavaScript 文件。例如执行 `yarn add -D typescript` 后，在 `package.json` 的 `script` 字段中添加添加：

```json
{
  "build": "tsc"
}
```

然后运行 `yarn build` 即可对 TypeScript 代码进行编译，更多的配置详见资源链接。

!!! tip "TypeScript 的类型标注"

    我们知道 TypeScript 通过静态类型检查提供额外的安全性，在包管理器安装第三方模块时，通常已经包含了相应模块的类型标注（形如 `index.d.ts` 的文件），但也有部分项目并没有自带类型标注，这部分项目许多都有单独的类型标注模块，这些模块形如 `@types/name`，例如 lodash 并没有自带类型标注，但可以通过 `yarn add -D @types/lodash` 来添加类型标注。

### 资源链接

- tsconfig配置：<https://typescript.bootcss.com/tsconfig-json.html>