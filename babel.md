# babel

+ babel-cli

+ babel-core

+ babel-runtime

+ babel-node

+ babel-polyfill

+ babel-preset-env

babel 把 JS 中 es2015/2016/2017 等的新语法转化为 es5，让低端运行环境（如浏览器和 node）能够认识并执行

## 使用方式

1. 使用单体文件 (standalone script)

2. 命令行 (cli)

3. 构建工具的插件 (webpack 的 babel-loader, rollup 的 rollup-plugin-babel)

4. 编程的方式 (babel-core)

## 运行方式和插件

babel 总共分为三个阶段：解析，转换，生成

babel 本身不具有任何转换功能，转换功能在 plugin 里。因此当我们不配置任何 plugin 时，经过 babel 的代码和输入是相同的

plugin 总共分为两种：

+ 添加语法插件，能使 babel 解析更多的语法 （babel 内部使用的解析类库是 babylon）

    - 为避免 babel 报错需增加语法插件 babel-plugin-syntax-trailing-function-commas

+ 添加转译插件，在转换时把源码转换并输出

    - babel-plugin-transform-es2015-arrow-functions 可以转换箭头函数

同一类语法可能同时存在语法插件和转译插件，如果使用了转译插件，就无需再使用语法插件

## 配置文件

1. 将插件名字增加到配置文件中（根目录下创建 .babelrc 或 package.json 的 `babel` 里）

2. 使用 npm install babel-plugin-xxx 进行安装

## preset

babel 提供的一组插件集合

+ 官方内容： env, react, flow, minify

+ stage-x 包含当前最新规范的草案 每年更新

    - Stage 0 - 稻草人：只是一个想法，经过 TC39 成员提出即可

    - Stage 1 - 提案：初步尝试

    - Stage 2 - 初稿：完成初步规范 (syntax-dynamic-import)

    - Stage 3 - 候选：完成规范和浏览器初步实现 (transform-object-rest-spread)

    - Stage 4 - 完成：被添加到下一年度发布 没有单独的 stage-4 可供使用

+ es201x, latest

    - 这些是已经纳入到标准规范的语法。如 es2015 包含 arrow-functions，es2017 包含 syntax-trailing-functions-commas。但因为 env 的出现，使得 es2016 和 es2017 都已经废弃

    - latest 是 env 的雏形，它是一个每年更新的 preset，目的是包含所有 es201x。但也是因为 env 的出现，已经废弃

## 执行顺序

+ plugin 会运行在 preset 之前

+ plugin 会从前到后顺序执行

+ preset 的顺序则相反，从后往前

preset 的逆向顺序主要是为了保证向后兼容，因为大多数用户编写顺序是 ['es2015', 'stage-0']。这样必须先执行 stage-0 才能确保 babel 不报错

因此编排 preset 的时候，只要按照规范的时间顺序列出即可

## 插件和 preset 的配置项

插件和 preset 只要列出字符串格式的名字即可。若需要配置项，需要把自己变成数组，第一个元素为字符串名字，第二个元素为配置对象

## env

env 的核心目的是通过配置得知目标环境的特点，然后只做必要的转换

如果不写任何配置项，env 等价于 latest，也等价于 es2015 + es2016 + es2017 三个相加（不包含 stage-x 中的插件）

+ browsers 考虑所配置的浏览器版本具有的特性

+ node 将目标设置为 nodejs 且支持指定的版本，使用 `node: current` 来支持最新稳定版本

+ modules 取值为 `amd`, `umd`, `systemjs`, `commonjs`, `false`。可以让 babel 以特定的模块化格式来输出代码。如果 false 就不进行模块化处理

## 其他配置工具

### babel-cli

cli 是命令行工具，安装了 babel-cli 就能够在命令行中使用 babel 命令来编译文件

在 npm package 是经常会使用以下模式：

+ 把 babel-cli 安装为 devDependencies

+ 在 package.json 中添加 scripts（如 prepublish），使用 babel 命令编译文件

+ npm publish

这样既可以使用较新规范的 JS 语法编写源码，同时又能支持旧版环境。项目不太大，用不到构建工具，可在发布前用 babel-cli 进行处理

### babel-node

babel-node 是 babel-cli 的一部分，不需要单独安装

作用是在 node 环境中，直接运行 es2015 的代码，而不需要额外进行转码

babel-node = babel-polyfill + babel-register

### babel-register

babel-register 模块改写 `require` 命令，为它加上一个钩子。此后，每当使用 `require` 加载 `.js`, `.jsx`, `.es`, `.es6` 后缀名的文件，就会先用 babel 进行转码

使用时，必须首先加载 require('babel-register')

babel-register 只会对 require 命令加载的文件转码，而不会对当前文件转码。由于是实时转码，所以只适合在开发环境使用

### babel-polyfill

babel 默认只转换 js 语法，而不转换新的 API，比如 Iterator, Generator, Set, Maps, Proxy, Reflect, Symbol, Promise 等全局对象，以及一些定义在全局对象上的方法，如 Object.assign。诸如此类都不会转码

