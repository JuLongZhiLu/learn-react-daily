## 第 8 天：React 中的列表渲染（List Rendering）

在实际的网页开发中，数据往往是以**数组**的形式存在的。比如：
* 你的购物车里有一组商品对象组成的数组。
* 你的社交媒体首页有一组动态帖子对象组成的数组。
* 你的待办事项页面有一组任务标题组成的数组。

在 Vue 中，我们通过指令 `v-for` 来循环渲染列表。而在 React 中，我们依然延续**“纯 JavaScript”**的原则——不发明新语法，直接使用原生的 JS 数组方法来循环渲染列表。

今天，我们来攻克 React 列表渲染的核心魔法：**`map()` 方法**，以及一条绝对不能违反的**性能铁律**。

---

## 为什么是 `map()` 方法？

如果你有一个简单的字符串数组，想要把它变成一堆 HTML 标签，原生 JS 中的 `map()` 方法是最完美的工具。

`map()` 的核心作用是：**把一个数组里的每一项，“加工”成另一种形式，并返回一个新数组。**

```js
// 原始的数据数组
const rawData = ["苹果", "香蕉", "橙子"];

// 经过 map 转换后的 JSX 数组
const jsxArray = rawData.map((item) => <li>{item}</li>);

// 此时 jsxArray = [<li>苹果</li>, <li>香蕉</li>, <li>橙子</li>]
```

在 React 中，**大括号 `{}` 是可以直接渲染 JS 数组的**。所以我们只需要在大括号里执行 `map`，React 就会自动把这些标签排好队印到屏幕上：

```jsx
function FruitList() {
  const fruits = ["苹果", "香蕉", "橙子"];
  
  return (
    <ul>
      {/* 只要大括号里是一个 JSX 数组，React 就会自动解开并渲染出来 */}
      {fruits.map((fruit) => (
        <li>{fruit}</li>
      ))}
    </ul>
  );
}
```

---

## ⚠️ 核心铁律：每个列表项必须有唯一的 `key`！

当你按照上面的方式写完代码，打开浏览器的控制台（Console），你会看到一个鲜红的警告：

> ⚠️ **Warning: Each child in a list should have a unique "key" prop.**
> 
> （警告：列表中的每个子元素都应该有一个唯一的 "key" 属性。）

### 什么是 `key`？为什么非要加它？
在循环渲染列表时，我们必须给每一个列表的最外层标签加上一个特殊的 `key` 属性：

```jsx
// 正确写法：给每一项添加一个唯一的 id 作为 key
{todos.map((todo) => (
  <li key={todo.id}>{todo.text}</li>
))}
```

**因为 `key` 是 React 在 Virtual DOM 中的“身份证”。**

当你的数据发生增加、删除、修改、重新排序时，React 需要快速比对旧页面和新页面。
* **没有 `key`**：React 无法分清谁是谁，只能简单粗暴地把整个列表删掉，再重新渲染一遍（这非常消耗性能，且如果列表里有输入框，输入框里的字会莫名其妙错位）。
* **有了唯一 `key`**：React 能一眼认出：“哦！只是 `id` 为 3 的那一项被删除了，其他项都没变。” 于是它只会定点删除那一个 DOM 节点，其他节点保持不动。这极大地提升了渲染性能和交互的准确性。

### 💡 什么样的值能作为 `key`？
1. **数据库里的 `id`（最推荐）**：它是绝对稳定且唯一的。
2. **尽量避免使用数组的索引（`index`）作为 `key`**：因为一旦列表发生排序、筛选或删除操作，每个元素的索引（0, 1, 2...）就会发生改变。这会导致 React 比对错乱，失去 `key` 本身的意义，甚至引发诡异的 UI bug。

---

## 动手练习

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

把下面的代码复制到 `index.jsx`，来体验如何用数据驱动列表的增删：

