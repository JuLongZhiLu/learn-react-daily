## 第 6 天：React 中的事件处理（Event Handling）

在前几天中，我们已经在代码里默默使用过类似 `onClick={() => setLikes(likes + 1)}` 这样的代码了。这就是 React 的**事件处理**。

网页之所以能跟人交互，全靠各种事件：点击按钮、输入文字、滚动页面、提交表单等。今天，我们一起来理清 React 处理事件的规则，以及避开初学者极易踩中的几个核心陷阱。

---

## React 与原生 HTML 事件的三大区别

React 的事件系统和原生的 HTML 事件非常像，但有三个极其关键的区别：

### 1. 命名必须采用“驼峰命名法”（CamelCase）
在 HTML 中，事件名全是小写的；而在 React 中，必须使用驼峰命名法。
* `onclick` ➡️ **`onClick`**
* `onchange` ➡️ **`onChange`**
* `onsubmit` ➡️ **`onSubmit`**
* `onmouseover` ➡️ **`onMouseOver`**

### 2. 传递的是“函数本身”，而不是“字符串”
* **原生 HTML 写法**（传递字符串）：
  ```html
  <button onclick="handleClick()">点击</button>
  ```
* **React 写法**（大括号内直接传入**函数名/函数引用**）：
  ```jsx
  <button onClick={handleClick}>点击</button>
  ```

### 3. 阻止默认行为必须显式调用 `preventDefault()`
在原生 HTML 中，如果你想阻止链接跳转或表单提交，可以直接 `return false`。但在 React 中，你必须显式调用事件对象上的 `preventDefault()` 方法（后面我们会写到）。

---

## ⚠️ 核心陷阱：函数后面，到底带不带括号 `()`？

这是所有 React 新手都犯过、甚至摔得很痛的一个经典错误。

请看下面两行代码的区别：

```jsx
// A 写法：不带括号
<button onClick={handleClick}>按钮 A</button>

// B 写法：带括号
<button onClick={handleClick()}>按钮 B</button>
```

### 为什么按钮 B 会直接毁掉你的程序？
* **按钮 A（正确）**：你把 `handleClick` **这个函数整装打包**送给了 `onClick`。React 会把这个函数存起来，**只有在用户真正点击按钮时**，才会拆开礼盒执行它。
* **按钮 B（错误）**：你在 `onClick` 的大括号里直接写了 `handleClick()`。这意味着，**在页面刚刚渲染出来的一瞬间，这个函数就已经被强制执行了**！如果这个函数里面恰好有 `setState`，就会导致页面重新渲染，重新渲染又会再次强行执行该函数，从而陷入**无限死循环，直到浏览器卡死**。

### 💡 那么，如果我想传参数怎么办？
如果你必须要给函数传递参数，不能直接写 `onClick={handleClick(id)}`，而必须在外面**套一个空箭头函数**：

```jsx
// 这样写是安全的，因为外层的箭头函数在点击时才会触发执行
<button onClick={() => handleClick(id)}>传参按钮</button>
```

---

## 如何获取输入框（Input）里的值？

在处理输入框变化时，我们经常需要拿到用户实时打出来的字。这时候，我们需要借助事件对象 **`e`（event）**：

```jsx
function App() {
  const [text, setText] = useState("");

  function handleChange(e) {
    // e.target 代表当前的输入框元素
    // e.target.value 就是里面输入的内容
    setText(e.target.value);
  }

  return <input type="text" onChange={handleChange} />;
}
```

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`：

```jsx
import React, { useState } from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  const [inputValue, setInputValue] = useState('');
  const [submittedName, setSubmittedName] = useState('');

  // 1. 处理输入框实时变化的事件
  function handleInputChange(e) {
    setInputValue(e.target.value);
  }

  // 2. 处理表单提交的事件
  function handleFormSubmit(e) {
    // 阻止表单提交导致页面刷新的默认行为！
    e.preventDefault();
    
    if (inputValue.trim() === '') {
      alert('名字不能为空哦！');
      return;
    }
    
    setSubmittedName(inputValue);
    setInputValue(''); // 提交后清空输入框
  }

  // 样式定义
  const boxStyle = {
    border: '1px solid #e2e8f0',
    borderRadius: '12px',
    padding: '24px',
    maxWidth: '400px',
    margin: '30px auto',
    boxShadow: '0 4px 6px -1px rgba(0,0,0,0.1)',
    fontFamily: 'sans-serif'
  };

  const inputStyle = {
    width: '100%',
    padding: '10px',
    borderRadius: '6px',
    border: '1px solid #cbd5e1',
    boxSizing: 'border-box',
    marginBottom: '12px'
  };

  const btnStyle = {
    backgroundColor: '#6366f1',
    color: 'white',
    border: 'none',
    width: '100%',
    padding: '10px',
    borderRadius: '6px',
    cursor: 'pointer',
    fontWeight: 'bold'
  };

  return (
    <div style={boxStyle}>
      <h2>📝 用户登记表</h2>
      
      {/* 绑定表单提交事件 */}
      <form onSubmit={handleFormSubmit}>
        <label style={{ display: 'block', marginBottom: '8px', color: '#475569' }}>
          请输入你的英文名：
        </label>
        
        {/* 绑定 onChange 事件，实现数据的双向绑定 */}
        <input 
          style={inputStyle} 
          type="text" 
          value={inputValue}
          onChange={handleInputChange} 
          placeholder="例如：Jack"
        />

        {inputValue && (
          <p style={{ color: '#64748b', fontSize: '14px', marginTop: '-4px', marginBottom: '12px' }}>
            ✍️ 正在输入：{inputValue}
          </p>
        )}

        <button style={btnStyle} type="submit">确认登记</button>
      </form>

      {/* 如果提交成功，展示欢迎词 */}
      {submittedName && (
        <div style={{ marginTop: '20px', padding: '12px', backgroundColor: '#ecfdf5', borderRadius: '6px', border: '1px solid #10b981' }}>
          <p style={{ color: '#065f46', margin: 0, fontWeight: 'bold' }}>
            🎉 欢迎你，{submittedName}！登记成功。
          </p>
        </div>
      )}
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. 在输入框里打字，观察下方“正在输入”的文字如何实现实时同步。
2. 点击“确认登记”按钮，观察它是如何阻止页面刷新，并把名字成功打印到下方的绿色横幅中。

---

## 明天可以学什么？

在刚才的练习中，我们写了这样一行代码：
`{submittedName && <div ...>欢迎你</div>}`

这代表“只有当 `submittedName` 有值时，才显示欢迎横幅”。这在 React 中被称为**条件渲染**。

明天我们来详细学习，如何在 React 中优雅地根据不同的数据状态展示不同的界面：**第 7 天：React 中的条件渲染（Conditional Rendering）**
