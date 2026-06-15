## 第 1 天：React 到底是什么？

很多新手会认为：

> React 是一个前端框架。

实际上 React 官方更喜欢称自己为：

> 用于构建用户界面的 JavaScript 库（Library）。


React 只负责：

* 状态（State）
* UI（View）
* 更新（Render）

例如：

```jsx
function Counter() {
  const [count, setCount] = React.useState(0)

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  )
}
```

这里：

```jsx
count = 0
```

↓

点击按钮

↓

```jsx
count = 1
```

↓

React 自动刷新界面

你不用：

```js
document.getElementById(...)
element.innerText = ...
```

React 会帮你完成。

---

## 为什么 React 会火？

在 React 出现之前：

```html
<div id="counter">0</div>
```

```js
const el = document.getElementById("counter")

el.innerText = Number(el.innerText) + 1
```

开发者需要手动操作 DOM。

项目大了以后：

```text
修改数据
↓
找到DOM
↓
修改DOM
↓
同步状态
↓
防止出Bug
```

非常痛苦。

React 提出：

```text
数据 => UI
```

只关心数据。

例如：

```jsx
count = 5
```

React：

```html
<button>5</button>
```

自动生成。

---

## 今天记住一句话

React 的核心思想：

> UI = f(state)

即：

```text
界面 = 数据的函数
```

当数据变化时：

```text
旧UI
↓
新State
↓
React重新计算
↓
新UI
```

开发者只需要维护 State。

---

## 动手练习

去这个网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面代码复制到index.jsx：

```jsx
// 1. 引入 React 和 ReactDOM 
import React, { useState } from 'react';
import { createRoot } from 'react-dom/client';

// 2. 定义一个组件 (App)
function App() {
  // 这就是我们的 State（数据）
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>当前数字：{count}</h2>
      
      {/* 点击按钮，修改数据，React 会自动帮你更新页面 */}
      <button onClick={() => setCount(count + 1)}>
        点我 + 1
      </button>
    </div>
  );
}

// 3. 找到 HTML 中的挂载点并渲染
// 确保 index.html 中有 <div id="root"></div>
const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

切换到 Preview 并尝试点击按钮：

看看页面是否立即变化。

<img width="1920" height="971" alt="image" src="https://github.com/user-attachments/assets/fadd0c35-7e8a-4084-a11b-7b811bdbebd4" />



其中的 const [count, setCount] = useState(0); 是什么意思？


这行代码是 React 中**最核心、最常用**的魔法！你可以把它理解为**向 React 申请了一个“有特殊功能的数据盒子”**。

对于新手，我们把它拆解成 3 个部分来理解：

```jsx
const [count, setCount] = useState(0);
```

### 1. `useState(0)`：制造盒子并放入初始值
* `useState` 是 React 提供的一个“锦囊”（官方叫 Hook）。
* 括号里的 `0` 是**初始值**。
* 这句话的意思是：**“React，请帮我创建一个数据，初始默认值是 0。”**

### 2. `count`：当前的数据
* 它就像是盒子里的物品，代表**当前的数字**。
* 刚开始的时候，`count` 就是 0。
* 你只能**读取**它，把它展示在页面上，比如 `<h2>当前数字：{count}</h2>`。

### 3. `setCount`：修改数据的“遥控器”
* 在 React 里，你**绝对不能**直接修改数据（千万别写 `count = count + 1`，React 会罢工）。
* `setCount` 是 React 配套送给你的**专属遥控器**（一个函数）。
* 只有按下遥控器，比如告诉它 `setCount(1)`，React 才会知道：“哦！数据变了！我要赶紧去刷新一下页面！”

---

### 💡 举个现实中的例子：看电视

把这行代码想象成你在**买一台电视机**：

* `useState(0)` = 你买了一台电视，出厂默认停在 **0 频道**。
* `count` = 电视机屏幕，告诉你当前正在播放 **第 0 频道**。
* `setCount` = 电视机的**遥控器**。你想换台，不能直接用手去抠屏幕（不能 `count = 1`），必须按遥控器 `setCount(1)`。

### ❓ 为什么外面要套一个方括号 `[]`？
这是 JavaScript（ES6语法）的一种简写方式，叫**数组解构**。
因为 `useState` 这个锦囊每次都会固定返回**两个东西**：
1. 数据本身
2. 修改数据的函数

用 `[]` 包起来，就是为了给这俩东西**随便起个名字**。
你完全可以改成这样（虽然不建议，但代码照样能跑）：
```jsx
// 给数据起名叫 apple，修改工具叫 setApple
const [apple, setApple] = useState(0);

// 那么按钮里就要写：
<button onClick={() => setApple(apple + 1)}>
```
*业界潜规则：通常把遥控器命名为 `set + 数据名`，且首字母大写，比如 `count` 和 `setCount`，`age` 和 `setAge`。*

### 总结
这行代码完美体现了第一天学的核心思想 **`UI = f(state)`**：
你用 `setCount` 改变了 `count`（改变了 State），React 就会自动重新计算，把最新的数字显示在页面上（更新了 UI）。



---

## 明天可以学什么？

建议按这个顺序：

1. JSX 是什么
2. React 组件
3. Props
4. State
5. useState
6. 事件处理
7. 条件渲染
8. 列表渲染
9. useEffect
10. 组件生命周期
11. 自定义 Hook
12. Context
13. React Router
14. TypeScript + React
15. React 项目结构
