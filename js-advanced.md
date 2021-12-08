# JS 深入

### 1. 原型和原型链

#### 使用构造函数创建对象

```js
function Person() {

}
var person = new Person();
person.name = 'Kevin';
console.log(person.name); // Kevin
```

#### prototype

```js
function Person() {

}
Person.prototype.name = 'Kevin';
var p1 = new Person();
var p2 = new Person();
console.log(p1.name); // Kevin
console.log(p2.name); // Kevin
```

```
           prototype
  Person -------------> Person.prototype
（构造函数）               （实例原型）
```

#### `__proto__`

每个 JS 对象（除 null）都有的属性，指向该对象的原型

```js
function Person() {

}
var person = new Person();
console.log(person.__proto__ === Person.prototype); // true
```

```
           prototype
  Person -------------> Person.prototype
（构造函数）               （实例原型）
    |                           ↑
    |                           |
    |                           |
    ↓              __proto__    |
  person -----------------------
```

#### constructor

每一个原型都有一个 constructor 指向关联的构造函数

```js
function Person() {

}
console.log(Person === Person.prototype.constructor); // true
```

```
              prototype
  Person   -------------> Person.prototype
（构造函数）<-------------   （实例原型）
    |        constructor        ↑
    |                           |
    |                           |
    ↓              __proto__    |
  person -----------------------
```

**综上：**

```js
function Person() {

}
var person = new Person();

// 以下均为 true
person.__proto__ === Person.prototype;
Person.prototype.constructor === Person;
Object.getPrototypeOf(person) === Person.prototype;
```

1. 所有 **引用类型** 都有一个 `__proto__` （隐式原型）属性，属性值是一个普通对象
2. 所有 **函数** 都有一个 `prototype` （原型）属性，属性值是一个普通对象
3. 所有 **引用类型的 `__proto__`** 属性指向它的**构造函数的 `prototype`**

#### 实例与原型

> 当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还找不到，就会找原型的原型，一直找到最顶层为止

#### 原型的原型

原型对象是通过 Object 构造函数生成的，实例的 `__proto__` 指向构造函数的 prototype

```
              prototype
  Person   -------------> Person.prototype
（构造函数）<-------------   （实例原型）
    |        constructor        ↑ |
    |                           | |
    |                           | |
    ↓              __proto__    | |
  person -----------------------  |
                                  | __proto__
                                  |
                                  |
              prototype           ↓
  Object() ---------------> Object.prototype
           <---------------
              constructor
```

#### 原型链

Object.prototype 没有原型

```js
Object.prototype.__proto__ === null
```

```
              prototype
  Person   -------------> Person.prototype
（构造函数）<-------------   （实例原型）
    |        constructor       ↑↑  ||
    |                          ||  ||
    |                          ||  ||
    ↓              __proto__   ||  ||
  person =======================   ||
                                   || __proto__
                                   ||
                                   ||
              prototype            ↓↓
  Object() ---------------> Object.prototype
           <---------------        ||
              constructor          || __proto__
                                   ↓↓
                                  null
```

由相互关联的原型组成的链状结构就是原型链，也就是加粗的这条线



