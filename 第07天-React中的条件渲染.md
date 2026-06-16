## 第 7 天：React 中的条件渲染（Conditional Rendering）

在实际的应用开发中，界面的展示绝不是一成不变的。我们要根据不同的情况，让界面显示不同的内容：
* 用户已登录，显示**“退出登录”**；未登录，显示**“立即登录”**。
* 页面正在加载，显示**“转圈动画”**；加载完毕，显示**“真实内容”**。
* 购物车是空的，显示**“购物车空空如也，去逛逛”**；有商品，显示**“商品列表和结算按钮”**。

在 Vue 中，我们可能需要学习特殊的指令如 `v-if` 和 `v-show`。但在 React 中，**完全没有特殊的条件渲染指令**。因为 React 遵循“用纯 JavaScript 写 UI”的原则，我们直接使用 JS 的标准语法就能搞定条件渲染。

今天，我们来理清 React 中最常用的条件渲染三大利器，以及避开一个初学者几乎人人都踩过的“怪异”陷阱。

---

## 条件渲染的三大利器

根据业务场景的不同，我们有三种最常用的写法：

### 利器一：If-Else 语句（适合整块页面的大变动）
如果你的页面因为一个状态，导致**整个结构都要发生彻底改变**，那么在 `return` 之前使用最标准的 `if-else` 是最清晰的写法。

```jsx
function Dashboard({ isLoggedIn }) {
  // 在 return 前用 if-else 判断
  if (isLoggedIn) {
    return <AdminPanel />; // 渲染管理员面板
  } else {
    return <LoginForm />; // 渲染登录表单
  }
}
```

### 利器二：三元运算符 `? :`（适合“二选一”的局部变动）
如果你只想在 JSX 内部某个小地方进行“如果是 A 就显示 A，否则显示 B”的微调，可以使用三元运算符。

```jsx
function Welcome({ username }) {
  return (
    <div>
      {/* 局部判断：如果是会员显示金牌，普通用户显示银牌 */}
      <h3>等级：{username === 'VIP' ? '🏅 金牌会员' : '🥈 银牌会员'}</h3>
    </div>
  );
}
```

### 利器三：逻辑与运算符 `&&`（适合“有或没有”的简单开关）
如果你只想在“当某个条件成立时，就显示一个东西；如果不成立，什么都不显示”，`&&` 是最优雅的写法。

```jsx
function Alert({ hasError }) {
  return (
    <div>
      {/* 如果 hasError 是 true，就渲染右边的 Div；如果是 false，什么都不渲染 */}
      {hasError && <div className="error-box">系统错误，请重试！</div>}
    </div>
  );
}
```

---

## ⚠️ 核心避坑指南：小心让页面渲染出一个“0”

在使用 `&&` 运算符时，React 社区有一个非常经典且隐蔽的陷阱。

请看下面这段代码：

```jsx
const unreadMessagesCount = 0;

return (
  <div>
    {/* 我们本意是：如果有未读消息，就显示提示字。如果是 0，就不显示。 */}
    {unreadMessagesCount && <p>你有新的消息！</p>}
  </div>
);
```

### 实际效果是什么？
你以为页面上什么都不会显示，但实际上，页面会结结实实地渲染出一个字符：**`0`**！

### 为什么会这样？
因为 JavaScript 在运行 `&&` 时，如果左边的值是假值（Falsy），它会返回左边这个值本身。
由于 `0` 是假值，`0 && ...` 的运行结果直接就是数字 `0`。
而对于 React 来说，**数字 `0` 是一个有效的可渲染内容**，所以 React 会把它乖乖印到屏幕上。

### 💡 解决方案
如果你需要判断数字，请务必将其转换为真正的**布尔值（Boolean）**后再进行 `&&` 判断：

