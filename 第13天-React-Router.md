## 第 13 天：React Router——构建多页面的单页应用（SPA）

在过去的 12 天里，我们写的应用无论逻辑多么复杂，浏览器地址栏里的网址（URL）永远是固定不变的（比如一直停留在 `http://localhost:5173/`）。

但在真实的网站中，我们必定会有多个“页面”：
* 访问 `/` 显示**首页**。
* 访问 `/about` 显示**关于我们**。
* 访问 `/user/jack` 显示**用户详情页**。

传统的网站跳转页面，需要向服务器重新请求一个全新的 HTML 文件，这会导致浏览器出现瞬间的“白屏”和刷新。

而 React 采用的是 **SPA（单页应用）** 架构：整个网站其实**只有一个 `index.html` 文件**。当用户点击跳转时，JavaScript 会拦截跳转，悄悄把旧的组件卸载、把新的组件挂载上去，并更新地址栏的网址，**整个过程完全不刷新浏览器**，体验极速丝滑。

在 React 生态中，掌管这一切的官方标准库就是 **React Router**（我们通常使用针对浏览器的 `react-router-dom`）。

---

## React Router 的四大核心法宝

### 1. 路由容器：`<HashRouter>` / `<BrowserRouter>`
这是整个路由系统的最外层容器，用来监听地址栏网址的变化。
* **`<BrowserRouter>`**：使用真实的 URL 路径（如 `/about`）。这是现代商业项目的标准。
* **`<HashRouter>`**：使用带 `#` 的路径（如 `/#/about`）。它的好处是**兼容性极佳**，非常适合在沙箱、演示环境或没有专门配置服务器路由的场景下运行（今天我们的练习将使用它，防止沙箱环境报错）。

### 2. 路由地图：`<Routes>` 与 `<Route>`
它们用来规划网址和组件的“配对关系”：
```jsx
<Routes>
  {/* 当网址是 /about 时，渲染 <About /> 组件 */}
  <Route path="/about" element={<About />} />
</Routes>
```

### 3. 导航连接：`<Link>`
在 React 中，我们**绝对不能**使用传统的 `<a href="/about">` 来做页面跳转，因为那会触发浏览器的整页刷新。
我们必须使用 `<Link>` 组件，它在后台拦截了点击事件，实现了**无刷新跳转**：
```jsx
// 正确做法：使用 Link 替代 a 标签
<Link to="/about">关于我们</Link>
```

### 4. 动态路由参数：`useParams`（Hook）
如果你的网址是 `/user/jack` 或 `/user/rose`，你不需要为每个人写一个路由，可以使用 **`:`** 占位符：
```jsx
<Route path="/user/:name" element={<UserProfile />} />
```
在 `UserProfile` 组件内部，你可以使用 `useParams()` 直接拿到 `:name` 对应的值（比如 `'jack'` ）。

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`，Playground 会自动帮你下载并导入外部依赖包 `react-router-dom`。

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
// 1. 导入 React Router 核心组件和 Hooks
import { 
  HashRouter as Router, // 💡 使用 HashRouter 在沙箱环境里最稳定，防止 404
  Routes, 
  Route, 
  Link, 
  useParams, 
  useNavigate 
} from 'react-router-dom';

// 页面 A：首页
function Home() {
  return (
    <div style={{ padding: '12px 0' }}>
      <h3>🏠 欢迎来到主页</h3>
      <p>这是一个基于 React Router 构建的单页应用。尝试点击上方的导航栏，注意观察**浏览器地址栏的网址**以及**页面跳转时有没有发生刷新**！</p>
    </div>
  );
}

// 页面 B：关于我们
function About() {
  return (
    <div style={{ padding: '12px 0' }}>
      <h3>ℹ️ 关于我们</h3>
      <p>我们致力于提供全网最通俗易懂的 React 入门教程，帮助新手无痛跨越学习门槛。</p>
    </div>
  );
}

// 页面 C：动态用户详情页
function UserProfile() {
  // 💡 使用 useParams 获取网址里的动态参数（例如 :username 对应的值）
  const { username } = useParams();
  const navigate = useNavigate(); // 编程式导航路由

  return (
    <div style={{ padding: '12px 0', borderTop: '2px dashed #cbd5e1', marginTop: '12px' }}>
      <h3>👤 用户主页</h3>
      <p>当前正在查看的是 <b style={{ color: '#6366f1', fontSize: '18px' }}>{username}</b> 的专属空间。</p>
      
      {/* 编程式导航：点击按钮用 JS 强制跳转回首页 */}
      <button 
        style={{ backgroundColor: '#f1f5f9', border: '1px solid #cbd5e1', padding: '6px 12px', borderRadius: '4px', cursor: 'pointer' }}
        onClick={() => navigate('/')}
      >
        ⬅️ 返回首页
      </button>
    </div>
  );
}

// 主组件：路由大管家
function App() {
  // 样式定义
  const containerStyle = {
    padding: '24px',
    maxWidth: '450px',
    margin: '30px auto',
    borderRadius: '16px',
    fontFamily: 'sans-serif',
    border: '1px solid #cbd5e1',
    boxShadow: '0 10px 15px -3px rgba(0,0,0,0.1)',
    backgroundColor: '#939393ff'
  };

  const navStyle = {
    display: 'flex',
    gap: '12px',
    paddingBottom: '16px',
    borderBottom: '1px solid #e2e8f0',
    marginBottom: '16px'
  };

  const linkStyle = {
    textDecoration: 'none',
    color: '#6366f1',
    fontWeight: 'bold'
  };

  return (
    // 2. 最外层用 Router 容器包起来
    <Router>
      <div style={containerStyle}>
        <h2>🚇 React Router 导航演示</h2>

        {/* 导航栏：必须使用 Link，绝对不要写 a 标签！ */}
        <nav style={navStyle}>
          <Link style={linkStyle} to="/">首页</Link>
          <Link style={linkStyle} to="/about">关于</Link>
          <Link style={linkStyle} to="/user/Jack">用户:Jack</Link>
          <Link style={linkStyle} to="/user/Rose">用户:Rose</Link>
        </nav>

        {/* 3. 路由地图：决定不同的网址渲染哪个组件 */}
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          {/* 💡 动态路由占位符 :username */}
          <Route path="/user/:username" element={<UserProfile />} />
        </Routes>
      </div>
    </Router>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. 点击“关于”，你会发现内容瞬间切换，且**页面完全没有重新加载的转圈动画**。
2. 点击“用户:Jack”和“用户:Rose”，观察内容如何根据网址上的名字（参数）进行动态呈现。
3. 点击用户主页里的“返回首页”按钮，体验如何用 JS 代码（`useNavigate`）强制让页面发生跳转。

---

## 明天可以学什么？

到今天为止，我们写的所有交互和路由都是针对纯 JavaScript 的。

但在今天的商业前端工程中，**TypeScript（TS）** 已经是绝对的事实标准（绝大部分大厂和中大型项目都强制要求使用 TS ）。
用 TS 写 React，能帮我们在写代码的过程中就提前拦截 90% 以上因拼写错误、类型传错而导致的低级 Bug。

明天我们来学一个极具含金量、帮你完成职业跨越的主题：**第 14 天：TypeScript + React 实战（如何用类型保护你的 React 应用？）**
