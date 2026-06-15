## 第 3 天：React 组件（Component）到底是什么？

在前两天，我们学习了 React 的基本思想和 JSX 语法。今天我们要接触 React 真正强大的核心武器——**组件（Component）**。

如果你去问一个资深前端：

> “React 开发到底是在写什么？”

他一定会告诉你：

> “我们其实就是在**搭乐高积木**。”

在 React 的世界里，**组件就是你的乐高积木。**

---

## 为什么需要“组件”？

在传统的网页开发中，如果你要写一个复杂的页面，你的 HTML 文件可能会长达几千行。

当项目变大后：
* 找一段代码像在大海捞针。
* 很多重复的结构（比如商品卡片、导航栏、按钮）要在不同的地方复制粘贴好几遍。一旦要改样式，就得改几十处地方，极易漏掉并产生 Bug。

React 提出了一个革命性的方案：**组件化。**

我们可以把网页拆成一个个独立的“小零件”，每个零件自己管自己的 UI、样式和逻辑：

```text
 ┌────────────────────────────────────────┐
 │               App 组件                  │
 ├────────────────────────────────────────┤
 │              Header 组件                │
 ├───────────────────┬────────────────────┤
 │    Sidebar 组件    │    Content 组件    │
 │                   │  ┌──────────────┐  │
 │                   │  │  Card 组件   │  │
 │                   │  └──────────────┘  │
 └───────────────────┴────────────────────┘
```

你可以把 `Header`、`Sidebar`、`Card` 分别写在独立的文件里，最后像拼积木一样，把它们拼装进 `App` 组件里。

---

## 如何定义和使用一个组件？

在现代 React 里，定义一个组件极其简单：**它就是一个返回 JSX 的普通的 JavaScript 函数。**

### 1. 定义组件

```jsx
// 这是一个名为 MyButton 的组件
function MyButton() {
  return (
    <button className="btn">
      我是一个按钮组件
    </button>
  );
}
```

### 2. 使用组件

在别的地方，你可以像写普通的 HTML 标签一样来使用这个组件：

```jsx
function App() {
  return (
    <div>
      <h1>欢迎来到我的网站</h1>
      
      {/* 像写 HTML 标签一样，直接写组件名 */}
      <MyButton />
      
      {/* 你可以无限制地复用它 */}
      <MyButton />
    </div>
  );
}
```

---

## 组件的两条硬性铁律

### 铁律一：组件名首字母必须大写！
在 React 中：
* **小写字母开头**的标签（如 `<div>`、`<button>`、`<p>`）会被 React 认为是**原生 HTML 标签**。
* **大写字母开头**的标签（如 `<MyButton />`、`<Header />`）才会被 React 识别为**自定义组件**。

如果你把组件命名为 `function myButton()` 并在页面中写 `<myButton />`，React 会把它当成一个不存在的 HTML 标签，程序会直接报错或无法渲染。

### 铁律二：组件可以嵌套，但不能在函数内部定义新组件
你可以在一个组件里引用另一个组件，但**绝对不要**在一个组件函数的内部去声明另一个组件。

❌ **错误示范：**
```jsx
function Parent() {
  // 绝对不要在里面定义 Child
  function Child() {
    return <p>我是子组件</p>;
  }
  return <Child />;
}
```

👉 **正确示范：** 它们应该在平级的地方分别定义，只是在逻辑上进行嵌套使用：
```jsx
// 在外面分别定义
function Child() {
  return <p>我是子组件</p>;
}

function Parent() {
  // 在里面直接使用
  return <Child />;
}
```

---

## 动手练习

我们去这个网站测试一下组件的嵌套和复用：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`：

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';

// 1. 定义一个“卡片”组件 (Card)
// 注意：首字母大写！
function Card() {
  const cardStyle = {
    border: '1px solid #e0e0e0',
    borderRadius: '8px',
    padding: '16px',
    margin: '12px 0',
    boxShadow: '0 4px 6px rgba(0,0,0,0.05)',
    backgroundColor: '#fff'
  };

  return (
    <div style={cardStyle}>
      <h3>这是一个卡片组件</h3>
      <p>我是被封装好的内容。因为被做成了组件，你可以无限次地复用我，而不用重复写样式代码！</p>
    </div>
  );
}

// 2. 主组件 (App)
function App() {
  return (
    <div style={{ padding: '24px', fontFamily: 'sans-serif', backgroundColor: '#f9f9f9' }}>
      <h1>我的个人中心</h1>
      <p>下面是我复用了三次的 Card 组件：</p>

      {/* 3. 使用写好的组件，感受复用的魅力 */}
      <Card />
      <Card />
      <Card />
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

在右侧 Preview 窗口中，你会看到页面上整齐地排着三个一模一样的精美卡片。

---

## 思考一个问题

现在的卡片虽然很方便，但你很快会发现一个致命痛点：

> “这三个卡片里的文字和标题**全是一模一样**的。如果我想让第一个卡片显示‘新闻’，第二个卡片显示‘体育’，第三个卡片显示‘娱乐’，该怎么办呢？”

为了解决“让同一个组件在不同地方显示不同内容”的问题，我们需要给组件“传参”。

这就是我们明天要学的：**第 4 天：Props 到底是什么？**
