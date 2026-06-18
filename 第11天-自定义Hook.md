## 第 11 天：自定义 Hook（Custom Hooks）——提取你的专属“代码魔法”

在过去的几天里，我们已经掌握了 React 的大部分核心技能。你现在可以熟练地在组件里使用 `useState` 来记录状态，用 `useEffect` 来发请求或监听事件。

但随着项目越来越大，你会发现一个让人头疼的现象：**代码重复**。

比如，你在“商品展示组件”里写了 `useState` 和 `useEffect` 来监听窗口大小以调整布局；接着，你在“数据图表组件”里，**又把一模一样的监听窗口代码复制粘贴了一遍**。

为了解决“**跨组件复用状态逻辑**”的问题，React 提供了一个非常优雅的高级武器：**自定义 Hook（Custom Hooks）**。

---

## 什么是自定义 Hook？

### 💡 一个生动的比喻：调料包与共享厨房

* **普通组件**：像是一个完整的菜品（比如一盘鱼香肉丝），它既管切菜、调味（逻辑），又管最后的装盘（UI 渲染）。
* **自定义 Hook**：就像是厨师提前调配好的**“鱼香肉丝万能调料包”**。它不负责具体装在哪个盘子里，它只打包了“调味和配比”的逻辑。不管谁想做鱼香肉丝，直接拆开调料包（调用这个 Hook）就能用。

在代码中，**自定义 Hook 本质上就是一个名字以 `use` 开头的普通 JavaScript 函数**，在这个函数内部，你可以自由地调用其他的 React Hook（如 `useState`, `useEffect`）。

---

## 核心规则

1. **函数名必须以 `use` 开头**：比如 `useFetch`、`useWindowWidth`。这是 React 官方的硬性规定。只有这样，React 才能识别它是一个 Hook，并对它应用 Hook 的语法安全检查。
2. **只共享“逻辑”，不共享“数据状态”**：如果组件 A 和组件 B 同时调用了同一个自定义 Hook `useCounter()`，它们的数据是**完全独立、互不干扰**的。组件 A 里的数字加 1，绝不会影响到组件 B。

---

## 实战案例：创造一个 `useWindowWidth`（窗口宽度监听器）

我们想写一个功能：实时获取当前浏览器的窗口宽度。如果不封装，我们需要在每个组件里写如下繁琐的代码：

```jsx
// 每次都要写这一大堆：
const [width, setWidth] = useState(window.innerWidth);
useEffect(() => {
  const handleResize = () => setWidth(window.innerWidth);
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize); // 别忘了昨天学的清理函数！
}, []);
```

现在，我们把这段逻辑打包，做成一个**自定义 Hook**：

```jsx
import { useState, useEffect } from 'react';

// 1. 声明一个 use 开头的函数
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);
    
    // 清理函数
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  // 2. 返回你需要暴露给外部组件的数据
  return width;
}
```

任何组件想要知道窗口宽度，只需要极其优雅地写一行代码：

```jsx
const width = useWindowWidth();
```

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`：

```jsx
import React, { useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

// 🛠️ 1. 自定义 Hook：监听窗口宽度
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);
    
    // 昨天学的生命周期清理函数，防止内存泄漏
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return width; // 返回当前的宽度数字
}

// 2. 使用刚才造的自定义 Hook 渲染主页面
function App() {
  // 调用自定义 Hook，直接拿到宽度
  const width = useWindowWidth();

  // 样式定义
  const boxStyle = {
    border: '1px solid #cbd5e1',
    borderRadius: '16px',
    padding: '24px',
    maxWidth: '400px',
    margin: '30px auto',
    boxShadow: '0 10px 15px -3px rgba(0,0,0,0.1)',
    fontFamily: 'sans-serif',
    backgroundColor: '#fff',
    transition: 'background-color 0.3s ease'
  };

  // 根据当前宽度，判断属于手机端还是电脑端布局
  const isMobile = width < 768;

  return (
    <div style={{ 
      ...boxStyle, 
      backgroundColor: isMobile ? '#fef2f2' : '#f0fdf4' // 手机端粉红色背景，电脑端淡绿色背景
    }}>
      <h2>🖥️ 响应式布局检测器</h2>
      <p style={{ color: '#475569' }}>
        我们把复杂的窗口 resize 监听事件，封装进了一个名为 <code>useWindowWidth</code> 的自定义 Hook 中。
      </p>

      <div style={{ padding: '16px', backgroundColor: '#fff', borderRadius: '8px', border: '1px solid #e2e8f0', marginTop: '20px' }}>
        <p style={{ margin: '0 0 8px 0' }}>当前窗口实际宽度：</p>
        <p style={{ fontSize: '28px', fontWeight: 'bold', margin: 0, color: isMobile ? '#dc2626' : '#16a34a' }}>
          {width} px
        </p>
      </div>

      <div style={{ marginTop: '16px', fontWeight: 'bold' }}>
        设备判定：{isMobile ? '📱 手机端布局（Width < 768px）' : '💻 电脑端布局（Width >= 768px）'}
      </div>
      
      <p style={{ fontSize: '13px', color: '#64748b', marginTop: '12px' }}>
        💡 提示：尝试拖动缩放你的浏览器窗口，你会发现背景色和设备判定会实时、丝滑地发生切换！
      </p>
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. **拖拽缩放你的浏览器窗口**（或者在调试模式下切换手机端视图）。
2. 你会发现，卡片背景色和设备判定在宽度跨越 `768px` 时，非常灵敏、顺滑地完成了切换。
3. 仔细感受一下 `App` 组件的代码有多么干净！原本复杂的事件监听、生命周期挂载、死亡卸载，全部消失了，只剩下了一行优美的 `const width = useWindowWidth();`。

这就是**自定义 Hook** 的强大和魅力。

---

## 明天可以学什么？

使用自定义 Hook，我们已经能够将逻辑提取和封装得极其优雅。

但随着组件层级越拼越深（例如：`App` 嵌套 `Main`，`Main` 嵌套 `List`，`List` 嵌套 `Card`），如果我们想把最顶层的 `App` 里的一个“全局主题颜色（Theme）”或“用户登录信息”传给最底层的 `Card`，我们需要把 Props 一层一层地手动往下传递。这在 React 中被称为 **“Props 钻孔（Props Drilling）”**，非常痛苦。

明天我们来学习 React 官方提供的一条专门跨越千山万水传递数据的“高速地下铁”：**第 12 天：Context（上下文）——干掉繁琐的逐层传参**
