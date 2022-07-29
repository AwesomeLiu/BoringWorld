### 1. 模拟实现函数的 `call` / `apply` / `bind` 方法

```js
Function.prototype.defineCall = function (context) {
    // 0. 不指定第一个参数，this 的值将会被绑定为全局对象
    context = context || window;
    // 1. 将函数设为对象的属性
    context.fn = this;
    // 1-1. call 可接受一个参数列表
    let args = [...arguments].slice(1);
    // 2. 执行该函数
    let result = context.fn(...args);
    // 3. 删除该函数
    delete context.fn;
    // 4. 返回值
    return result;
}
```

```js
Function.prototype.defineApply = function (context, arr) {
    // apply 接受的是一个包含多个参数的数组
    context = context || window;
    context.fn = this;

    let result;
    if (!arr) {
        result = context.fn();
    } else {
        result = context.fn(...arr);
    }

    delete context.fn;
    return result;
}
```

```js
Function.prototype.defineBind = function (context) {
    // 0. 调用 bind 的必须是函数
    if (typeof this !== "function") {
        throw new Error("blah...");
    }

    let self = this;
    // 1-1. 处理传参，此时的参数为 bind 函数从第二个参数到最后一个参数
    let args = arguments.slice(1);

    // 3. 创建一个空函数来中转
    let fNOP = function () {};

    let fBound = function () {
        // 1-2. 此时的参数为 bind 返回的函数传入的参数
        let bindArgs = [...arguments];
        // 3-1. 绑定函数也能使用 new 操作符创建对象
        return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs));
    };

    // 4. 修改返回函数的 prototype，实例便可继承绑定函数原型中的值
    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();

    // 2. 返回值
    return fBound;
}
```

### 2. 模拟实现函数防抖 (debounce) 和节流 (throttle)

```js
function debounce(func, wait, immediate) {
    let timeout, result;

    let debounced = function () {
        // 2. 修正 this 指向
        let context = this;
        // 3. 增加 events 对象
        let args = arguments;

        // 1. 清除触发
        if (timeout) {
            clearTimeout(timeout);
        }

        // 4. 立即执行
        if (immediate) {
            // 如果已经执行过 则不再执行
            let callNow = !timeout;
            timeout = setTimeout(function () {
                timeout = null;
            }, wait);

            if (callNow) {
                result = func.apply(context, args);
            }
        } else {
            timeout = setTimeout(function() {
                func.apply(context, args);
            }, wait);
        }

        // 5. 返回值
        return result;
    };

    // 6. 添加取消功能
    debounced.cancel = function () {
        clearTimeout(timeout);
        timeout = null;
    };

    return debounced;
}
```

```js
// 1. 使用时间戳
function throttle(func, wait) {
    let prev = 0;
    return function () {
        let now = +new Date();
        let context = this;
        let args = arguments;
        if (now - prev > wait) {
            func.apply(context, args);
            prev = now;
        }
    };
}

// 2. 使用定时器
function throttle(func, wait) {
    let timeout;
    return function () {
        let context = this;
        let args = arguments;
        if (!timeout) {
            timeout = setTimeout(function () {
                timeout = null;
                func.apply(context, args);
            }, wait);
        }
    };
}

// 比较两种方法
// 1. 第一种会立刻执行，第二种会在 n 秒后第一次执行
// 2. 第一种停止触发后没有办法再执行事件，第二种停止触发后依然会再执行一次事件
```

### 3. 模拟实现深拷贝 (deep clone)

```js
// 1. JSON.parse(JSON.stringify()) 实现深拷贝

// 2. 递归实现深拷贝
function deepClone(obj) {
    if (typeof obj !== "object") {
        return;
    }

    let newObj = obj instanceof Array ? [] : {};

    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
            if (typeof obj[key] === "object") {
                newObj[key] = deepClone(obj[key])
            } else {
                newObj[key] = obj[key];
            }
        }
    }

    return newObj;
}
```

### 4. 数组扁平化

