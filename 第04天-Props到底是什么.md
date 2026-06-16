## 第 4 天：Props 到底是什么？

昨天我们在结束时留下了一个非常现实的痛点：

> 我们用同一个 `Card` 积木拼了三次，但里面的标题和内容**全是一模一样**的。
>
> 这种“复制粘贴”显然不能满足真实的网页需求。我们希望每个卡片能展示不同的数据。

在 React 中，解决这个问题的秘诀就是 **Props**。

---

## 什么是 Props？

**Props** 是英文 **Properties（属性）** 的缩写。

你可以把它理解为**从外面传给组件的“定制化参数”**。

### 💡 一个绝妙的类比：定制 T 恤

把组件想象成一个**“印花 T 恤生产线”**：

* **不使用 Props 的组件**：这条生产线不接受外界指令，不管谁来买，它永远只生产**印着“白色默认”的白色 T 恤**。
* **使用 Props 的组件**：这条生产线接受外界的定制参数（Props）。你给它传一个 `color="red"` 和 `text="绝不加班"`，它就能为你生产出一件专属的红色 T 恤。

### 💡 另一个程序员视角：函数的参数

在第一天我们学过，React 的核心思想是 `UI = f(state)`，组件本质上就是一个 JavaScript 函数。

在普通的 JavaScript 中，我们这样给函数传参：

```js
// 定义函数
function greet(name) {
  return "你好，" + name;
}

// 调用函数并传参
greet("张三"); // 输出 "你好，张三"
greet("李四"); // 输出 "你好，李四"
```

在 React 中，给组件传参（即传递 Props）的形式是一模一样的，只是换成了 JSX 的语法：

```jsx
// 调用组件并传参（也就是传递 Props）
<Greet name="张三" />
<Greet name="李四" />
```

---

## 如何在代码中接收和使用 Props？

在 React 中，传递和接收 Props 只需要两步：

### 第一步：父组件“传递”数据

在引用子组件的地方，像写 HTML 属性一样把数据传过去：

```jsx
function App() {
  return (
    <div>
      {/* 传了两个参数：title 和 category */}
      <Card title="今日新闻" category="热点" />
      <Card title="詹姆斯夺冠" category="体育" />
    </div>
  );
}
```

### 第二步：子组件“接收”并展示数据

子组件函数的**第一个参数**，就是 React 自动打包好送给你的 `props` 对象：

```jsx
// 1. 在参数里接收 props 对象
function Card(props) {
  // 此时 props = { title: "今日新闻", category: "热点" }
  return (
    <div className="card">
      {/* 2. 用大括号 {} 提取并展示数据 */}
      <span>【{props.category}】</span>
      <h3>{props.title}</h3>
    </div>
  );
}
```

### 🌟 现代 React 推荐写法：解构赋值（Destructuring）

上面的 `props.xxx` 写起来稍微有点繁琐。现代前端开发更喜欢直接在参数栏把我们要的属性“解开”：

```jsx
// 直接在大括号里写我们要的参数名字，更直观、更干净！
function Card({ title, category }) {
  return (
    <div className="card">
      <span>【{category}】</span>
      <h3>{title}</h3>
    </div>
  );
}
```

---

## ⚠️ 必须死记硬背的铁律：Props 是“只读”的！

在 React 中，有一条绝对不能触碰的红线：**组件永远不能修改自己收到的 Props**。

这被称为**单向数据流（One-Way Data Flow）**：数据只能从父组件像瀑布一样向下流给子组件，子组件只能看，不能改。

❌ **绝对错误的写法：**
```jsx
function Card({ title }) {
  // 试图强行修改传进来的参数，React 会直接报错！
  title = "新标题"; 
  
  return <h3>{title}</h3>;
}
```
如果你需要让数据可以在组件内部被修改、被更新，那就不能用 Props，而应该用我们第一天提到过的 **State（状态）**。

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx` 中，感受 Props 的魔法：

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';

// 1. 定义 Card 组件，使用解构赋值接收三个 Props
function Card({ title, description, color }) {
  const cardStyle = {
    border: `2px solid ${color}`, // 动态使用传进来的颜色
    borderRadius: '8px',
    padding: '16px',
    margin: '12px 0',
    backgroundColor: '#354449ff',
    borderLeft: `8px solid ${color}` // 炫酷的左侧边框颜色
  };

  return (
    <div style={cardStyle}>
      <h3 style={{ color: color }}>{title}</h3>
      <p>{description}</p>
    </div>
  );
}

// 2. 在 App 组件中，用不同的数据多次调用 Card
function App() {
  return (
    <div style={{ padding: '24px', fontFamily: 'sans-serif' }}>
      <h1>今日资讯栏目</h1>
      <p>每个卡片都是同一个组件，但因为传了不同的 Props，呈现出了完全不同的内容：</p>

      {/* 传不同的标题、描述和颜色 */}
      <Card 
        title="1. 前端技术周刊" 
        description="Svelte 5 推出全新状态机制 Runes，前端圈又热闹起来了。" 
        color="#3b82f6" // 蓝色
      />
      
      <Card 
        title="2. 体育热点追踪" 
        description="昨晚的精彩比赛中，客队完成惊天大逆转，顺利晋级决赛。" 
        color="#ef4444" // 红色
      />
      
      <Card 
        title="3. 轻松一刻" 
        description="为什么程序员喜欢冷色调？因为冷色调能让发热的 CPU 看起来凉快一点。" 
        color="#10b981" // 绿色
      />
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

在右侧预览窗口中，你会看到三个颜色各异、内容完全不同的精美卡片。这就是 Props 的威力！

---

## 明天可以学什么？

现在我们已经学会了：
* 如何用 **Components** 搭建静态积木。
* 如何用 **Props** 给积木涂上不同的颜色和文字。

但这些网页依然是“死”的，用户跟它没有真正的互动。如果我们想让用户点击按钮，卡片自己切换颜色，或者动态增加一个新卡片，我们要怎么做？

明天我们继续深入：**第 5 天：State（状态）到底是什么？**
