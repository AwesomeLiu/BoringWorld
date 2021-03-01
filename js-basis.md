# JavaScript Basis

### 1. 基础数据类型

+ String
+ Number
+ Boolean
+ null
+ undefined
+ BigInt（Number 的扩展）
+ Object（引用类型）
+ Symbol（es2016 引入）

### 2. var / let / const 的区别

+ var 和 let 用于声明变量；const 声明只读的常量，一旦声明，内存地址所保存的数据不得改动

+ var 不存在块级作用域，声明后在全局范围内都有效；而 let 和 const 只在代码块内有效

+ var 有变量提升，在非严格模式下，可以先使用，后声明；而 let 和 const 不允许；且 const 必须在定义时赋值

+ let 存在“暂时性死区”问题，即在代码块内，let 声明变量前，变量是不可用的

+ let 不允许重复声明；var 重复声明会进行覆盖

+ 详见： https://es6.ruanyifeng.com/#docs/let

### 3. 继承方式

#### 1. 原型链继承

```js
function Parent() {
    this.name = 'Peter';
}

Parent.prototype.getName = function () {
    console.log(this.name);
};

function Child() {}

Child.prototype = new Parent(); // 实现继承

var child1 = new Child(); // 实例化
```

**缺点：**

1. 引用类型的属性被所有实例共享

2. 在创建 Child 的实例时，不能向 Parent 传参

#### 2. 经典继承（借用构造函数）

```js
function Parent(name) {
    this.name = name;
}

function Child(name) {
    Parent.call(this, name); // 实现继承
}

var child1 = new Child('Peter'); // 实例化
```

**缺点：** 方法都在构造函数中定义，每次创建实例都会创建一遍方法

#### 3. 组合继承

将原型链继承和经典继承结合，是最常用的继承模式

```js
function Parent(name) {
    this.name = name;
}

Parent.prototype.getName = function () {
    console.log(this.name);
}

function Child(name, age) {
    Parent.call(this, name); // 经典继承
    this.age = age;
}

Child.prototype = new Parent(); // 原型链继承
Child.protytype.constructor = Child;

var child1 = new Child('Kevin', 18); // 实例化
```

**缺点：** 会调用两次父构造函数，一次在原型链继承时，一次在实例化时

#### 4. 原型式继承

ES5 中 `Object.create()` 的模拟实现

```js
function createObj(o) {
    function F() {}
    F.prototype = o;
    return new F();
}
```

**缺点：** 与原型链继承一样，引用类型的属性值会被实例共享

#### 5. 寄生式继承

```js
function createObj(o) {
    var clone = Object.create(o);
    clone.greet = function () {
        console.log('hi');
    };
    return clone;
}
```

**缺点：** 与经典继承一样，每次创建对象都会创建一遍方法

#### 6. 寄生组合式继承

```js
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function prototype(child, parent) {
    var prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}

prototype(Child, Parent);
```

只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变，能够正常使用 instanceof 和 isPrototypeOf。是引用类型最理想的继承范式。

### 4. Promise

Promise 通过链式调用解决了回调地狱，是 es6  异步编程的一种解决方案

Promise 对象有两个特点：

1. 对象的状态不受外界影响。有三种状态：

    + pending 进行中

    + fulfilled 已成功

    + rejected 已失败

2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。状态的变化只有两种可能：

    + pending -> fulfilled

    + pending -> rejected

    只要状态发生变化，会一直保持这个结果，称为 resolved 已定型

Promise 存在的缺点：

1. 无法取消 Promise，一旦新建 Promise 就会立即执行，无法中途取消

2. 如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部

3. 当处于 pending 状态时，无法得知目前进展到哪一个阶段

#### Promise.prototype.then()

接受两个可选参数，第一个是 resolved 状态的回调函数，第二个是 rejected 状态的回调函数。返回一个新的 Promise 实例，与原来的不一样。

#### Promise.prototype.catch()

是 `.then(null | undefined, rejection)` 的别名，用于指定发生错误时的回调函数。另外在 `then()` 指定的回调函数中抛出错误，也会被 `catch()` 方法捕获。

如果 Promise 的状态已经变成 resolved，再抛出错误是无效的（特点二）。

Promise 对象的错误具有冒泡性质，会一直向后传递，直到被捕获为止。所以一般不要在 `then()` 的方法里定义 reject 状态的回调函数，而是使用 `catch()` 方法捕获。

与 `try/catch` 不同的是，如果没有使用 `catch()` 方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，不会有任何反应。

#### Promise.finally()

指定不论 Promise 对象最后状态如何，都会执行。不接受任何参数。

#### Promise.all()

将多个 Promise 实例包装成一个新的 Promise 实例。接受一个数组（或具有 Iterator 接口，且返回的每个成员都是 Promise 实例）作为参数。如果不是 Promise 实例，就会调用 `Promise.resolve()`，将参数转为 Promise 实例。

新 Promise 的状态由数组中的 Promise 决定：

+ 数组中所有的 Promise 状态都变为 fulfilled，新 Promise 的状态才会变成 fulfilled。此时所有的返回值组成一个数组，传递给新的 Promise 的回调函数

