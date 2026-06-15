## 第 2 天：JSX 到底是什么？

初学者在刚接触 React 的时候，看到下面这段代码通常会非常疑惑：

```jsx
const element = <h1>Hello, World!</h1>;
```

> “为什么在 JavaScript（JS）代码里，可以直接写 HTML 标签？这不会报错吗？”

这行代码既不是 HTML，也不是纯粹的 JavaScript，它被称为 **JSX**（JavaScript XML）。

---

## 为什么需要 JSX？

在 React 出现之前，如果你想用原生 JS 在页面上创建一个按钮，需要这样写：

```js
// 繁琐的原生 DOM 操作
const btn = document.createElement('button');
btn.className = 'btn-active';
btn.innerText = '点我';
```

而在早期的 React 中，如果不使用 JSX，你需要这样写：

```js
// 纯 JS 创造节点的写法
React.createElement('button', { className: 'btn-active' }, '点我');
```

想象一下，如果界面非常复杂、嵌套很深：

```js
// 让人崩溃的嵌套写法
React.createElement('div', null,
  React.createElement('h1', null, '标题'),
  React.createElement('p', null, '段落一'),
  React.createElement('p', null, '段落二')
);
```

为了解决这种“反人类”的代码书写体验，React 团队创造了 **JSX**：

```jsx
// 极其直观的 JSX 写法
<div>
  <h1>标题</h1>
  <p>段落一</p>
  <p>段落二</p>
</div>
```

**JSX 只是一个语法糖**。在代码打包时，编译器（如 Babel）会自动把 JSX 翻译成上面那种 `React.createElement` 的纯 JS 代码。它存在的唯一目的，就是为了让前端开发者写代码时更爽、更直观。

---

## JSX 的 4 条核心铁律

在 JSX 里写代码非常自由，但也必须遵守以下 4 条硬性规则：

### 1. 必须有且仅有一个“大总管”（根元素）
一个 React 组件不能同时返回两个并列的标签。

❌ **错误示范：**
```jsx
return (
  <h1>标题</h1>
  <p>段落</p>
)
```
因为在 JavaScript 中，一个函数不能同时 `return` 两个独立的值。

👉 **正确示范：** 必须用一个外壳（比如 `<div>`）把它们包起来：
```jsx
return (
  <div>
    <h1>标题</h1>
    <p>段落</p>
  </div>
)
```
如果你不想在页面上平白无故多出一个 `<div>` 标签，React 提供了**空标签**（官方叫 Fragment）：
```jsx
return (
  <>
    <h1>标题</h1>
    <p>段落</p>
  </>
)
```

### 2. 万能的大括号 `{}`
在 JSX 的标签里，如果你想写 **JS 代码**（比如使用变量、做加减法、调用函数、写判断），就必须用大括号 `{}` 包裹起来。

```jsx
const name = "张三";
const age = 18;

return (
  <div>
    {/* 插入变量 */}
    <p>名字：{name}</p>
    
    {/* 进行计算 */}
    <p>明年：{age + 1} 岁</p>
  </div>
);
```

### 3. 属性名要换个名字（避开 JS 关键字）
因为 JSX 最终会变成 JS 代码，所以有些 HTML 属性由于和 JS 的关键字冲突，需要写成别的名字：
* `class` 必须写成 **`className`**（因为 JS 已经占用了 `class` 这个关键字来声明类）。
* `for` 必须写成 **`htmlFor`**（因为 JS 里有 `for` 循环）。
* 所有的事件属性都采用**驼峰命名法**（如 `onclick` 变成 **`onClick`**，`onchange` 变成 **`onChange`**）。

### 4. 行内样式（Style）要用双大括号 `{{ }}`
在普通的 HTML 中，我们这样写样式：
```html
<p style="color: red; font-size: 20px;">字体的样式</p>
```

但在 JSX 中，样式必须以一个 **JS 对象** 的形式存在。所以写法会变成“套娃”式的双大括号：
```jsx
<p style={ { color: 'red', fontSize: '20px' } }>字体的样式</p>
```
* 外层的 `{}` 告诉 React：“我要在这里开始写 JS 代码了”。
* 内层的 `{}` 代表：“这是一个 JS 键值对对象 `{ color: 'red' }`”。
* 注意：CSS 属性中的中划线要改成驼峰写法，比如 `font-size` 必须写成 `fontSize`。

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把 `index.jsx` 里的代码替换为以下内容：

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  const username = "旅行者";
  const isLoggedIn = true; // 试着把这里改成 false，看看右侧预览有什么变化

  // 这是一个样式对象
  const textStyle = {
    color: 'royalblue',
    fontSize: '24px',
    fontWeight: 'bold'
  };

  return (
    <div className="container" style={{ padding: '20px' }}>
      {/* 1. 使用样式对象和变量 */}
      <h1 style={textStyle}>你好，{username}！</h1>
      <p>欢迎来到第 2 天的 React 学习。</p>

      {/* 2. 在大括号内使用三元运算符进行条件判断 */}
      <div>
        当前状态：{isLoggedIn ? "🟢 已登录" : "🔴 未登录，请先登录"}
      </div>

      {/* 3. 使用空标签 <> 包裹并列的元素 */}
      <>
        <hr />
        <small style={{ color: '#888' }}>页面底部版权信息 © 2026</small>
      </>
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

在右侧预览窗口中观察代码渲染的结果，并试着修改 `isLoggedIn` 的值，体验大括号 `{}` 内 JS 逻辑的灵活性。

---

## 明天可以学什么？

理解了 JSX 这个“外壳”后，我们就可以探索如何用它来组装积木了。

建议明天学习：**第 3 天：React 组件（Component）到底是什么？**
