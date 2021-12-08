# 工程类面试题

> 针对简历内容深究

## webpack

核心思想：模块化，一切皆模块

#### CommonJS

CommonJS 是一种使用广泛 JS 模块化规范，核心思想是通过 `require` 来**同步**加载依赖的其他模块，通过 `module.exports` 导出需要暴露的接口

```js
// 导出
const moduleA = require('./moduleA');

// 导出
module.exports = moduleA.foo;
```

优点：

+ 代码可复用与 Node.js 环境下并允许
+ 通过 NPM 发布的很多第三方模块都采用了 CommonJS 规范

缺点：

+ 无法直接运行在浏览器环境下，必须通过工具转换成标准的 ES5

#### AMD

与 CommonJS 最大的不同是采用**异步**的方式去加载依赖的模块。

AMD 规范主要是为了解决针对浏览器环境的模块化问题，最佳实践是 `requirejs`

```js
// 定义模块
define('module', ['dep'], function (dep) {
    return exports;
});

// 导入和使用
require(['module'], function (module) {
    ...
});
```

优点：

+ 可在不转换代码的情况下直接在浏览器中运行
+ 可异步加载依赖
+ 可并行加载多个依赖
+ 代码可运行在浏览器环境和 Node.js 环境下

缺点：

+ JS 运行环境没有原生支持 AMD，需要先导入实现了 AMD 的库后才能正常使用

#### ES6 模块化

```js
// 导入
import React, { Component } from 'react';

// 导出
export function foo() {};
export default bar;
```

无法直接运行在大部分 JS 运行环境下，必须通过工具转换成标准的 ES5 后才能正常使用

### 构建工具

+ 代码转换
+ 文件优化
+ 代码分割
+ 模块合并
+ 自动刷新
+ 代码校验
+ 自动发布

Loader 转换文件；Plugin 扩展功能，在构建流程中注入钩子实现

### 配置

1. entry

【必填项】配置模块的入口，Webpack 执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块

Webpack 在寻找相对路径的文件时会以 context 为根目录，默认为执行启动 Webpack 时所在的当前工作目录。context 必须是一个绝对路径的字符串

2. output

一个对象，配置如何输出代码

filename


## 脚手架搭建

### inquirer.js

inquirer.js 是命令行与用户交互工具

#### 语法结构

```js
inquirer.prompt([...])
    .then(...)
    .catch(...)
```

参数有：

+ type: 表示提问的类型，包括

    - `input` 输入型
    - `confirm` Y/N 型
    - `list` 无序列表选择
    - `rawlist` 有序列表选择
    - `expand` 扩展列表选择（可自定义选项）
    - `checkbox` 勾选
    - `password` 密码型
    - `editor` 长文本型

+ name: 存储当前问题答案的变量
+ message: 问题描述
+ default: 默认值
+ choices: 列表选项（分隔符 inquirer.Separator）
+ validate: 对回答进行校验
+ filter: 对回答进行过滤，返回处理后的值
+ transformer: 对回答的显示效果（styles）进行处理
+ when: 根据问题的回答，判断当前问题是否需要被回答
+ pageSize: 修改渲染行数，适用于 list / rawList / expand / checkbox
+ prefix: 修改 message 默认前缀
+ suffix: 修改 message 默认后缀

### commander.js

详见：https://github.com/tj/commander.js/blob/HEAD/Readme_zh-CN.md

### 构建 cli

https://www.cnblogs.com/rongfengliang/p/8245016.html

## Sentry

## 图表、组件库搭建

## 音视频

## 滚动加载

## 文档搭建

gitbook