```jsx
import React, { useState } from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  // 初始化一个任务数组作为 State
  // 每个任务都是一个对象，有唯一的 id、任务内容 text、是否完成 completed
  const [todos, setTodos] = useState([
    { id: 1, text: '学习 React JSX 语法', completed: true },
    { id: 2, text: '理解 Props 和 State 的区别', completed: true },
    { id: 3, text: '攻克列表渲染 map() 与 key', completed: false }
  ]);

  const [newTodoText, setNewTodoText] = useState('');

  // 1. 添加新任务
  function handleAddTodo(e) {
    e.preventDefault();
    if (newTodoText.trim() === '') return;

    // 组装新任务对象
    const newTodo = {
      id: Date.now(), // 用时间戳作为简易的唯一 id
      text: newTodoText,
      completed: false
    };

    // 把新任务塞进数组（注意：React 推荐用解构创建新数组，不能直接 push）
    setTodos([...todos, newTodo]);
    setNewTodoText(''); // 清空输入框
  }

  // 2. 删除任务
  function handleDeleteTodo(id) {
    // 使用 filter 过滤掉对应 id 的任务，生成新数组
    const updatedTodos = todos.filter(todo => todo.id !== id);
    setTodos(updatedTodos);
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

  const todoItemStyle = {
    display: 'flex',
    justifyContent: 'space-between',
    alignItems: 'center',
    padding: '12px',
    backgroundColor: '#f8fafc',
    borderRadius: '8px',
    marginBottom: '8px',
    border: '1px solid #e2e8f0'
  };

  return (
    <div style={boxStyle}>
      <h2>📅 我的待办清单</h2>

      {/* 输入区域 */}
      <form onSubmit={handleAddTodo} style={{ display: 'flex', marginBottom: '16px' }}>
        <input 
          style={{ flex: 1, padding: '8px', borderRadius: '6px 0 0 6px', border: '1px solid #cbd5e1' }}
          type="text" 
          value={newTodoText}
          onChange={(e) => setNewTodoText(e.target.value)}
          placeholder="添加新任务..."
        />
        <button style={{ backgroundColor: '#6366f1', color: 'white', border: 'none', padding: '0 16px', borderRadius: '0 6px 6px 0', cursor: 'pointer' }} type="submit">
          添加
        </button>
      </form>

      {/* --- 列表渲染的核心区域 --- */}
      {todos.length === 0 ? (
        <p style={{ textAlign: 'center', color: '#64748b' }}>🎉 所有任务都完成了，真棒！</p>
      ) : (
        <div>
          {/* 使用 map 循环渲染 todos 数组 */}
          {todos.map((todo) => (
            // 铁律：最外层标签必须绑定唯一的 key
            <div key={todo.id} style={todoItemStyle}>
              <span style={{ textDecoration: todo.completed ? 'line-through' : 'none', color: todo.completed ? '#94a3b8' : '#1e293b' }}>
                {todo.completed ? '✅' : '📌'} {todo.text}
              </span>
              
              {/* 删除按钮 */}
              <button 
                style={{ backgroundColor: '#ef4444', color: 'white', border: 'none', padding: '4px 8px', borderRadius: '4px', cursor: 'pointer', fontSize: '12px' }}
                onClick={() => handleDeleteTodo(todo.id)}
              >
                删除
              </button>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. 输入任务并点击“添加”，观察新节点如何渲染出来。
2. 点击任务右侧的“删除”，观察对应的任务节点如何平滑地从页面上消失。
3. 想一想：在 `todos.map(...)` 内部，我们是如何优雅地将每一条数据组装成 HTML 结构的。

---

## 明天可以学什么？

到今天为止，我们写的项目，所有的数据都是我们在本地代码里写死的。

但在真实的网页中，数据必须是“活的”。我们需要在页面加载时，**从真实的后台服务器获取（Fetch）数据**，然后再用今天学的列表渲染把它们展示出来。

我们不能把网络请求直接写在组件函数里（那会导致每次渲染都重复发请求）。在 React 中，我们需要一个极其特殊的 Hook，用来管理各种“副作用”操作。

明天我们来攻克 React 中最深的一道关卡：**第 9 天：认识 useEffect（如何从服务器获取数据）**
