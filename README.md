# 前端 100 问

## JS 基础

0. JS 基础数据类型
1. `var` / `let` / `const` 的区别
2. 什么是闭包？【手写】闭包中怎么解决回收？
3. `call` / `apply` / `bind` 的区别？【手写】
4. 谈谈对原型和原型链的理解
5. 隐式转换问题
6. 如何准确判断数组？
7. 什么是浅拷贝和深拷贝？ 【手写】
8. 继承的方式有哪些？ 【手写】
9. `extends` 和寄生组合继承有什么区别？
10. `new` 的原理是什么？返回值会影响什么？ 【手写】
11. 什么是事件循环机制 (Event Loop) ？
12. 哪些是微任务？哪些是宏任务？
13. 谈谈对 `Promise` 的理解和认识
14. `async/await` 和 `Generator` 函数的关系和区别
15. 箭头函数和普通函数的区别
16. `for ... in` 和 `for ... of` 的区别
17. 有哪些获取对象 key 值的方法，都分别获取到的是怎么样的 key 值？
18. 数组去重的方法
19. 数组扁平化的方法
20. `Map` / `Set` / `WeakMap` / `WeakSet` 的区别
21. 变量提升问题
22. 什么是函数柯里化？ 【手写】
23. 链式调用 【手写】（LazyMan）
24. 数组排序的方法及**复杂度**。sort 排序，用了什么排序？
25. 字符串模板是怎么解析的？
26. `try...catch` 和 `Promise.catch` 的区别？ `try...catch` 能捕获 `Promise` 里的错误吗？
27. 事件的三个阶段分别是？捕获目标冒泡 事件委托
28. `map` 和 `forEach` 的区别
29. 栈和堆
30. `JSON.stringfy` 的缺点
31. `Proxy` 和 `Object.defineProperty()`
32. `typeof` 和 `instanceof`
33. 

## HTML5 和 CSS3

1. 垂直水平居中
2. CSS 优先级
3. `flex: 0 1 auto` 表示什么意思？
4. `position` 的值都有哪些？分别呈现什么样的效果？
5. 浏览器渲染页面的步骤
6. 什么是标准盒模型？什么是怪异盒模型？可以通过什么设置？
7. flex 布局计算宽度问题
8. 什么是回流 (reflow) 和重绘 (repaint) ？触发它们的行为有哪些？
9. HTML5 新特性有哪些？
10. 对语义化的理解
11. async 和 defer 的区别
12. BFC
13. 伪类和伪元素
14. 外边距塌陷、父容器高度塌陷
15. `DOM0` / `DOM2` / `DOM3`
16. CSS 单位 `px` / `rem` / `em`
17. 

## 网络知识

1. tcp 三次挥手和四次握手
2. http 状态码
3. 缓存
4. 数据存储
5. 什么是跨域？如何处理跨域请求？
6. 什么是简单请求？什么是复杂请求？
7. Options 是干什么的？
8. `Get` 和 `Post` 的区别
9. `http/1.0` / `http/1.1` / `http/2` / `https` 的区别和原理
10. `http` 头部都有哪些？
11. CDN 是什么？获取最近节点资源的算法是什么？为什么可以实现 CDN 加速？
12. 前端安全（中间人攻击劫持（MITM）、XSS、CSRF）
13. JSONP
14. Redis
15. chrome 调试 performance
16. TCP 和 UDP
17. `JWT` `Cookie` `Session` 的区别
18. `websocket` 和 `keep-alive`
19. v8 垃圾回收
20. `XMLHttpRequest`
21. 怎么设置会让 cookie 自动添加到请求头？
22. 双栏布局、三栏布局（圣杯布局、双飞翼布局等）
23. http 请求时有序的吗？如何保证有序？

## 手写

1. 并发调用只调用一次，怎么实现？
2. 如何重新定义 `getter` / `setter` 方法？
3. 请实现一个 `retry` 方法，在给定的次数中尝试，只要有一次成功即为成功，否则失败 
4. 节流 (throttle) 和 防抖 (debounce) 的实现，都有哪些场景？
5. 如何判断一个单链有环？
6. 如何判断循环引用？
7. 请实现 `Promise.any` / `Promise.all` 方法
8. 请实现 `Promise.race` / `Promise.allSettled` 方法
9. 

## 项目实践

1. 


1. 【React】 对 React 中 Hooks 的理解？
2. 【React】 React 是如何渲染的？
3. 【React】 React 是如何重新渲染的？
8. 【CSS】 动画旋转
12. 【React】 虚拟DOM
14. 【Vue】 数据劫持
15. 【Vue】 双向绑定
18. 【Vue】 组件通信的方法
19. 【React】 diff 算法
21. 【操作系统】 线程与进程
23. 【工程化】 webpack 怎么优化
25. 【React】 React 中 setState 的理解
26. 【工程化】 Tree shaking
27. 【工程化】 首屏和白屏及其他页面时间计算？区别？优化？
31. 【React】 React 如何优化？
33. 【React】 React fiber
34. 【React】 react-router 原理 hash 模式和 history 模式
35. node.js / egg.js
36. 【工程化】 首屏优化方案
37. 【工程化】 CI/CD
38. 【工程化】 mock.js