```js
// 1. Array.prototype.flat([depth])
// depth 为深度，默认值为 1
// depth 为 Infinity 时可展开任意深度的嵌套数组

// 2. 递归
function flatten(arr) {
    let result = [];
    for (let i = 0, len = arr.length; i < len; i++) {
        if (Array.isArray(arr[i])) {
            result = result.concat(flatten(arr[i]));
        } else {
            result.push(arr[i]);
        }
    }
    return result;
}

// 3. reduce
function flatten(arr) {
    return arr.reduce(function (prev, next) {
        if (Array.isArray(next)) {
            return prev.concat(flatten(next));
        } else {
            return prev.concat(next);
        }
    }, []);
}

// 4. toString() 只能用于数字数组
function flatten(arr) {
    return arr.toString().split(",");
}

// 5. undercore
/**
 * 数组扁平化
 * @param  {Array} input   要处理的数组
 * @param  {boolean} shallow 是否只扁平一层
 * @param  {boolean} strict  是否严格处理元素，下面有解释
 * @param  {Array} output  这是为了方便递归而传递的参数
 * 源码地址：https://github.com/jashkenas/underscore/blob/master/underscore.js#L1030
 */
function flatten(input, shallow, strict, output) {
    output = output || [];
    let idx = output.length;

    for (let i = 0, len = input.length; i < len; i++) {
        let value = input[i];
        if (Array.isArray(value)) {
            if (shallow) {
                let j = 0;
                while (j < value.length) {
                    output[idx++] = value[j++];
                }
            } else {
                flatten(value, shallow, strict, output);
                idx = output.length;
            }
        } else if (!strict) {
            output[idx++] = value;
        }
    }

    return output;
}
```

### 5. 模拟实现 `new`

> new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例

new 关键字会进行如下的操作：

1. 创建一个空的简单 JS 对象（即 `{}`）
2. 链接该对象（设置该对象的 `constructor`）到另一个对象
3. 将 *步骤 1* 新创建的对象作为 `this` 的上下文 
4. 如果该函数没有返回对象，则返回 `this`

```js
function simulateNew() {
    // 1. 创建一个空对象
    var obj = new Object();
    // * 因为是模拟函数，所以调用时是 simulateNew(A, ...)
    // * 所以需要取出第一个参数作为 new 的构造函数
    var Constructor = [].shift.call(arguments);
    // 2. 将新对象的原型指向构造函数的原型
    obj.__proto__ = Constructor.prototype;
    // 3. 使用 apply 改变 this 指向到新对象上
    var ret = Constructor.apply(obj, arguments);
    // 4. 如果构造函数有返回值，不为 null 且是个对象，则直接返回该返回值；否则返回新对象
    return ret != null && typeof ret === 'object' ? ret : obj;
};
```

### 6. 模拟实现 `Promise.all()` / `Promise.any()` / `Promise.race()` / `Promise.allSettled()`

#### Promise.all()

```js
function proAll(proArr) {
    if (proArr != null && typeof proArr[Symbol.iterator] === "function") {
        return new Promise((resolve, reject) => {
            let result = [];
            let len = proArr.length;
            let count = 0;

            if (len === 0) {
                return resolve([]);
            }
            
            for (let i = 0; i < len; i++) {
                proArr[i].then(data => {
                    result[i] = data;
                    if (++count === len) {
                        resolve(result);
                    }
                }, reject);
            }
        });
    } else {
        console.error(new TypeError(`${typeof proArr} ${proArr} is not iterable`));
    }
}
```

#### Promise.any

```js
function proAny(proArr) {
    if (proArr != null && typeof proArr[Symbol.iterator] === "function") {
        return new Promise((resolve, reject) => {
            let errors = [];
            let len = proArr.length;
            let count = len;

            if (len === 0) {
                return reject(new AggregateError("All promises were rejected"));
            }

            for (let i = 0; i < len; i++) {
                proArr[i].then(resolve, (err) => {
                    errors[i] = err;
                    if (--count === 0) {
                        reject(errors);
                    }
                });
            }
        });
    } else {
        console.error(new TypeError(`${typeof proArr} ${proArr} is not iterable`));
    }
}
```

#### Promise.race

```js
function proRace(proArr) {
    if (proArr != null && typeof proArr[Symbol.iterator] === "function") {
        return new Promise((resolve, reject) => {
            for (let i = 0; i < proArr.length; i++) {
                proArr[i].then(resolve, reject);
            }
        });
    } else {
        console.error(new TypeError(`${typeof proArr} ${proArr} is not iterable`));
    }
}
```

#### Promise.allSettled()

```js
function proAllSettled(proArr) {
    if (proArr != null && typeof proArr[Symbol.iterator] === "function") {
        return new Promise((resolve, reject) => {
            let result = [];
            let len = proArr.length;
            let count = len;

            for (let i = 0; i < len; i++) {
                proArr[i].then(data => {
                    result[i] = {
                        status: "fulfilled",
                        value: data
                    };
                }).catch(err => {
                    result[i] = {
                        status: "rejected",
                        reason: err
                    };
                }).finally(() => {
                    if (--count === 0) {
                        resolve(result);
                    }
                });
            }
        });
    } else {
        console.error(new TypeError(`${typeof proArr} ${proArr} is not iterable`));
    }
}
```