+ 数组中任意一个 Promise 状态时 rejected，新 Promise 的状态就会变成 rejected。此时第一个被 reject 的实例的返回值，会传递给新 Promise 的回调函数

**注意：** 如果作为参数的 Promise 实例定义了 catch 方法，那么即使被 rejected，也不会触发 `.all()` 的 `catch()` 方法

#### Promise.race()

将多个 Promise 实例包装成一个新的 Promise 实例。与 `all()` 不同的是，只要数组中任意一个 Promise 状态发生变化，新的 Promise 的状态就会发生变化。率先发生变化的 Promise 实例的返回值，就传递给新 Promise 的回调函数。

#### Promise.allSettled()

将多个 Promise 实例包装成一个新的 Promise 实例。只有等到所有实例都返回结果，无论是 fulfilled 还是 rejected，包装实例才会结束，且新的 Promise 实例状态总是 fulfilled。

返回值是 allSettledPromise，状态为 fulfilled。监听函数接收到的参数是数组 results，数组成员是一个对象，具有 status 属性，值为 `'fulfilled'` 或 `'rejected'`。为 fulfilled 时，对象有 value 属性；为 rejected 时，对象有 reason 属性，对应状态的返回值。

#### Promise.any()

将多个 Promise 实例包装成一个新的 Promise 实例。

+ 任意一个参数实例变成 fulfilled 状态，新 Promise 就会变成 fulfilled 状态

+ 所有参数实例变成 rejected 状态，新 Promise 才会变成 rejected 状态

与 `race()` 不同的是，不会因为某个 Promise 变成 rejected 状态而结束

#### Promise.resolve()

将现有对象转为 Promise 对象

```js
Promise.resolve('foo');
// 等价于
new Promise(resolve => resolve('foo'));
```

`Promise.resolve()` 方法的参数分为四种情况：

1. 参数是一个 Promise 实例，那么不做任何修改，直接返回该实例

2. 参数是一个 thenable 对象（具有 then 方法的对象），将其转为 Promise 对象，然后立即执行 then 方法

3. 参数不是 thenable 对象或不是对象，则返回一个新的 Promise 对象，状态为 resolved

4. 不带任何参数，直接返回一个 resolved 状态的 Promise 对象

#### Promise.reject()

返回一个 rejected 状态的 Promise 实例

```js
Promise.reject('foo');
// 等价于
new Promise((resolve, reject) => reject('foo'));
```

`Promise.reject()` 方法的参数会原封不动的作为 reject 的理由，成为后续方法的参数

### 5. Iterator 遍历器

Iterator 是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作

Iterator 的作用有三个：

1. 为各种数据结构，提供统一、简便的访问接口

2. 使数据结构的成员能够按照某种次序排列

3. 为 es6 中的 `for...of` 循环服务

Iterator 的遍历过程如下：

1. 创建一个指针对象，指向当前数据结构的起始位置，即 Iterator 对象**本质**上是一个指针对象

2. 第 n 次调用指针对象的 next 方法，将指针指向数据结构的第 n 个成员，返回数据该成员的信息（包含 value 和 done 两个属性，value 表示当前成员的值，done 是布尔值，表示遍历是否结束

3. 直到指向数据结构的结束位置

原生具有 Iterator 接口的数据结构有：

+ Array
+ Map
+ Set
+ String
+ TypedArray
+ 函数的 arguments 对象
+ NodeList 对象

调用 Iterator 接口的场合：

+ 解构赋值
+ 扩展运算符（...）
+ yield*
+ for ... of
+ Array.from()
+ Map(), Set(), WeakMap(), WeakSet()
+ Promise.all(), Promise.race()

### 6. Generator 函数

Generator 在语法上，可以理解为一个状态机，封装了多个内部状态；在形式上，是一个普通函数，具有两个特征（星号和 yield）。调用函数后，函数不执行，返回一个指向内部状态的指针对象（Iterator Object）

yield 暂停执行标志，只有调用 next 方法，内部指针指向该语句时，才会执行 yield 后的表达式。yield 没有返回值（可认为总是返回 undefined）

next 方法可以带一个参数，该参数会被当做**上一个** yield 表达式的返回值（第一次 next 传值无效）

#### Generator.prototype.throw()

可以在函数外抛出错误，在 Generator 内部捕获

可接受一个参数，会被 catch 语句接收

thorw 方法抛出的错误要被内部捕获，必须至少执行过一次 next 方法

throw 方法被捕获后，会附带执行下一条 yield 表达式，即自动执行一次 next 方法

#### Generator.prototype.return()

返回给定的值（不提供参数，则返回值的 value 为 undefined），并终结遍历 Generator 函数

如果 Generator 函数内部有 `try...finally` 代码块，且正在执行 `try` 代码块，那么 `return()` 会导致立即进入 `finally` 代码块，执行完后，函数才结束

next / throw / return 的作用都是让 Generator 函数恢复执行，并使用不同的语句替换 yield 表达式：

+ next 是将 yield 替换成一个值

+ throw 是将 yield 替换成一个 throw 语句

+ return 是将 yield 替换成一个 return 语句

**`yield*`** 用于在一个 Generator 函数里执行另一个 Generator 函数

