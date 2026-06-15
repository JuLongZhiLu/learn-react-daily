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
import React, { useState } from 'react';
import { createRoot } from 'react-dom/client';

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

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

切换到 Preview 并尝试点击按钮：

看看页面是否立即变化。

<img width="1920" height="971" alt="image" src="https://github.com/user-attachments/assets/fadd0c35-7e8a-4084-a11b-7b811bdbebd4" />


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