```jsx
// 方案 A：显式进行大小判断（最推荐，安全无痛）
{unreadMessagesCount > 0 && <p>你有新的消息！</p>}

// 方案 B：双感叹号强转布尔值
{!!unreadMessagesCount && <p>你有新的消息！</p>}
```

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`，来亲自体验这三种条件渲染的魅力：

```jsx
import React, { useState } from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const [cartItemsCount, setCartItemsCount] = useState(0);

  // 模拟点击登录后的加载效果
  function handleLogin() {
    setIsLoading(true);
    // 模拟 1.5 秒后加载完毕并成功登录
    setTimeout(() => {
      setIsLoading(false);
      setIsLoggedIn(true);
    }, 1500);
  }

  // 样式定义
  const boxStyle = {
    border: '1px solid #cbd5e1',
    borderRadius: '16px',
    padding: '24px',
    maxWidth: '400px',
    margin: '30px auto',
    boxShadow: '0 10px 15px -3px rgba(0,0,0,0.1)',
    fontFamily: 'sans-serif'
  };

  const btnStyle = {
    backgroundColor: '#3b82f6',
    color: 'white',
    border: 'none',
    padding: '10px 16px',
    borderRadius: '8px',
    cursor: 'pointer',
    fontWeight: 'bold',
    width: '100%'
  };

  // --- 利器一：If-Else 语句的应用 ---
  // 如果正在加载，渲染整块加载动画页面
  if (isLoading) {
    return (
      <div style={boxStyle}>
        <div style={{ textAlign: 'center', padding: '40px 0' }}>
          <div style={{ fontSize: '32px', animation: 'spin 1s linear infinite' }}>⏳</div>
          <p style={{ color: '#64748b', marginTop: '12px' }}>正在为您加载安全登录通道...</p>
        </div>
      </div>
    );
  }

  return (
    <div style={boxStyle}>
      {/* --- 利器二：三元运算符的应用（二选一） --- */}
      {isLoggedIn ? (
        // 已登录界面
        <div>
          <h2 style={{ color: '#10b981' }}>🎉 登录成功！</h2>
          <p>欢迎回到您的专属工作台。</p>
          
          <div style={{ padding: '16px', backgroundColor: '#374450ff', borderRadius: '8px', marginBottom: '16px' }}>
            <p style={{ margin: '0 0 10px 0' }}>🛒 我的购物车里有 <b>{cartItemsCount}</b> 件商品</p>
            
            {/* 模拟添加/清空商品的操作 */}
            <button onClick={() => setCartItemsCount(cartItemsCount + 1)}>添加商品</button>
            <button style={{ marginLeft: '10px' }} onClick={() => setCartItemsCount(0)}>清空购物车</button>
          </div>

          {/* --- 利器三：逻辑与 && 的应用（有或没有） --- */}
          {/* 避坑技巧：用大于 0 判断，防止购物车为 0 时页面显示出数字 0 */}
          {cartItemsCount > 0 && (
            <div style={{ padding: '10px', backgroundColor: '#fef3c7', borderRadius: '6px', color: '#b45309', fontSize: '14px' }}>
              ⚠️ 您有商品未结算，请尽快结账！
            </div>
          )}

          <button 
            style={{ ...btnStyle, backgroundColor: '#ef4444', marginTop: '20px' }} 
            onClick={() => { setIsLoggedIn(false); setCartItemsCount(0); }}
          >
            安全退出
          </button>
        </div>
      ) : (
        // 未登录界面
        <div>
          <h2>🔐 访客通道</h2>
          <p style={{ color: '#64748b' }}>您当前尚未登录，请点击下方按钮进行安全验证。</p>
          <button style={btnStyle} onClick={handleLogin}>
            立即登录
          </button>
        </div>
      )}
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. 点击“立即登录”，观察整页加载效果（If-Else 语句）。
2. 加载完毕后，界面无缝切换到登录成功状态（三元运算符二选一）。
3. 试着点击“添加商品”和“清空购物车”按钮，观察黄色提示框如何动态地出现、消失，且在数量为 0 时完美隐藏，不留下脏数据（逻辑与 `&&` 配合大于 0 判断）。

---

## 明天可以学什么？

现在，我们已经学会了如何根据状态“展示或隐藏”一整块或局部内容。

但还有一种极高频的场景：
在真实的页面中，我们常常需要从后台获取一个**商品列表、新闻列表或用户列表数组**，然后将这个数组里的每一条数据批量、循环地渲染到页面上。

明天我们来攻克：**第 8 天：React 中的列表渲染（List Rendering）**
