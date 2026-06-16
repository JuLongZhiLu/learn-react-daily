## 第 5 天：State（状态）到底是什么？

昨天我们学习了 **Props**，知道它是从外部传进来的“定制化参数”。但 Props 有一个致命的限制：**它是只读的，组件内部绝对不能修改它。**

但是在真实的网页中，界面必须是“活的”：
* 点击“点赞”按钮，点赞数要加 1。
* 点击“展开”按钮，折叠的内容要显示出来。
* 输入账号密码，文本框里的字要跟着变化。

这些**会随着用户操作而发生改变的数据**，在 React 中被称为 **State（状态）**。

---

## Props 与 State 的核心区别

初学者最容易混淆这两者。我们用一个非常通俗的例子来区分：

| 维度 | Props（属性） | State（状态） |
| :--- | :--- | :--- |
| **打个比方** | 你的**身高、基因、出生地**（外部赋予你的，你无法在自己内部修改它们） | 你的**心情、饱腹感、存款**（你私有的，会随着你的日常活动不断发生变化） |
| **数据来源** | **外部传入**（由父组件决定） | **内部自建**（由组件自己管理） |
| **可否修改** | **只读**（绝对不能在组件内部修改） | **可读可写**（必须通过专属函数来修改） |

---

## 深入拆解 `useState`

在第 1 天，我们曾快速体验过 `useState`。今天我们来彻底理清它的工作原理。

要让一个数据成为“有魔法的 State”，我们需要向 React 申请：

```jsx
import React, { useState } from 'react';

const [count, setCount] = useState(0);
```

我们把这行代码拆成 4 个关键角色：

1. **`useState`（魔法制造器）**：这是 React 提供的一个 Hook（钩子函数）。它的作用是声明一个由 React 托管的状态数据。
2. **`0`（初始值）**：传给 `useState` 的参数。这里我们让初始的 `count` 为 0。
3. **`count`（状态变量）**：当前时刻的数据。你在界面上可以直接读取它，比如 `<h2>{count}</h2>`。
4. **`setCount`（更新遥控器）**：修改这个数据的**唯一通道**。

---

## 为什么不能直接修改变量？

很多习惯了原生 JS 的同学，经常会犯这样一个错误：

❌ **绝对错误的写法：**
```jsx
function App() {
  let count = 0;

  function handleClick() {
    count = count + 1; // 强行直接修改变量
    console.log(count); // 控制台里的数字确实变了，但页面毫无反应！
  }

  return <button onClick={handleClick}>{count}</button>;
}
```

### 为什么页面不刷新？
因为普通的 JavaScript 变量改变时，**React 根本不知道**。

在 React 中，界面更新的唯一公式是：**`UI = f(state)`**。
当你使用遥控器 `setCount(count + 1)` 时，React 内部其实偷偷做了两件事：
1. **更新数据**：把 `count` 的值从 `0` 改成 `1`。
2. **触发重新渲染（Re-render）**：React 会把你的组件函数重新执行一遍。因为 `count` 变成了 1，重新执行后渲染出来的 HTML 也就变成了 `1`。

**记住：只有调用 `set` 开头的更新函数，页面才会刷新！**

---

## 动手练习

我们去 React Playground 亲自体验一下 State 的神奇力量：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`：

```jsx
import React, { useState } from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  // 1. 声明一个数字类型的状态（点赞数）
  const [likes, setLikes] = useState(0);

  // 2. 声明一个布尔类型的状态（是否展开详情）
  const [isOpen, setIsOpen] = useState(false);

  // 样式定义
  const boxStyle = {
    border: '1px solid #ddd',
    borderRadius: '12px',
    padding: '24px',
    maxWidth: '400px',
    margin: '20px auto',
    boxShadow: '0 4px 12px rgba(0,0,0,0.08)',
    fontFamily: 'sans-serif'
  };

  const buttonStyle = {
    backgroundColor: '#3b82f6',
    color: 'white',
    border: 'none',
    padding: '8px 16px',
    borderRadius: '6px',
    cursor: 'pointer',
    marginRight: '10px'
  };

  return (
    <div style={boxStyle}>
      <h2>🚀 React 知识卡片</h2>
      <p>今天我们学习了 React 的核心概念：State。</p>

      {/* 点击按钮，调用 setLikes 遥控器，数据 +1，页面立刻自动刷新 */}
      <button style={buttonStyle} onClick={() => setLikes(likes + 1)}>
        ❤️ 点赞 {likes}
      </button>

      {/* 点击按钮，将 isOpen 的布尔值取反（如果是 true 变成 false，反之亦然） */}
      <button 
        style={{ ...buttonStyle, backgroundColor: '#10b981' }} 
        onClick={() => setIsOpen(!isOpen)}
      >
        {isOpen ? '收起详情 ▲' : '展开详情 ▼'}
      </button>

      {/* 根据 isOpen 的状态，决定是否渲染下面这段文字（条件渲染） */}
      {isOpen && (
        <div style={{ marginTop: '16px', padding: '12px', backgroundColor: '#36486bff', borderRadius: '6px' }}>
          <h4>💡 深度解析：</h4>
          <p>State 就像是组件的“私人记忆”。只有通过它的专属遥控器去更新它，React 才会重新计算并刷新屏幕。</p>
        </div>
      )}
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中点击“点赞”和“展开详情”：
* 你会发现，数据在发生变化的同时，页面上的数字也实时增加。
* 当你点击展开时，底部的深度解析文字会神奇地出现或消失。

这就是 **State** 赋予以网页实时动态交互的魔力。

---

## 明天可以学什么？

现在我们已经把 React 最难跨越的三座大山搞定了：
1. **JSX**（如何写界面）
2. **Components**（如何拼积木）
3. **Props & State**（数据如何流动和变化）

今天你在练习中可能已经注意到了这行代码：`onClick={() => setLikes(likes + 1)}`。
这种点击事件是如何运作的？除了点击，我们如何处理输入框的变化、表单的提交？

明天我们继续：**第 6 天：React 中的事件处理（Event Handling）**
