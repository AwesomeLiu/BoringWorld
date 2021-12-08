Github: https://github.com/semlinker/reactjs-interview-questions

## 1. Virtual DOM

Virtual DOM(VDOM) 是 Real DOM 的内存表示形式。UI 的展示形式被保存在内存中并与真实的 DOM 同步。这是在调用的渲染函数和屏幕上显示元素之间发生的一个步骤，称为 reconciliation

| Real DOM | Virtual DOM |
|-----|-----|
| 更新较慢 | 更新较快 |
| 可以直接更新 HTML | 无法直接更新 HTML |
| 元素更新，创建新的 DOM | 元素更新，更新 JSX |
| DOM 操作非常昂贵 | DOM 操作非常简单 |
| 较多的内存浪费 | 没有内存浪费 |

VDOM 工作步骤：

1. 每当任何底层数据发生变化时，整个 UI 都将以 VDOM 的形式重新渲染

2. 计算先前的 VDOM 对象与新的 VDOM 对象之间的差异

3. 计算完成，真实的 DOM 将只更新实际更改的内容

> Shadow DOM 是一种浏览器技术，解决了构建网络应用的脆弱性问题，修复了 CSS 和 DOM，在网络平台中引入作用域样式

## 2. React Fiber

Fiber 是 React v16 中新的 reconciliation 引擎，或核心算法的重新实现

Fiber 的目标是提高对动画、布局、手势、暂停、中止或重用任务的能力以及为不同类型的更新分配优先级，及新的并发原语等领域的适用性

## 3. Redux

Redux 遵循三个基本原则：

1. 单一数据来源：整个应用程序的状态存储在单个对象数中

2. 状态是只读的：改变状态的唯一方法是发出一个 action，一个描述发生的事情的对象

3. 使用纯函数进行更改：编写 Reducers 指定状态树如何变化，将先前的状态和操作作为参数，返回下一个状态

redux-saga 使用 Generators

Redux Thunk 使用 Promises

## 4. Hooks

Hooks 允许在不编写类的情况下使用状态和其他 React 特性

Hooks 需要遵守的规则：

1. 仅在顶层的 React 函数调用 hooks。也就是说，不能在循环、条件或内嵌函数中调用 hooks。这确保每次组件渲染时都以相同的顺序调用 hooks，并且它会在多个 useState 和 useEffect 调用之间保留 hooks 的状态

2. 仅在 React 函数中调用 hooks。

useState useEffect useRef useCallback useMemo

## 5. diff 算法

参考：https://zhuanlan.zhihu.com/p/20346379

diff 策略：

1. Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计
2. 拥有相同类的两个组件将生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构
3. 对于同一层级的一组子节点，可以通过唯一 id 进行区分

基于三个前提策略，分别对三个算法进行优化：

1. tree diff: 分层比较，只比较同一层次的节点
2. component diff: 如果是同一类型的组件，按原策略比较；若不是则将组件判断为 dirty component，替换整个组件下的所有子节点
3. element diff: 设置唯一的 key 值，将新老 component 集合对比，进行插入、移动、删除的操作

**建议 1：** 根据 component diff，保持稳定的 DOM 结构会有助于性能提升，可使用 shouldComponentUpdate() 来节省 diff 运算时间

**建议 2：** 根据 element diff，尽量减少类似将最后一个节点移动到列表首部的操作

## 6. router

#### React Router 和 history 库的区别

React Router 是 history 库的包装器，处理浏览器的 `window.history` 与浏览器和 history 的交互，还提供了内存历史记录，对于没有全局历史记录的环境非常有用，如 React Native 和使用 Node 进行单元测试

#### v4 中 <Router> 组件

+ `<BrowserRouter>` 创建 BrowserHistory
+ `<HashRouter>` 创建 HashHistory
+ `<MemoryRouter>` 创建 MemoryHistory

#### HashHistory 与 BrowserHistory 区别

1. HashHistory 的 url 中会有 `#` 符号（不可去掉），在末尾会带有 `?_k=[hash]`（可去掉）；而 BrowserHistory 没有

2. BrowserHistory 使用的是 HTML5 的 History API，浏览器提供相应的接口来修改历史记录；而 HashHistory 是通过改变 hash 值来修改历史记录

3. History API 提供了 `pushState()` 和 `replaceState()` 方法来增加或替换历史记录；而 HashHistory 没有，无法替换（但 react-router 通过 polyfill 实现了）

4. HashHistory 中的 hash 不会被发送到服务端，会造成 hash 值的丢失；BrowserHistory 需要服务端支持，使用 nginx 时需要进行配置

#### 其他组件

+ `<Switch>`
+ `<Redirect>`
+ `<Link>`
+ `<NavLink>`
+ `<StaticRouter>`
+ `<Route>`

#### `<Link>` / `<NavLink>` / `<a>` 的区别

+ `<Link>` 的 onClick 事件禁掉了 `<a>` 的默认事件，跳转事件被 router 托管监听，只会触发对应的 `<Route>` 页面的内容更新，不会刷新整个页面

+ `<NavLink>` 是特殊的 `<Link>`，会在匹配当前 url 时**给已经渲染的元素添加参数**

## n.2. 设计模式
