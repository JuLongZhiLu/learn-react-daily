## 第 9 天：认识 useEffect（如何从服务器获取数据）

在前 8 天里，我们写的 React 项目，所有的数据都是我们在本地代码里写死的。

但在真实的网页中，数据必须是“活的”。我们需要在页面加载时，**从真实的后台服务器获取（Fetch）数据**，然后再用我们昨天学的列表渲染展示出来。

那么问题来了：我们该把“获取数据”的代码写在组件的什么地方？

今天，我们要攻克 React 学习之路上公认最难理解、也最重要的一道关卡：**`useEffect` 锦囊（Hook）**。

---

## 什么是“副作用”？为什么不能直接写在组件里？

在开始学语法前，我们先来理解一个概念：**副作用（Side Effect）**。

### 什么是副作用？
在 React 的设计哲学里，一个组件的本职工作是非常纯粹的：**输入数据（Props/State），输出界面（JSX）**。它应该是一个干净、快速、不干扰外部世界的“纯函数”。

但是，有些操作必须要和**外部世界**打交道，这些就被称为**“副作用”**。比如：
1. **从后台服务器获取数据（发送 Fetch / Axios 请求）**。
2. **手动修改浏览器的标题（`document.title = ...`）**。
3. **设置一个定时器（`setInterval` / `setTimeout`）**。

### ❌ 为什么不能直接写在组件里？
请看下面这段极易写出来的错误代码：

```jsx
function App() {
  const [data, setData] = useState(null);

  // ❌ 极其严重的错误：每次渲染都会执行 fetch！
  fetch('https://api.com/users')
    .then(res => res.json())
    .then(result => {
      setData(result); // 更改 State 会触发重新渲染！
    });

  return <div>数据加载完毕</div>;
}
```

**为什么这会直接导致浏览器卡死？**
1. 页面第一次加载，组件执行。
2. 遇到 `fetch`，向服务器发请求。
3. 请求成功，调用 `setData(result)` 改变了 State。
4. **State 变了，React 强制组件重新渲染（重新跑一遍 App 函数）**。
5. 重新跑 App 函数，**又遇到了 `fetch`**！
6. 再次发请求 ➡️ 再次修改 State ➡️ 再次重新渲染……
7. **无限死循环，直到页面卡死。**

---

## `useEffect` 是如何拯救我们的？

`useEffect` 的核心魔力在于：**它可以把里面的“副作用代码”隔离出来，等页面渲染完毕后，在特定的时机才去执行。**

### `useEffect` 的基本语法：

```jsx
import React, { useEffect } from 'react';

useEffect(() => {
  // 这里写你的副作用代码（比如获取数据）
}, [ 依赖数组 ]);
```

### 依赖数组的“三种姿势”（极其关键）：

`useEffect` 第二个参数是一个数组，称为**“依赖数组”**。它决定了你的副作用代码何时被触发：

#### 姿势一：传空数组 `[]`（最常用，本章重点）
```jsx
useEffect(() => {
  // 只会在页面“第一次加载完毕”后，执行【唯一一次】。
  // 以后组件怎么重新渲染，它都不会再执行。
  // ➡️ 这正是我们【从服务器获取初始化数据】的完美时机！
}, []);
```

#### 姿势二：不传第二个参数
```jsx
useEffect(() => {
  // 页面第一次加载执行，且组件只要有任何 State 改变，它就会跟着执行。
  // ➡️ 极少使用，容易导致性能问题。
});
```

#### 姿势三：数组里有变量 `[count]`
```jsx
useEffect(() => {
  // 页面第一次加载执行，且只有当 count 变量发生改变时，它才会重新执行。
  // ➡️ 适合写“只有当某个条件改变时才去发请求”的逻辑（比如：翻页、搜索框打字）。
}, [count]);
```

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`，来体验从真实的互联网 API 获取数据：

```jsx
import React, { useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  // 1. 声明数据状态
  const [users, setUsers] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  // 2. 使用 useEffect 隔离副作用，只在页面第一次加载后获取数据
  useEffect(() => {
    // 真实的免费测试 API（获取用户列表）
    fetch('https://jsonplaceholder.typicode.com/users')
      .then((response) => {
        if (!response.ok) {
          throw new Error('网络请求似乎出问题了...');
        }
        return response.json();
      })
      .then((data) => {
        setUsers(data);        // 存入 State
        setIsLoading(false);    // 关闭加载动画
      })
      .catch((err) => {
        setError(err.message);  // 记录错误
        setIsLoading(false);    // 关闭加载动画
      });
  }, []); // 💡 核心：空数组 [] 保证这辈子只执行一次网络请求！

  // 样式定义
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

  const userCardStyle = {
    padding: '12px',
    backgroundColor: '#f8fafc',
    borderRadius: '8px',
    marginBottom: '8px',
    border: '1px solid #e2e8f0'
  };

  // 3. 条件渲染：正在加载中
  if (isLoading) {
    return (
      <div style={boxStyle}>
        <h3 style={{ textAlign: 'center' }}>⏳ 正在加载用户数据...</h3>
      </div>
    );
  }

  // 4. 条件渲染：请求失败
  if (error) {
    return (
      <div style={boxStyle}>
        <h3 style={{ color: '#ef4444' }}>❌ 出错了：{error}</h3>
        <p>请检查您的网络连接并刷新页面重试。</p>
      </div>
    );
  }

  // 5. 渲染正常的用户列表
  return (
    <div style={boxStyle}>
      <h2 style={{ marginBottom: '16px', color: '#1e293b' }}>👥 团队成员列表</h2>
      <p style={{ color: '#64748b', fontSize: '14px', marginBottom: '20px' }}>数据已实时同步自线上测试 API 接口：</p>

      {/* 列表渲染：用 map 循环用户数组 */}
      {users.map((user) => (
        <div key={user.id} style={userCardStyle}>
          <strong style={{ color: '#0f172a' }}>{user.name}</strong>
          <div style={{ fontSize: '13px', color: '#475569', marginTop: '4px' }}>
            📧 邮箱：{user.email} <br />
            🏢 公司：{user.company.name}
          </div>
        </div>
      ))}
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. 页面刚刚出现时，你会短暂看到“⏳ 正在加载用户数据...”（条件渲染-加载状态）。
2. 网络请求成功后，页面无缝切换，并把你从**真实互联网数据库中拉取的 10 个用户**，以精美卡片列表的形式打印了出来（列表渲染-渲染数据）。
3. 这个练习完美地把我们过去几天学过的 **State、条件渲染、列表渲染、网络请求** 融会贯通在了一起！

---

## 明天可以学什么？

恭喜你！今天你已经迈过了 React 学习之路上最高、最险的一座大山。

现在，我们已经掌握了几乎所有最基础的核心概念。但是在开发大型应用时，你往往会写出好几十个甚至上百个组件。
当组件变得越来越多时，我们该**如何规范地组织我们的文件结构**？一个合格的 React 生产级项目到底长什么样？

明天我们来学一个极其接地气、对找工作/写项目非常有帮助的主题：**第 10 天：实战与工程化（React 项目结构到底该怎么建？）**
