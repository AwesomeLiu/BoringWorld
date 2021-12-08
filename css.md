# CSS Interview Questions

### 1. 垂直水平居中

```html
<div class="parent">
    <div class="child"></div>
</div>
```

#### 方法一：flex 布局

```css
div.parent {
    display: flex;
    justify-content: center; /* 横轴 */
    align-items: center; /* 纵轴 */
}
```

#### 方法二：定位

```css
div.parent {
    position: relative;
}

/* transform */
div.child {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

/* margin */
div.child {
    position: absolute;
    width: 50px;
    height: 50px;
    top: 50%;
    left: 50%;
    margin-top: -25px;
    margin-left: -25px;
}
/* 或 */
div.child {
    position: absolute;
    width: 50px;
    height: 50px;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
```

#### 方法三：grid

```css
div.parent {
    display: grid;
}

div.child {
    justify-self: center;
    align-self: center;
}
```

### 2. `flex: 0 1 auto;` 的意思

flex 是复合属性，设置或检索弹性盒模型对象的子元素如何分配空间

flex 是 flex-grow flex-shrink flex-basis 的简写

+ **单值语法**

    - 无单位数 `<num>`，被当作是 `flex: <num> 1 0;`

    - 有效的宽度值 `<width>`，被当作是 flex-basis 的值

    - 关键字 `none` | `auto` | `initial`

+ **双值语法**：第一个值必须为无单位数 `<num>`，被当作 flex-grow 的值。第二个值必须为

    - 无单位数 `<num>`，被当做是 flex-shrink 的值

    - 有效的宽度值 `<width>`，被当作是 flex-basis 的值

+ **三值语法**

    - 第一个值必须为无单位数 `<num>`，被当作 flex-grow 的值

    - 第二个值必须为无单位数 `<num>`，被当作 flex-shrink 的值

    - 第三个值必须为有效的宽度值 `<width>`，被当作是 flex-basis 的值

### 3. flex 元素宽度的计算

```html
<ul class="flex">
    <li>a</li>
    <li>b</li>
    <li>c</li>
</ul>
```

```css
.flex { display: flex; }
.flex :nth-child(1) { flex: 1 1 300px; }
.flex :nth-child(2) { flex: 2 2 200px; }
.flex :nth-child(3) { flex: 3 3 400px; }
```

#### 例1：收缩

```css
.flex { width: 800px; }
```

**解：**

1. 计算 flex-basis，相加 300 + 200 + 400 = 900px

2. 与父容器宽度（800px）比较，则会**溢出** 900 - 800 = 100px

3. 根据收缩比率 flex-shrink，加权得到 300 * 1 + 200 * 2 + 400 * 3 = 1900px

4. 分配溢出量

    + a 的溢出量 (300 * 1 / 1900) * 100 = 16px

    + b 的溢出量 (200 * 2 / 1900) * 100 = 21px

    + c 的溢出量 (400 * 3 / 1900) * 100 = 63px

5. 移除溢出量

    + a 的实际宽度为 300 - 16 = 284px

    + b 的实际宽度为 200 - 21 = 179px

    + c 的实际宽度为 400 - 63 = 337px

#### 例2：扩展

```css
.flex { width: 1500px; }
```

**解：**

1. 计算 flex-basis，相加 300 + 200 + 400 = 900px

2. 与父容器宽度（1500px）比较，则会**剩余** 1500 - 900 = 600px

3. 根据扩展比率 flex-grow 分配剩余宽度

    + a 的扩展量为 1 / (1+2+3) * 600 = 100px

    + b 的扩展量为 2 / (1+2+3) * 600 = 200px

    + c 的扩展量为 3 / (1+2+3) * 600 = 300px

4. 添加扩展量

    + a 的实际宽度为 300 + 100 = 400px

    + b 的实际宽度为 200 + 200 = 400px

    + c 的实际宽度为 400 + 300 = 700px

### 4. css 优先级

`!important` > 内联（1000） > ID 选择器（100） > 类选择器（10） > 标签选择器（1）

### 5. 盒模型

```css
/* 标准盒模型 默认 */
box-sizing: content-box;
/* width = content */

/* 怪异盒模型 */
box-sizing: border-box;
/* width = content + padding + border */
```

### n. css modules 的原理