### 7. async/await

async/await 相当于 */yield

async 对 Generator 进行了四点改进：

1. 内置执行器

Generator 函数的执行必须依靠执行器（co 模块），而 async 函数自带执行器，无需如 next 之类的方法才能执行得到结果

2. 更好的语义

3. 更广的适用性

co 模块约定，yield 后只能是 Thunk 函数或 Promise 对象，而 async 函数的 await 命令后可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但都会自动转成立即 resolved 的 Promise 对象）

4. 返回值是 Promise

Generator 函数的返回值是 Iterator 对象，async 函数的返回值是 Promise 对象

#### await

await 的返回值和 await 后的表达式有关：

+ 是一个 Promise 对象，返回该对象的结果

+ 是一个**非** thenable 对象，直接返回对应的值

+ 是一个 thenable 对象，将其等同于 Promise 对象返回

任何一个 await 语句后的 Promise 对象变成 rejected 状态，那么整个 async 函数都会中断执行（若错误被 `try...catch` 或 `.catch()` 捕获，则不影响）

await 使用注意点：

1. await 命令最好放在 `try...catch` 代码块中

2. 多个 await 命令后的异步操作，如果是相互独立的，可以使用 `Promise.all()` 同时触发

3. await 只能在 async 函数中使用

4. async 函数可以保留运行堆栈

5. 顶层 await 只能用在 ES6 模块中，不能用在 CommonJS 模块

### 8. Class

+ 类的方法都定义在 prototype 上

+ prototype 对象的 constructor() 属性，直接指向类本身

+ 类的内部所有定义的方法不可枚举

+ 类不存在变量提升

+ this 指向类的实例

+ static 不会被实例继承，要通过类来调用；静态方法可与非静态方法重名；可被子类继承

+ new.target 在类中使用，用来确定构造函数是如何调用的。可实现不能独立使用，必须继承后才能使用的类（接口？）

### 9. Proxy

用于修改某些操作的默认行为，在语言层面做出修改，属于“元编程”

```js
var proxy = new Proxy(target, handler);
```

target 是所要代理的目标对象

handler 是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。如果不设置，相当于直接通向原对象

Proxy 支持的十三种拦截操作：

+ get(target, propKey, receiver)
+ set(target, propKey, value, receiver)
+ has(target, propKey)
+ deleteProperty(target, propKey)
+ ownKeys(target)
+ getOwnPropertyDescriptor(target, propKey)
+ defineProperty(target, propKey, propDesc)
+ preventExtensions(target)
+ getPrototypeOf(target)
+ isExtensible(target)
+ setPrototypeOf(target, proto)
+ apply(target, object, args)
+ construct(target, args)

`Proxy.revocable()` 返回一个可取消的 Proxy 实例

在 Proxy 代理的情况下，目标对象内部的 this 会指向 Proxy

### 10. Reflect

+ 将 Object 对象的一些明显属于语言内部的方法（如 `Object.defineProperty`），放到 Reflect 对象上

+ 修改某些 Object 方法的返回结果，让其变得更合理（如 `Object.defineProperty` 在无法定义属性时，会抛出一个错误）

+ 让 Object 操作变成函数行为（如 `delete key`）

+ Reflect 对象的方法与 Proxy 对象的方法一一对应

### 11. Module

```js
// CommonJS 模块
const { stat, exists, readfile } = require('fs');

// ES6 模块
import { stat, exists, readFile } from 'fs';
```

CommonJS 模块是**整体**加载 fs 模块，生成一个对象，然后再从对象上读取方法。称为“运行时加载”，无法在编译时做“静态优化”

ES6 模块是从 fs 模块加载对应的方法，其他不加载，可在编译时就完成模块加载

`import` 会有变量提升，多次执行同一语句也只会执行一次

`import()` 动态加载模块，返回一个 Promise 对象。适用的场合有：按需加载，条件加载，动态模块路径

#### Module 的加载

`defer` 需要等到整个页面再内存中正常渲染结束（DOM 结构完全生成，其他脚本全部执行完毕），才会执行；`async` 一旦下载完，渲染引擎就会中断渲染，立即执行脚本。多个 `defer` 按照顺序加载，多个 `async` 不能保证加载顺序。

`type="module"` 浏览器加载 ES6 模块，异步加载，默认 defer 模式

ES6 模块与 CommonJS 模块的差异：

+ CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用
+ CommonJS 模块是运行时加载，ES6 模块是编译时输出接口
+ CommonJS 模块的 `require()` 是同步加载模块，ES6 模块的 import 命令是异步加载，有独立的模块依赖的解析阶段

循环加载：

+ CommonJS 模块是加载时执行，在 require 时，全部执行，当出现循环加载，就只输出已执行的部分，未执行的部分不会输出
+ ES6 模块是动态引用，使用 import 加载的变量不会被缓存，需要开发者自己保证，在真正取值时能够取到

### 12. 闭包

### 13. 箭头函数

1. 没有 `this`，不会创建上下文
2. 没有 `arguments`
3. 不能通过 `new` 关键字调用，没有 `new.target`
4. 没有原型，不能调用 `super`

