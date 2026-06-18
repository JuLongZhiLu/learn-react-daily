## 第 10 天：React 组件的生命周期（Lifecycle）

在昨天我们学习 `useEffect` 时，提到过“有些代码只在页面第一次加载完毕后执行一次”。

实际上，每一个 React 组件从出现在屏幕上，到最后从屏幕上消失，都经历了一段完整的“生命历程”。这就是**组件生命周期（Component Lifecycle）**。

理解生命周期，能帮你写出性能更高、没有内存泄漏（Memory Leak）的高质量代码。今天，我们用“生老病死”的自然规律，来彻底搞懂 React 组件的生命周期。

---

## 什么是生命周期？

你可以把一个组件想象成一个**生命体**，它的一生会经历三个最核心的阶段：

```text
  挂载（出生）            更新（成长）            卸载（死亡）
 ┌────────────┐        ┌────────────┐        ┌────────────┐
 │  Mounting  │  ───>  │  Updating  │  ───>  │ Unmounting │
 └────────────┘        └────────────┘        └────────────┘
 插入到页面 DOM 中       State/Props 变化        从页面 DOM 中移除
```

在旧版本的 React（类组件 Class Component）中，开发者需要写非常复杂的生命周期函数（如 `componentDidMount`、`componentWillUnmount`）。

但在现代 React（函数组件）中，我们不需要那些复杂的函数，**仅靠一个 `useEffect` 就能完美掌管这三个阶段！**

---

## 三个阶段的 `useEffect` 写法

### 阶段一：挂载阶段（Mounting / 出生）
组件被创建并第一次插入到 HTML 页面中的过程。
* **常见场景**：去后台服务器请求数据、开启定时器、绑定全局键盘事件。
* **代码写法**（依赖数组为空 `[]`）：
  ```jsx
  useEffect(() => {
    console.log("👶 组件出生了（挂载完毕）！");
  }, []); // 💡 空数组保证它只在“出生”时运行一次
  ```

### 阶段二：更新阶段（Updating / 运行与成长）
组件已经在页面上，因为 State（状态）或 Props（属性）发生改变，导致组件重新渲染的过程。
* **常见场景**：当输入框改变时自动保存、当页码改变时重新获取对应页面的数据。
* **代码写法**（依赖数组里写需要监听的变量 `[count]`）：
  ```jsx
  useEffect(() => {
    console.log("📈 变量 count 变了，组件重新长大了（更新了）！");
  }, [count]); // 💡 只有 count 改变时才触发
  ```

### 阶段三：卸载与清理（Unmounting & Cleanup / 死亡与善后）
组件因为某些条件（比如条件渲染）被从页面上移出、销毁的过程。
* **常见场景**：**非常关键！** 清除开启的定时器、解绑全局事件监听、取消未完成的网络请求。
* **代码写法**：在 `useEffect` 的回调函数中，**`return` 一个清理函数**。

```jsx
useEffect(() => {
  // 1. 挂载时做的事
  console.log("👶 组件出生了");

  // 2. 死亡时（卸载时）做的事：返回一个清理函数
  return () => {
    console.log("💀 组件死亡了（卸载完毕），清理战场，防止内存泄漏！");
  };
}, []);
```

---

## ⚠️ 核心避坑指南：为什么要进行“清理（Cleanup）”？

初学者最容易写出**“内存泄漏（Memory Leak）”**的代码，导致浏览器越运行越慢甚至卡死。

比如，你在一个组件“出生”时开启了一个每秒执行一次的定时器（`setInterval`）。如果组件被销毁（死亡）时，你**没有清理这个定时器**：
* 这个定时器就会像幽灵一样，继续在后台悄悄运行，消耗电脑 CPU。
* 如果这个组件反复加载、销毁 100 次，后台就会有 100 个幽灵定时器在同时疯狂运行！