babel-polyfill 内部集成了 core-js 和 regenerator

使用时，在所有代码运行之前增加 require('babel-polyfill')。或者更常规的操作是在 webpack.config.js 中将 babel-polyfill 作为第一个 entry。因此必须把 babel-polyfill 作为 dependencies 而不是 devDependencies

babel-polyfill 主要两个缺点：

1. 使用 babel-polyfill 会导致打出来的包非常大，因为 babel-polyfill 是一个整体，把所有方法都加到原型链上。可使用 core-js 的某个类库来解决

2. babel-polyfill 会污染全局变量，给很多类的原型链上都做了修改

因此在实际使用中，通常会倾向于使用 babel-plugin-transform-runtime

但如果代码中包含高版本 js 中类型的实例方法，如 Array.includes，还是要使用 polyfill

### babel-runtime 和 babel-plugin-transform-runtime

babel-plugin-transform-runtime 将定义方法改为引用

在 babel-plugin-transform-runtime 的时候必须把 babel-runtime 当做依赖

babel-runtime 内部集成了

1. core-js: 转换一些内置类（Promise, Symbols 等）和静态方法（Array.from 等）。绝大部分转换是在这里完成的。自动引入

2. regenerator: 作为 core-js 的拾遗补漏，主要是 generator/yield 和 async/await 两组的支持。当代码中有使用 generators/async 时自动引入

3. helpers: 如 asyncToGenerator, jsx, classCallCheck 等，在代码中有内置的 helpers 使用时移除定义，并插入引用

babel-plugin-transform-runtime 不支持实例方法，如 Array.includes

此外，把 helpers 抽离并统一起来，避免重复代码的工作的 babel-plugin-external-helpers 但功能与 transform-runtime 相似

### babel-loader

babel-loader 与 cli 一样，会读取 .babelrc 或 package.json 中 babel 段作为自己的配置，唯一比 cli 复杂的是，会和 webpack 交互，因此需要在 webpack 配置

### 小结

| 名称 | 作用 | 备注 |
|:-----|:-----|:-----|
| babel-cli | 允许命令行使用 babel 命令转译文件 | |
| babel-node | 允许命令行使用 babel-node 直接转译 + 执行 node 文件 | 随 babel-cli 一同安装 babel-node = babel-polyfill + babel-register |
| babel-register | 改写 require 命令，为其加载的文件进行转码，不对当前文件转码 | 只适用于开发环境 |
| babel-polyfill | 为所有 API 增加兼容方法 | 需要在所有代码之前 require 且体积较大 |
| babel-plugin-transform-runtime & babel-runtime | 把帮助类方法从每次使用前定义改为统一 require 精简代码 | babel-runtime 需要安装为 dependencies |
| babel-loader | 使用 webpack 时作为一个 loader 在代码混淆之前进行代码转换 | |

## Babel 7.x

### preset 的变更

+ 淘汰 es201x

+ 删除 stage-x

+ 强推 env

+ 可使用 babel-upgrade 平滑迁移

### npm package 名称的变化

babel 7 把所有 `babel-*` 重命名为 `@babel/*`。在 .babelrc 的配置中也需要进行更改

babel 解析语句的内核 babylon 现在重命名为 `@babel/parser`

所有包含在 stage-x 的转译插件都使用了 `proposal` 前缀，语法插件不在其列，如 `@babel/plugin-proposal-function-bind`

如插件名称中包含了规范名称（如 `-es2015-`）一律删除

### 不再支持低版本 node

babel 7 对 nodejs >= 6，不再支持 0.10, 0.12, 4, 5

不再支持指的是在这些 node 环境中不能使用 babel 转译代码，但 babel 转译后的代码仍能在这些环境上运行

### only 和 ignore 匹配规则的变化

在 babel 6 中，`ignore` 选项如果包含 `*.foo.js`，实际上的含义（转化为 glob）是 `./**/*.foo.js`，也就是当前目录包括子目录的所有 `.foo.js` 文件

而在 babel 7 中，相同的表达式 `*.foo.js` 只作用于当前目录，不作用与子目录

这个规则变化只作用于通配符，不作用与路径。所以 `node_modules` 依然包含所有它的子目录，而不单单只有一层

### @babel/node 从 @babel/cli 中独立了 需要单独安装

### babel-upgrade

1. package.json

2. 把依赖（和开发依赖）中所有的 `babel-*` 替换成 `@babel/*`

3. 把 `@babel/*` 依赖的版本更新为最新版

4. 如果 `scripts` 中使用 `babel-node`，自动添加 `@babel/node` 为开发依赖

5. 如果有 `babel` 配置项，检查其中的 `plugins` 和 `presets`，把短名（env）替换为完成的名字（@babel/preset-env）

6. .babelrc

7. 检查其中的 plugins 和 presets，把短名（env）替换为完成的名字（@babel/preset-env）

8. 检查是否包含 `preset-stage-x`，如有替换为对应的插件并添加到 `plugins`