**所以：任何在“挂载”时启动的全局任务、定时器、全局监听，都必须在“清理函数”中彻底消灭。**

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`，来亲眼见证组件的“生”与“死”，以及清理函数的必要性：

```jsx
import React, { useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

// 1. 这是一个独立的“定时器”子组件
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    // --- 👶 挂载阶段 ---
    console.log('👶 [Timer] 组件出生了！开启每秒 +1 的定时器。');
    
    // 开启定时器，并保存它的 ID
    const intervalId = setInterval(() => {
      setSeconds((prevSeconds) => prevSeconds + 1);
    }, 1000);

    // --- 💀 卸载阶段（清理函数） ---
    // 💡 极其重要：当 Timer 组件被隐藏（死亡）时，React 会自动调用这个返回的函数
    return () => {
      console.log('💀 [Timer] 组件死亡了！正在为您彻底销毁定时器。');
      clearInterval(intervalId); // 彻底杀掉定时器，防止内存泄漏
    };
  }, []); // 空数组保证只在挂载和卸载时执行

  // --- 📈 更新阶段 ---
  useEffect(() => {
    console.log(`📈 [Timer] 发生更新，秒数变为：${seconds}`);
  }, [seconds]); // 只要 seconds 变了，就会触发

  return (
    <div style={{ padding: '20px', backgroundColor: '#eff6ff', borderRadius: '8px', textAlign: 'center', marginTop: '16px', border: '1px solid #bfdbfe' }}>
      <h3 style={{ color: '#1e40af', margin: '0 0 10px 0' }}>⏱️ 幽灵定时器正在运行</h3>
      <p style={{ fontSize: '32px', fontWeight: 'bold', color: '#2563eb', margin: 0 }}>{seconds} 秒</p>
    </div>
  );
}

// 2. 主组件 (App)
function App() {
  const [showTimer, setShowTimer] = useState(false);

  const boxStyle = {
    border: '1px solid #cbd5e1',
    borderRadius: '16px',
    padding: '24px',
    maxWidth: '400px',
    margin: '30px auto',
    boxShadow: '0 10px 15px -3px rgba(0,0,0,0.1)',
    fontFamily: 'sans-serif',
    backgroundColor: '#fff'
  };

  const btnStyle = {
    backgroundColor: showTimer ? '#ef4444' : '#10b981',
    color: 'white',
    border: 'none',
    padding: '12px 16px',
    borderRadius: '8px',
    cursor: 'pointer',
    fontWeight: 'bold',
    width: '100%',
    fontSize: '16px'
  };

  return (
    <div style={boxStyle}>
      <h2>🧬 组件生命周期演示</h2>
      <p style={{ color: '#64748b', fontSize: '14px', marginBottom: '20px' }}>
        点击下方按钮，手动控制 Timer 组件的“出生（挂载）”与“死亡（卸载）”：
      </p>

      {/* 切换 showTimer 的值 */}
      <button style={btnStyle} onClick={() => setShowTimer(!showTimer)}>
        {showTimer ? '毁灭（卸载）定时器 💀' : '创造（挂载）定时器 👶'}
      </button>

      {/* 条件渲染：当 showTimer 为 true 时渲染 Timer 组件 */}
      {showTimer && <Timer />}
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. **打开你浏览器的开发者工具控制台（F12 -> Console）**。
2. 点击“创造定时器”：你会看到控制台打印出 `👶 [Timer] 组件出生了！`。定时器开始读秒，同时控制台每秒都会打印出 `📈 [Timer] 发生更新`。
3. 点击“毁灭定时器”：你会看到控制台立刻打印出 `💀 [Timer] 组件死亡了！正在为您彻底销毁定时器。`，且页面读秒停止。
4. 观察控制台，读秒停止后，后台再也没有任何输出。这证明**清理函数成功把后台的定时器彻底干掉了**。

---

## 明天可以学什么？

恭喜你！今天你补齐了 React 开发中极具深度的一块核心知识版图。

写到今天，我们掌握的所有 `useState` 都是**只能在组件内部自己玩**的私有数据。
但是在真实的复杂项目中，如果 `App` 组件里有一个“用户登录状态”，而深埋在最底层的 `Card` 组件里的“点赞按钮”也需要知道用户是否登录，我们该怎么把数据跨越千山万水传给它？

明天我们来攻克组件间通信的高级武器：**第 11 天：自定义 Hook 与组件通信**
